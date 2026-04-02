## Configuracion
- [x] dar permiso de ejecucion sobre gatekeeper.sh

- [] configurar las variables de entorno de conexion correctamente
  `export CLIENT_MANAGER_ENDPOINT="http://sidekiq:3000"` al inicio de gatekeeper.sh

- [] configurar environment config en client-manager 
  * rails tiene protegion contra DNS rebinding por lo que se necesita especificar en el archivo el nombre de host para que este pueda ser utilizado
  `config.hosts << "sidekiq"` en config/environments/development.rb -> client-manager

