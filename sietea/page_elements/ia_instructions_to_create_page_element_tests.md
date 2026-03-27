1. Contexto general del proyecto:
lenguaje: ruby
framework: ruby on rails
herramienta de testing: minitest
herramientas de mocking: no

leer page_element_base.rb
leer generic_export.rb

controller recibe params y en params puede venir data, todo esto se pasa a generic_export el cual instancia un PageElements segun el key_name de forma dinamica y ejecuta `parse_data` del Pagelement cargado seguido de `render` al cual le pasa el resultado de `parse_data` y los demas params pasan a convertirse en `@parameters` dentro del PageElement

este PageElement sera el que sera testeado

2. Definir casos de renderizado:

buscar en el codigo del componente si este presenta distintas opciones de renderizados, generalmente esto se define en el metodo `render` evaluando algun parameter o puede que tambien se defina en `parse_data` evaluando algun parametro tambien, o en ambos

el objetivo pasara a crear un test por cada caso de renderizado definido en la clase

3. definir mock_data:
Para crear mock_data correctamente debe hacer lo sigueinte:
  * se usa como base del hash los atributos que devuelve `data`, oesa que el primer nivel del objeto es igual a el retorno de `data`
    ejemplo: si data retorna
    ```rb
    {
      name:..., 
      header:...
    }
    ```
    entonces el mock tambien debe tener como base esta misma estructura

  * para los demas niveles del hash se debe revisar `parse_data`
  deben agregarse todos los atributos a los que intenta acceder `parse_data`
  * asi mismo, `render` no puede fallar al intentar acceder a atributos de params[:data] luego de que params[:data] haya sido manipulado o convertido por `parse_data`
  en resumen: se debe asegurar de que `parse_data` nunca falle al intentar acceder a algo de params[:data] y que `render` no falle al acceder a propiedades que esteen en `parse_data`

  * la jerarquia seria la siguente
  - el exito de `render` depende del retorno de `parse_data`
  * el exito de `parse_data` depende de params[:data]

4. definir params:
`render` y `parse_data` intentaran acceder a propiedades de `@parameters` si estos no se definen por defecto o no se definen dentro de ninguno de estos dos metodos, entonces deberas enviarlo a travez de `params` en el cuerpo de la request

*el controller y el generic_export se encargaran de pasarlos al PageElement y que terminene en `@parameters`*

## EJEMEPLO DE IMPLEMENTACION

```rb
require "test_helper"

module ControllerTest
  class AllocationLimitsChartTest < ActionDispatch::IntegrationTest
    def test_pdf
      data_mock = {
        title: "Distribución de Activos vs Límites",
        allocation_data: [
          {
            region_name: "Acciones",
            end_percentage: 65.0,
            benchmark_percentage: 60.0,
            lower_bound: 40.0,
            upper_bound: 80.0,
            within_limits: true,
            return_percent: 5.0
          }
        ]
      }
      get "/pdf_by_page_element",
        params: { 
          key_name: 'allocation_limits_chart',
          data: data_mock
        },
        as: :json

      unless response.successful?
        puts "ERROR DETECTADO (#{response.status}):"
        puts response.body
      end

      assert_response :success
      assert_equal "application/pdf", response.media_type

      path = File.join("page_elements", "allocation_limits_chart.svg", __method__.to_s)
      assert File.exist?(save_pdf(path, response.body))
    end
  end
end
```

## PROBLEMAS COMUNES DE CLINE
- :totalRows missing fiel, cline es tonto y no se da cuenta de que params[:totalRow] se asigna a la variable total
- no leer bien `parse_data` y no entender que la misma clase crea `parameter[:columns]` o algun otro 
- tipica confudion de enviar params[:columns] y no darse cuenta de que parse_data es quien declara parameters[:columns] basandose en `params[:header]` provocando un error de columns not found
- cline no sigue el flujo de params, params[:algo] casi siempre es reasignado y guardado en otra variable dentro de `parse_data` y cline ignora esto