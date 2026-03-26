## Contexto de page_elements

### ubicaciones de recursos
- Componentes principales:
  * page_element_path: app/pdfs/page_element

- Componentes secundarios que se usan dentro de los page_elements:
  * pdf_elements_path: app/pdfs/pdf_elements
  * highcharts_elements: lib/high_charts_to_image_v2

### metodos
los page_elements comparten algunas caracteristicas similares como

- comportamientos
  * todos heredan PageElementBase
  * si no se pasa `:data` en el metodo `render` se intenta ejecutar el metodo `data` propio de la clase la cual hara llamadas a la base de datos y devolvera cierta estructura esperada por render

- metodos en comun
  * metodo heredados
  * data => trae data de la base de datos y devuelve un hash con cierta estructura especifica esperada por `render`
  * render => usa `data` ya sea de pasada por los argumentos o generada en ausencia de ellos para crear `high_charts_to_image_v2` , `pdf_elementos` entre otros y añadirlos al pdf
  * parse_data => se suele usar sobre `params[:data]` o en su ausencia al metodo `data`
---

### relacion controller y page_element
* params => @parameters
* params[:data] => 

### diferencias entre page_elements
* por `data` method
  - hay algunos que no especifican un metodo data pero al mismo tiempo `render` pide cierta data especifica que planea renderizar como lo son `GenericChart` y `GenericBar`
  - los que tienen un metodo `data` que indica lo que espera `render`, como `AllocationLimitsChart`

* por `parse_data`
  - algunos parsean valores 1 a 1 pero las llaves que entran son las mismas que salen
  - otros agarran un conjunto de valores y los reaordenando y reasignan, haciendo que las keys que salen sean distintas a las que entraron como en el caso de `AssetMasterTable`

* por render_type
  - algunos solo renderizan una sola cosa
  - otros renderizan dinamicamente dependindo de un campo como en el caso de `AssetMasterTable` o `AllocationManager`

### flujo de uso
- se hace una peticion a /pdf_by_page_element?key_name={nombre de page_element class en snake_case}
- se declaran variables por defecto como pdf_config_version, dimension y otras mas
- se instancia GenericExport, este define la config del pdf
- se usa generic_export.render_page_element y se le pasa todos los params configurados en el controller
- `render_page_element` intenta parsear e instanciar la clase a la que se hace referencia el `:key_name` pasado en los params
- renderiza el elemento pasandole `:data` que tambien viene de los params