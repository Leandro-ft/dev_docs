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
  * [x] AllocationChart 4/38, 
  * [x] AllocationManager 5/38,
### tarde
**sietea**
- crear test para 
  * [x] AssetMasterTable 6/,
  * [x] AssetsDetailClpNotClpGroupedByAttributesAndCustodian,
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
  * [x] BenchmarkSeries (este son como 9 en uno)
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
  * [] RvDataTable
  * [] TableEditor´
  * [] TaxReport
- 31/36 page_element_tests




## penditnes
- [] Ansiblev2: restore users

### Obligatorios
- Agregar al restore la opción de restaurar los usuarios

### No obligatorios o ideas
- [] Controlar request múltiple por clicks múltiples (simulus => Lo ve Felipe)
- [x] Logs en view (implementación complicada -> log task worker api => query a semaphore)
- [] Hacer semaphoreService una entidad global
- [] Cola de deploys por clientes
- [] agregar test de comprobacion para evitar crear cualquier submodulo de high_charts_to_image_v2 sin tener un test de ello
- [x] Hacer autorizaciones controladas, (identificables y escalables)
- [x] Arreglar logs de restore_client_environment (como la arquitectura cambio, algunos logs dicen cosas que ya no son ciertas)
- [] Refactorizar arquitectura de semaphore
- [] agregar entorno de pruebas para restore_client_environment en ansibleV2

## Recordatorios post desarrollo
**client manager internal request**
- [x] Quitar `config.log_level :warn` en development.rb
- [] Quitar el semaphore_api_token hardcodeado
  - semaphore_service debe tener un token de autenticación de usuario de semaphore => crear un usuario con permisos de admin y generar un token (no expirable)

**sietea highcharts**
- [] quitar autoload path test/support de development.rb

