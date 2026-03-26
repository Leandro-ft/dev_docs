1. Contexto
lenguaje: ruby
framework: ruby on rails
herramienta de testing: minitest
herramientas de mocking: no

leer page_element_base.rb
leer generic_export.rb

2. Definir casos de renderizado

buscar en el codigo del componente si este presenta distintas opciones de renderizados, generalmente esto se define en el metodo `render` evaluando algun parameter o puede que tambien se defina en `parse_data` evaluando algun parametro tambien, o en ambos

3. TEST y Mocks
una vez definido los casos de renderizado debe crearse un test para cada uno
acto seguido debe analizar el flujo de cada caso y crear un mock apropiada para cada uno

4. Creacion de un mock

Para crear un mock correctamente debe hacer lo sigueinte:
  * se usa como base del hash los atributos que devuelve `data`, base del hash = retorno de `data`
  * para todo lo que este dentro del hash de forma anidada se debe mirar a que atributos trata de acceder `parse_data` y posteriormente `render` ya que los mocks deben ser solo los justos inecesarios, no entidades completas o reflejos exactos de la base de datos, solo los atributos que se usan en el momento
  * lo anterior tambien implica que no hace falta y de hecho esta prohibido revisar otros modelos para saber su estructura, esto solo genera ruido en tu context_view

5. Verificar params
dentro de page_elements `@parameters` es igual a `params` del controlador, todos los parmas terminan en `@parameters`
  
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
