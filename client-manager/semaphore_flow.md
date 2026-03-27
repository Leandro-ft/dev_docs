## semaphore_entity_tree context
```json
{
  "semaphore" : {
    "global_users" : [
      ...
      - usuarios reales
    ],
    "projects" : {
      "task_templates" : [
        "tasks" : [
            {
              "output": [...logs de semaphore]
            },
          ...
          - una vez se crea una tarea ya solo se puede eliminar
          - si se intenta reiniciar solo se creara una nueva tarea
          ],
      ],
      "repositories" : [],
      "group_vars" : [],
      "users" : [
        - permisos a nivel de project
        - estos no son usuarios independientes, estas basados en un usuario global de semaphore
      ]
    }
  }
}

```

## enidades que intervienen

- SemaphoreService
  * contiene metodo base para hacer requests a la api de `semaphore` y control de errores de api o conexion
    - metodo para hacer peticion abstracta
    - metodo para crear una task  `task_template` cualquier, arma el body estandar del endpoint
    - metodo para crear un `task` basado en una `semaphore_task_template` y `client_manager_model`, arma la estructura de los cli_args pasados a semaphore como `body.environment`
  * contiene un mapeo donde indica donde esta cada `task_template` en `semaphore`, que `semahore_project` y que id ya que pueden existir la misma `task_template` en `semaphore_project` distintos

- DeploymentRequest
  * contiene `request_status`, el cual es perzonalizado
  * contiene info sobre el propietario y sobre la persona que ha procesado la solicitud
  * contiene el campo `task_ref` que es lo que `SemaphoreService` usa para relacionarlo con Semaphore
  * se usa `semaphore_task_ref` en vez de algo como `task_id` porque `semaphore` permite crear varios proyectos, pueden existir una `task` con el mismo nombre e id para proyectos distintos, se guarda asi para atajar cualquier peculiaridad de Semaphore

## flujo

### creacion de Request
se valida en base a `deploy_project.yml` de `ansibleV2`
  * branch deben seguir el patron DEV-xxxx/{string}
  * almenos debe contener uno de los branch_fields `branches, new_branch, maintain_branches, dashboard_branches`
  * project name y name obligatorios

la request aparecera como `pending` en internal_requests#index y en deploy_requests#index

### Authorizacion y accion
se puede aprobar desde `#index` o `:show` del recurso

1. si se ha autorizado se actualiza `processed_by` y `request_status`

2. `SemaphoreService`
  * se ejecuta `deploy_project` y se le pasa la `DeploymentRequest`
  * arma los `cli_args`
  * arma `body`
  * hace la request
  * retorna la `task` creada en como un `hash` de ruby

3. finalmente se actualiza `processed_by` y `semaphore_task_ref`, si ocurre un error aparecera como una tarea authorizada pero huerfana, osea que no tiene una semaphore_task_ref ya sea porque fallo al vincularse o porque no se pudo crear la task

### Logs
* funcion de log dentro del respectivo modelo de {Algo}Request
* se ejecuta ante un cambio de creacion o cambios de estado





