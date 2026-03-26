rama AnsibleV2 gatekeeper : `DEV-3898/restore-client-environment-security-improvement`
rama client-manager : `DEV-3898/internal-request-module`

# Flujo antiguo:

### modelos de client-manager
```diff
+ DbSyncRequest
```

### En restore_client_environment.sh
- ejecucion de `restore_client_environment.sh`
- ejecuta `3` funciones principales : {
	* 1: function de logeo
	* 2: function de creacon de `DbSyncRequest`
    http_request => POST `api/v1/db_requests`
    ```json
    {
			"cleint_name" : "demo",
			"project_name" : "sietea"
		}
    ```
    ### en client-manager

    ### de vuelta en restore_client_environment.sh
	* 3: function de espera de aprobacion de `DbSyncRequest` 
    http_request => GET `api/v1/db_sync_requests/{id}`

    ### en client manager
    este verifica 2 cosas
      - el usuario que pregunta es a quien pertenece la request que consulta?
      - la request a sido autorizada por un admin
    si todo esta bien responde:
    ```json
    {
			"id" : "1",
			"otp_code" : "132499"
		}
    ```
}

- con el otp obtenido envia un json en vez de un commando por ssh al servidor que ejecuta las acciones
- el servidor ejecuta un force_command `gatekeeper.sh`
- ejemplo de lo que se envia
```json
{
  "action" : "get_temp_s3cfg",
  "otp_code" : "132499",
  "request_id": "1",
  "client_name": "demo"
} 
```

### En gatekeeper.sh
- lee la accion, en caso de ser `get_temp_s3cfg` hace lo siguiente
  - http_request => POST `db_sync_request/{request_id}/verify_otp`
  ```json
  {
    "otp_token": "143245",
    "client_name": "$CLIENT_NAME"
  }
  ```
  - ejemplo de resupesta:
  ```json
  {
    "authtenticated": true
  }
  ```
- gatekeeper ejecuta la accion



# Nuevo flujo

### modelos de client-manager
```diff
- DbSyncRequest
+ GatekeeperRequest
+ GatekeeperAction
+ todos los archivos que y referencias cambiadas de db_sync_request a gatekeeper_request
```

### En restore_client_environment.sh
- ejecuta `2` funciones principales : {
	* function de logeo: para logearse
	* function para crear y esperar la aprobacion de la `GatekeeperRequest`: 
    - envia POST `api/v1/gatekeeper_request`:
    ```json
      {
        "scope": "download_backup",
        "actions": {
          "get_temp_s3cfg": {
            "args": {
              "client_name": "demo",
              "project_name": "sietea"
            }
          }
        }
      }
    ```

### en client-manager
- recibe peticion
- crea GatekeeperRequest
- por cada action crea un GatekeeperAction
- responde

### de vuelta en restore_client_environment.sh

- de vuelta a 
  * funcion de post and audit request
    - recibe la respuesta
    - ejemplo de respuesta
    ```json
    {
      "request_id": 1
    }
    ``` 
    - ahora se queda en un bucle infinito esperando que sea aprobada la request
    - consulta `GET api/v1/gatekeeper_request/{request_id}` hasta que vea que a sido authorizada o rechaza
    - ejemplo de respuesta de request authorizada
    ```json
    {
      "id" : 1,
      "request_status" : "authorized",
      "opaque_token" : "a random 32 bit string",
      "protected" : "true",
    }
    ```
    }

- el script saca el `request_id`, `opaque token` y los envia en un json a gatekeeper


### En gatekeeper.sh:
- evalua el json recibido
- gatekeeper ejecuta una funcion `wrapper` q `protected_wrapper` dependiendo de si encuentra la key `protcted` con el valor `true` 


#### Wrappers
* wrapper
  - evalua accion y simplemente ejecuta, confia en authorized_keys

* proctected_wrapper
  - este wrapper antes de evaluar y ejecutar `action` necesita hacer 2 cosas
    * hacer un claim de la accion de la request a la que apunta el `opaque_token` que espera en el json recibido
    * luego de hacer claim espera recibir los `args` de esa accion en especifico
  - para esto se comunica con `client-manager`
  http_request => `api/v1/gatekeeper_requests/{request_id}/claim_action`
  ```json
  {
    "opaque_token":"5e9f4d31-170b-45d7-b142-8bf356180c4c",
    "action_scope":"get_temp_s3cfg"
  }
  ```

  ### en client-manager
  - verifica que el opaque token efectivamente pertenezca a la `Gatekeeper` que se esta consultando
  - busca que el `Gatekeeper.scope` contenga una accion llamada igual que `action_scope` recibido
  - verifica si aun no ha sido reclamada
  - la reclama y retorna lo siguiente
  ```json
  {
    "id" : "{request_id}",
      "args" : {
        "client_name" : "demo",
        "project_name" : "sietea"
      }
  }
  ```
  
  # de vuelta en gatekeeper
  - ahora con la `action` y los `args` evalua y ejecuta la accion

# Beneficios de este enfoque
- Se pueden agregar todas las acciones que quieran
- es completamente seguro, para que falle client-manager, su db y gatekeeper deberian verse comprometidos al mismo tiempo
- client-manager sabe absolutamente cada cosa que hace cada script paso a paso
- es facil saber desde clint-manager en que parte fallo en caso de un proceso largo en un futuro
- crea un modelo unificado entre las 3 maquinas [maquina del desarrollador, client-manager y la maquina donde esta el gatekeeper.sh] en vez de que cada uno haga las cosas a su manera provocando desorden y errores dificiles de predecir y debugear
