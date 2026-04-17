# Daily Tasks

## 24-02-2026
### Mañana
- [x] Agregar paginación visual a todo
- [x] Corregir orden
- [x] Cambiar index a internal_requests
- [x] Testear flujo completo db.sh -> client-manager -> db.sh -> gatekeeper.sh
### Tarde
- Crear servicio semaphore UI
- Probar el uso de api token vs sesión cookies para autenticarse en semaphore

## 25-02-2026
### Mañana
- [x] Agregando multi campos para Branch
- [x] Agregando validación personalizada para formatos y otras condiciones
### Tarde
- [x] Ajustar SemaphoreService y controlador sync
- Solución problemas de flujo completo de client-manager y semaphore en local (pendiente)

## 26-02-2026
### Mañana
- [x] Terminar y testear de flujo completo de client-manager y semaphore en local
### Tarde
- [x] Semaphore task turbo frame
- Revisar db.sh sietea y adaptar

## 27-02-2026
### Mañana
- [x] Refactorizar (mejorar legibilidad y robustez)
- [x] El archivo de log no se creaba automáticamente si este no existía a la hora de escribir (arreglado)
- [x] Botón de refresh task info
### Tarde
- [x] Arreglar valor de maintain_branches
- [x] Logs
- Adaptar el db.sh a restore_client_environment.sh

## 03-03-2026
### Mañana
- [x] Agregar obtención de otp restore_client_environment.sh (hecho-falta testing)
- [x] Observar comandos por ssh en restore_client_environment.sh
- [x] Crear y configurar contenedor semaphoreui con user deploy en Docker
### Tarde
- [x] Agregar obtención de otp restore_client_environment.sh
- [x] Crear wrapper de gatekeeper.sh
- [x] Configurar gatekeeper OTP
- [ ] Configurar gatekeeper .s3cfg expirable (falta rol)
- [x] Configurar forcé command
- [x] Verificar conexión entre las 3 partes
- [x] Test opción de restauración 1

## 04-03-2026
### Mañana
- [x] Test opción de restauración 2
### Tarde
- [x] Retocar permisos de client-manager
- [x] Informe de propuestas a daniel
### Pendientes
- [] Configurar gatekeeper .s3cfg expirable (falta rol)

## 09-03-2026
- Refactorizacion: mejora de escalabilidad de task_log_worker en uso de RAM y almacenamiento
### Mañana
- Se refactorizó el servicio task_log_worker:
  - Separación de clases en `/api_consumer/services` y `/api_consumer/clients`
  - Se agregó el uso de un logger global para auditar al task_log_worker de forma precisa
  - Mejora de escalabilidad de uso de RAM guardando y consultando ultimos estados de forma masiva con `sqlite` en vez de operaciones separadas con el uso de un id_file para evitar logs duplicados
  - Mejora escalabilidad de almacenamiento guardando información mínima observable en sqlite en vez de un hash generado a partir del objeto de tipo `dict`
- Se descompuso api_consumer en docker-compose como un servicio base el cual heredaran otros servicios que ejecutaran scripts debido a que todos usan la misma config
- Se agregó un nuevo servicio/script `semaphore_status` el cual al ejecutarlo mostrara todo el arbol jerárquico de semaphore actual `projects => task_templates => playbooks` relacionando `name` e `id` de cada cosa
### Tarde
- Se ha un contenedor para crear backups y restaurar proyectos de semaphore a través de una interfaz interactiva

## 10-03-2026
### Mañana
**Sietea**
- Comenzando desarrollo de refactor otp => opaque token:
  - Cambiar modelo db_sync_request y limpiar modelo user
  - Crear migraciones para añadir campos a db_sync_request y quitar campos de user
  - Modificar controladores y vistas de db_sync_request para que se ajusten al nuevo flujo
- Highchart:
  - Testeando highchart actual y analizando flujo actual
  - Actualizando y testeando highchart con version mas actual de node
### Tarde
- (No hay tareas registradas para esta tarde)

## 11-03-2026
### Mañana
**Sietea**
- [x] Estudiando flujo de highcharts
- [x] Solucionando parsing puppeteer will_be_replaced
- [x] Solucionando svg black background (debería ser transparent)
- [x] Revisando otros statement_pages para buscar problemas de renderizado
- [x] Generando scheme de db para poder crear mocks para testing (consultar casi cualquier statement toma como de 1 a 30 mins)
**Ansiblev2**
- Reescritura y documentación de nuevo flujo reforzado y escalable
### Tarde
- Creando mock para testear PageStatements en sietea
- Documentando refactorización de db.sh ahora gatekeeper flow
- Realizando migraciones para gatekeeper flow

## 12-03-2026
### Mañana
**Sietea**
- He arreglado problemas con la transparencia adaptandome a el flujo ya establecido en el pasado
- he probado todos los PDFs que no requieren de CustomCompleteStatementCOnfig
- He detectado problemas y posibles problemas en los PDFs revisados
**Client-manager**
- ordenando migraciones vistas y controladores para adaptarlos a la nueva estructura de gatekeeper_request (antes db_sync_request)
### Tarde
**client-manager**
- adapte las migraciones relacionadas con gatekeeper e internal_request_view
**sietea**
- creando reporte para felipe de errores y observaciones encontradas tras actualizar highcharts

## 13-03-2026 (viernes)
### Mañana
**client-manager**
- [x] creando sistema de scope-scheme para definir que se puede y no hacer en las gatekeeper_request
- [x] testing con postman create, show y claim_action
- [x] testing con sh scripts
### Tarde
**sietea**
- [x] terminar de revisar y comparar highcharts angituos vs nuevos
 `highcharts revisados, (4 problemas encontrados/solucionados_o_parcheados en total)`

## 16-03-2026 (lunes)
### Mañana
**sietea**
- [x] testear por componente individual
- [x] investigar StatementCompleteCustomConfig
### Tarde
**ansibleV2**
- [x] actualizar script sh al nuevo flujo de client-manager
- [x] investigar formas de testear sietea pdfs
- [x] arreglar problema de gatekeeper_request_controller#create

## 17-03-2026
### Mañana
**Dev_docs**
- [x] documentar refactorizacion de gatekeeper_flow
- [x] actualizar generacion de logs gatekeeper_request
### tarde
- [x] hacer commits de client-manager y ansibleV2, refactorizacion completa
- [] test unitarios de high_charts_image_v2

## 18-03-2026
### Mañana
**sietea**
- [x] create test for bar.rb
- [x] solucoinar errores configuracion de rails para `test` environment
### Tarde
**sietea**
- [x] crear test unitarios para todos los elementos de high_charts_image_v2
- [x] observar errores resultado del testing y buscar causa y solucion para documentar

## 19-03-2026
### mañana
**sietea**
- [x] configuracion base completa de entorno y arquitectura de prueba para sietea
### tarde
**sietea**
- [x] investigar flujo y como testear page_elements

## 20-03-2026
### mañana
**client-manager**
- [x] testear client-manager post main rebase
### tarde
**sietea**
- [x] crear primer test de page_eleent exitoso

## 23-03-2026
### mañana
**client-manager**
- [x] crear data migration para client-manager internal request resources
**sietea**
### tarde
- [x] idear alguna arquitectura que permita crear testing de page_elements

## 24-03-2026
### mañana
**sietea**
- [x] ver variables por defecto de la generacion de page_elements a travez del controlador
- [x] armar y ordenar valores por defecto para las variables observadas
- [x] configurar entorno para test de integracion
### tarde
**sietea**
- crear test de page_elements que no dependen de `data`
  * [x] GenericTable,
  * [x] GenericChart,
- crear al menos un test de un page_element que dependa de `data`
  * [x] AllocationLimitsChart
- 3/36 page_elements_tests
## 25-03-2026
### mañana
**sietea**
- crear test para 
  * [x] AllocationChart 
  * [x] AllocationManager
### tarde
**sietea**
- crear test para 
  * [x] AssetMasterTable
  * [x] AssetsDetailClpNotClpGroupedByAttributesAndCustodian
  * [x] IndexTable
- 8/36 page_element_tests

## 26-03-2026
### mañana
**sietea**
- crear test para 
  * [x] GainLoss,
  * [x] AssetsDetailGroupedByAttributesAndCustodian
  * [x] AssetsModificationTableTest
### tarde
**sietea**
- crear test para 
  * [x] AttributionChartA
  * [x] BenchmarkSeries
- 13/36 page_element_tests

## 27-03-2026
### mañana
**sietea**
- crear test para 
  * [x] CustodianAssetTable,
**dev_docs**
- creando documentos
  - [x] flujo semaphore y client-manager
  - [x] api_consumer refactor de ansibleV2
### tarde
**sietea**
-crear test para
  * [x] CustodianTable
  * [x] DetailedCostTer
  * [x] EqualityFlowTwo
  * [x] EqualityFlow
  * [x] EvolutionAllocationManager
  * [x] EvolutionAssetsDistributionElement
  * [x] FifoLotsGroupedByAttribute
- 21/36 page_element_tests

## 30-03-2026
### mañana
**sietea**
- crear test para 
  * [x] InformationRatio
  * [x] MarcowitzGraph
  * [x] MonthlyProfitabilityElement
  * [x] ProjectedCashFlowElement
  * [x] RfBondsDataTable
  * [x] RfBondsPanel
  * [x] RfSeriesManager
- añadir render cases faltantes
  * [x] AllocationChart
### tarde
**sietea**
-crear test para
  * [x] RvDataTable
  * [x] TableEditor´
  * [x] TaxReport
  * [x] TextEditor
  * [x] TopElementsElement
  * [x] TransactionsTable
  * [x] ValueAtRisk
- all page_element_tests

## 31-03-2026
### mañana
**sietea**
- agregar tasts faltantes:
  * [x] ManagerMasterTable
- page_elements testing:
  * [x] crear assert standard + limpiar
### tarde
**sietea**
- page_elements testing
  - en cada archivo de test:
    * [x] cambiar variable por inline en caso de que no se este reutilizando o modificando para mejorar la legibilidad
    * [x] eliminar comentarios redundantes 
    * [x] evaluar data reutilizable sin perjudicar la legibilidad del mock

## 01-04-2026
### mañana
**ansiblev2**
- [x] limpiar docker compose up
- [x] agregar backup inicial
### tarde
**client-manager**
- [x] migracion cambio de nombre new_bracn => add_branch
**ansiblev2**
- [x] documentar contenedores semaphore
- [x] limpiar y testear docker-compose 

## 02-04-2026
### mañana
**ansiblev2**
- [x] fix para script de restore y gatekeeper
**client-manager**
- implementar user.semaphore_api_token
  * [x] crear migracion
  * [x] modificar vista y controlador para poder editarlo
  * [x] modificar semaphoreService si es necesario
### tarde
**client-manager**
- implementar user.semaphore_api_token
  * [x] encriptar semaphore_api_token
  * [x] testear flujo completo con semaphore_api_token
- client-manager mejoras de legibilidad => {
  * [x] mejorar SemaphoreError para mostrar info mas precisa
  * [x] agregar color, o color parcial a los de semaphore_task
}

## 06-04-2026
### mañana
**client-manager**
- gatekeeper_request STI refact => {
  * [x] crear vistas nuevas
  * [x] log_activity propio para cada modelo hijo
  * [x] unificar validacion de actions
  * [x] todos comparten el mismo api_controller pero distintos MVC
  * [x] gatekeeper_controller handlers por defecto
}
### tarde
**client-manager**
- deployment_request 1-M semaphore_tasks
  * [x] crear migracion para nueva columna task_references => {
    project_id
    task_id
    created_date
  }

## 07-04-2026
### mañana
**client-manager**
- testear gatekeeper STI
  * [x] validacion dinamica 
  * [x] independencia de vistas
  * [x] logs
  * [x] validacion y getters de actions args
### tarde
**client-manager**
- deployment_request 1-M semaphore_tasks
  * [x] adaptar creacion
  * [x] modificar show
  * [x] colocar opcion de re-launch request
  * [x] actualizar logs para escuchar re-launch request

## 08-04-2026
### mañana
**client-manager**
- deployment_request 1-M semaphore_tasks
  * [x] corregir prefetching de turbo
  * [x] corregir bugs renderizado condicional
- [x] corregir quejas de rubocop
### tarde
**ansiblev2**
- [x] crear staging template

**client-manager**
- [x] actualizar semaphore service con staging template
- [x] ocultar no cargar semaphore_api_token en vista edit
- [x] solucion bug ver task 401 semaphore

## 09-04-2026
### mañana
**client-manager**
- [x] env semaphore_service config IDs
- [x] mostrar estado de la ultima task arriba del todo
- [x] arreglar name, usar Client.fintree_key_name
- [x] arreglar problema vista STI gatekeeper_request title en show
### tarde
**ansibleV2**
- [x] evitar que el usuario tenga que volver a poner contraseña en restore_client_environment.sh 
  * testing
    - [x] caso de que el archivo no exista
    - [x] caso de que falle auth
    - [x] caso de que exista y usar la password
**client-manager**
paralell task control
- [x] implementar algoritmo
- [x] implementar control de excepcion
- testing
  * bloqueo de deploy al mismo clienteÇ
  * permitir ejecutar luego de que el task bloqueante termine
  * permitir paralelos de distintos clientes
- [x] actualizar backup de semaphore_project para incluir pararell tasks

## 09-04-2026
### mañana
**client-manager**
* [x] bugs renderizado de show dinamico en internal_request
**ansiblev2**
* [x] ebcryoted credentials para client-manager
### tarde
**client-manager**
* [x] turbo-frame optimization

## 13-04-2026
### mañana
- [x] estuadiar finanza corporativa
### tarde
**client-manager**
- [x] arreglar problemas de renderizado
- [x] estado de ultima tarea en deployment_request#index

## 14-04-2026
### mañana
**client-manager**
- Deployment_requests
  * [x] coincidir client con server name
  * [x] agregar la opcion de client null para que el server sea el nombre del proyecto
### tarde
**ansiblev2**
- configurar crear y testear
  * [x] credenciales con permisos filtrados dinamicamente para s3cfg

## 15-04-2026
### mañana
**client-manager**
- Server entity
  * [x] create crud
  * [x] configure permissions
- DeploymentRequest Server implementation
  * modify create
### tarde
**client-manager**
- Server entity
  * [x] create crud
  * [x] configure permissions
- DeploymentRequest
  * [x] migration, remove client, add server and host_expr
  * [c] refact MVC
  * [x] test deploy with server

## 16-04-2026
### mañana
**client-manager**
- Server entity
  * [x] create crud
  * [x] configure permissions
- DeploymentRequest Server implementation
  * [x] modify create
### tarde
**client-manager**
- DeploymentRequest
  * [x] test deploy with server
- DeploymentRequest massive requests
  * [x] MVC hosts_expr refactor
  * [x] semaphore_service integration

## 17-04-2026
### mañana
**client-manager**
* [x] notificacion de slack para internal requests
### tarde
- [x] retuilizacion de request


## penditnes

### No obligatorios o ideas
- [] Controlar request múltiple por clicks múltiples (simulus => Lo ve Felipe)
- [x] Logs en view (implementación complicada -> log task worker api => query a semaphore)
- [] Hacer semaphoreService una entidad global
- [] Cola de deploys por clientes
- [] agregar test de comprobacion para evitar crear cualquier submodulo de high_charts_to_image_v2 sin tener un test de ello
- [x] Hacer autorizaciones controladas, (identificables y escalables)
- [x] Arreglar logs de restore_client_environment (como la arquitectura cambio, algunos logs dicen cosas que ya no son ciertas)
- [] Refactorizar arquitectura de semaphore
- [] continuando con el refactor de db_sync a gk_request transformar la configuracion que ahora es un hash en el modelo de GatekeeperRequest en un JSON en la base de datos tal como lo hace AWS, que se valide en el modelo y en el backend y que pueda ser editador directamente desde roles y permisos => admin => gatekeeper resource
- [] remplazar comunicacion de echo sin formato con comunicacion en formato JSON


## Recordatorios post desarrollo
**client manager internal request**
- [x] Quitar `config.log_level :warn` en development.rb
- [] Quitar el semaphore_api_token hardcodeado
  - semaphore_service debe tener un token de autenticación de usuario de semaphore => crear un usuario con permisos de admin y generar un token (no expirable)

**sietea highcharts**
- [] quitar autoload path test/support de development.rb

