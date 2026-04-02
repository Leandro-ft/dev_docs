## Controller
- problemas
  * [x] el rescue de `pdf_by_page_element_controller` envia codigo 200, cualquier error inesperado como un error de validacion en cualquier de los `parse_data` de culquier `page_element` resultara en errores fantasma para mecanismos autmatizados, `porfavor revisar en otros controladores`, succede un caso similar con otras respuestas, envian todas el error pero con codigo 200 en vez de 400 403 o 500
  
## Validacion
**params y data**
  * [] si hay un campo que solo deberia tener ciertos valores, este fallara por algun comportamiento inesperado en vez de validar e informar sobre los campos disponibles o esperados
  * [] lo mismo si se necesita que algun atributo especifico se encuentre en `param` o `data`, si no está el codigo continua e intenta hacer operaciones imposibles lanzando errores de todo tipo los cuales no son utiles ni claros
  * si se busca enviar data vacio siendo este un array la funcion lo detecta como que no se ha enviado nada y por lo tanto se ejecuta `self.data`, de todas formas, es mas un comportamiento inesperado que un problema real, pero ahi está

## Carga de modelos deprecados o inexistentes
**group y multigroup**
  * algunos page_elements tratan de cargar de forma dinamica a trave de `@parameters[:entity_type]`, algrunos otros tambien lo hacen solo que luego adaptan el codigo para cargar la clase deseada `DashboardGroup por ejemplo`

## PageElements

### PageElement
  * [] en su metodo `format_field` se espera un objeto Date no contempla la posibilidad de que venga en formato string, se puede solucionar con un `field.to_date` ya que ruby ofrece este metodo tanto en strings como en Date precisamente por este tipo de casos

### AllocationChart
  * [] codigo 400 de highcharts, se ha enviado alguna propiedad invalida (categories tiene valores null)
    test : test_bars_with_color_by_value_pdf

### AssetMasterTable
*problemas*
  * [x] algunas barras como en asset_master_table_test => test_bars_profit_mkt_pdf no modifican su z-index (nose como se llama aqui) automaticamente, lo que siempre que haya 2 barras una tape a la otra si resulta tener un valor mayor
  * [x] su metodo parse_data lanza un error que hace que se responda con un `200 success` y con `application/html` lo cual no deberia pasar, es un error fantasma

*peculiaridades*
  - en este caso se debe enviar lo que espera `parse_data` debido a que la funcion

### AssetsDetailClpNotClpGroupedByAttributesAndCustodian
*problemas*
  * [x] el metodo data se llama dentro de `render` rompiendo el uso de `data` como argumento y duplicando la llamada
  ```rb
  max_depth = calculate_max_depth(self.data[:output])
  ```
  `parse_data` no retorna `:output`

### IndexTable
*problemas*
  * [] el metodo data se llama dentro de `render` rompiendo el uso de `data` como argumento y duplicando la llamada
  ```rb
    self_data_header = self.data[:header]
  ``` 
  `parse_data` no retorna `:header`

### AssetsDetailGroupedByAttributesAndCustodian
*problemas*
  * [] el metodo data se llama dentro de `render` rompiendo el uso de `data` como argumento y duplicando la llamada
  ```rb
    max_depth = calculate_max_depth(self.data[:output])
  ``` 
  `parse_data` no retorna `:output`
  * el metodo `parse_data` no devuelve nada por en caso de no estan en el when-case

### AttributionChart
*problemas*
  * si `:attribution_type` es `WATERFALL`

### EquityFlowTwo
*problemas*
  * en `initialize` llamada a un registro completo de `Level` solo para extraer el campo profit_start_date
  * esto no es un problema muy grande, aun se puede testear de forma simple,solo requiere indicar unos ids, el problema es que no esta respetando el estandard de page_elements y hace una query poco optima

  * este componente acepta en `params[:entity_type]` : `level, group y multiple_group`, pero ni group ni multiple_group existen en sietea, por lo que al hacer `@parameters[:entity_type].classify.constantize` explota

### EvolutionAssetsDistributionElement
*problemas*
  * [x] `parse_data` hace calculos y demas cosas, necesita modificar el codigo para manejar numeros y date que vienen en forma de strings y necesitan ser transformados a Float o Date, de lo contrario falla al intentar user metodos o hacer operaciones con atributos que son `string`
    - actualmente se usa stubbing y redifiniciones para sortear esto

### FifoLotsGroupedByAttribute
*problemas*
  * [x] el metodo data se llama dentro de `render` rompiendo el uso de `data` como argumento y duplicando la llamada
  ```rb
  max_depth = calculate_max_depth(self.data[:output])
  ```
  `parse_data` no retorna `:output`

### RvDataTable
*problemas*
  * [] no tiene nada, al parecer nunca se llego a hacer

### TableEditor
*recordatorios*
  * color

### TaxReport
  * falla en `format_field` porque la clase padre no contempla que la fecha pueda ser un string

# TransactionsTable
  * falla en `format_field` porque la clase padre no contempla que la fecha pueda ser un string (osea, que venga de params)
----------------------------------------------------------
----------------------------------------------------------
## PdfElements

### HorizontalTable
*problemas*
  * [] si no se pasa `weight en los headers` dara un error de float null en Prawn

------------------------------------------------------------
------------------------------------------------------------

## problemas o posibles problemas en el codigo

- en el caso de querer hacer test unitarios de forma sencilla o clara sera dificil debido a que page_element esta acoplado a `GenericExport`

- no hay casi ningun tipo de validacion lo que hace que se generen errores confusos o que pase data invalida a highchart lo cual resulta en que el server de highcharts se quede pillado

- a pesar de que se suguiere la posibilidad de pasar data a travez de `params`, es engañoso en algunos casos ya que en varios componentes se realizan operaciones que no contemplan el caso en el que al stringificar y parsear JSON el tipo de las variables cambie, por ejemplo:
  * Data => string 
  * int/float => string

- no respeta sus propias reglas/patrones
  * `paramaters` se supone que viene de params o son cosas de la clase base `page_element_base` pero el metodo `parse_data` varias veces crea parametros sin aviso, resignando cosas que estaban en `data` a una variable y luego esa variable asignandola a `parameters`, todo un caos

  * el codigo siempre hara que data ya sea que venga de `params` o de `self.data` pase por `parse_data`, pero en vez de atjarlo en la primera linea del metodo `render` parsea `self.data` en el mismo valor por defecto del argumento y en el caso de `params[:data]` lo hace en `generic_export` antes de pasarlo a `render`

  * muchos componentes tienen codigo Copy-pasteados de otros componentes, el problema es que la mayoria de ese codigo sobra completamente, no se usa y parece que se pego por el miedo a no romper el codigo, ensuciando la lectura o permitiendo comportamientos inesperados y vulnerabilidades varias, aqui hay demasiados ejemplos asique solo pondre 2 muy claros:
    `COLUMNS` : confuso, porque muchos asignan esto a `header` o contiene valores que jamas se manejan o que nisiquiera tienen sentido dentro del contexto de lo que grafica el componente
    `RULES` : en la mayoria de casos no tiene sentido porque no se usan para validar nada

  * componentes ineccesarios: muchos componentes se podrian agrupar en una solo porque usan las mismas reglas y columnas como es el caso de `assets_detail_`, esto tambien ignora el patron de `visuaization_type`, hace qu se repita codigo y por lo tanto problemas que hay en un componente pasan a estar en otro ya que al parecer se ha copypasteado

  * `COLUMNS` `parameters[:columns]` y `:header` tienen un serio problema de identidad, ejemplos de casos:
    - se recibe `data[:header]` para luego convetirlos a una variable header_list (o algo similar) para luego declararlo como `parameters[:columns]`
    - el caso contrario recibirlo como `params[:columns]` y termina siendo `outputs[:header]` o algo asi
    - recibir `params[:columns]` o `data[:header]` para al final simplemente ignorarlos y usar la constante `COLUMNS` directamente

