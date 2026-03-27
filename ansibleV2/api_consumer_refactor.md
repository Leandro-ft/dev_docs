# Antiguo

## arbol de directorios
```bash
/api_consumer
  - /clients
  - /out
  - /vars
  ...scripts
```

## Task log worker

### clases que intervienen
- TaskLogService
  * logica de creacion de logs
  * logica de integridad de archivos y task logs

- SemaphoreClient
  * contiene todos los metodos para hacer request a la api de semaphore

### archivos generados o modificados
- archivos de logs:  se genera uno por cada `semaphore_project` detectado
- metodo para evitar logs redundantes: 
  * se crea un archivo para guardar hasehes basado en cada task que luego se puede consultar, ya que si el hash cambia significa que la task cambió y por ende vale la pena hacer otro log
  * se crea uno por `semaphore_project` detecado
## flujo de creacion de logs

1. hace peticion a `semaphore` para buscar las ultimas 200 `semaphore task` de semaphore segun los projects_id definido en la `task_log_worker_config.json`

2. crea un esquema para estructurar bien que se incluira en el json string (el log)

3. comprueba redundancia
  * se carga en memoria el archivo de ids del proyecto al que pertenece la task que se esta evaluando si es que aun no se ha hecho
  * convierte el esquema a un hash, se busca el hash dentro del archivo de ids cargado en memoria
  * si no se encuentra significa que tiene cambios no registrados y por lo tanto se da el visto bueno

4. se crea el log en caso de que no sea redundante y se guarda en un archivo

______________________________________________________________________________________________

# Refactorizacion

## arbol de directorios
```bash
/api_consumer
  - /clients
  - /services -new
  - /outs
  - /vars
  - /utils
  ...scripts
```

## Task log worker

### clases que intervienen
- TaskLogService `updated`
  * logica de creacion de logs
  * logica de integridad de archivos

- TaskStateTracker `new`
  * logica de integridad de task logs

- SemaphoreClient
  * contiene todos los metodos para hacer request a la api de semaphore

### archivos generados o modificados
- archivos de logs:  se genera uno por cada `semaphore_project` detectado
- metodo para evitar logs redundantes: `updated`
  * se guarda el ultimo estado en una base de datos `sqlite`
    - esto evita la carga en memoria
    - consultas mas rapidas
  * solo se guarda
   - esto mejora es escalado en almacenamiento
   - se puede elegir que atributos tomar en consideracion para la generacion de logs

## flujo de creacion de logs

1. hace peticion a `semaphore` para buscar las ultimas 200 `semaphore task` de todos los proyectos que encuntra `updated`

2. crea un esquema para estructurar bien que se incluira en el json string (el log)

3. comprueba redundancia `updated`
  * se realiza una sola consulta para las `200` tasks de una sola vez, mejorando la velocidad y en caso de fallo se hace un `rollback`
  * retorna un array con los booleanos que indican uno por uno si el log es redundante o no

4. se crea el log en caso de que no sea redundante y se guarda en un archivo