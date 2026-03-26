
## Problemas
* 1 
	- problema: fondo transparent se ve negro
	- fuente: prawn-svg
	- causa: highchart con un formato antitucado para programas que actuan como lectores de pdf, prawn espera un svg con`rgb(0,0,0) opactity=0` en vez de rgba
	- solucion: agregar un gsub de correccion en `lib/high_charts_to_image/base.rb` en el metodo `fix_svg` ya que se llama en el metodo que se usa para generar los svg

* 2
	- problema: fondo transparent se ve negro
	- fuente: puppeteer
	- causa: el cambio de phantom hacia puppeteer agrega ciertas restricciones, entre ellas esta el uso obligatorio de string que puedan transformarse en funciones de JS en el campo `formatter` del highchart
	- solucion: dentro de `lib/high_charts_to_image/base.rb` en el metodo `to_highchart_img` antes de convertir highcharts a svg se puede remplazar `000will_be_replaced000` por `function() { return '000will_be_replaced000'; }` para interceptar todos, o bien cambiarlos en cada archivo que usa este string custom `000will_be_replaced000` el resultado final seria el mismo un bloque <text> con el value `000will_be_replaced000` en el svg

* 3
	- problema: fondo transparent se ve negro
	- fuente: highcharts
	- causa: es un problema de las librerias internas de highcharts para gnerar el pdf, es un problema de herencia de valores por defecto como `textOutline`, segun esto se hizo para cumplir con las pautas de accesibilidad WCAG
	- soluction: la solucion oficial es actualizar de la `11.1.0` (en el caso de nuestro dockfile) a `11.2.0` y agregar manualmente `textOutline: none` y `color: #000000` para mantener el estilo anterior, aunque con `color: #000000` basta

* 4
	- problema: BUG, server congelado al agregar outlined
	- fuente: highcharts
	- causa: al agregar `textOutline` especificamente en la config de highcharts `plotOptions.ine.dataLabel.styles` highcharts server no responde nunca y el server espera infinitamente por un respuesta quedandose congelado, si bien es un BUG de highcharts, que rails se quede infinitamente esperando es un problema de diseño creo
	- solucion: aun no se, solo sospechas, pero podriamos dejar solo color sin quitar el textOutline y se ve exactamente igual que la vieja version

al final los cambios se resumen en modificar solo la funcion que retorna la img/svg, ahi se puede corregir el svg, el highcharts y cualquier propiedad de highcharts config, es un punto por el que pasan todos los componentes

## extra
- la version 11.2.0 al parece resolvio algunas cosas mas porque la carga del pdf es mucho mas rapida que la version antigua

- la nueva versoin de highchart reajusta el y-limit por defecto para mejorar la visibilidad por ejemplo:
	* en page_aum_evolution `y-izquierda (patrimonio)` es el valor maximo y queda con un limite 88k, eso no ha cambiado, en cambio `y-derecho (aportes)` es un valor mucho menor, y su limite que antes era de 25k ahora es de 15k
	* esto hace que las barras no sean tan pequeñas cuando se expanden poco en `y`, algo similar ocurre con `x`
	* los valores siguen siendo consistentes con las escalas

# pdf page build flow:
```md
- routes.rb 
	* pdf_by_page
		- metodo render busca un `StatementPage` segun param[:key_name]
		- `StatementPage` estructura => 
			* directorio : app/pdfs/statement_pages
			* estructura de archivo:
				- render method
				- cada clase en cada archivo esta dentro de un module `StatementPages`
				- cada `StatementPage` carga su propia data
				- heredan `PagesBase`
				- `PagesBase` estructura => 
					* tiene metodos make_{algo}
						- el metodo carga algun `HighchartsToImage` `{Pie,Line,Waterfall}`
						- devuelve un pdf armado con estos elementos
						- `HighchartsToImage` estructura =>
						- cada clase en cada archivo esta dentro de un module `HighchartsToImage`
							* directorio: lib/high_charts_to_image
							* cada archivo contiene un elemento de highchart cons sus propiedades
							* todos heredan Base de base.rb en el mismo directorio
							* todos son llamados desde algun nivel superior usando el metodo `to_highchart_img` el cual cada elemento hereda de Base
```

# StatementCompleteCustomConfig

```md
- entra `:statement_version` como param a `pdf_by_page_controller#render_pdf`
- se busca un `StatementCompleteConfig` que es una configuracion generica con una `data` se usa en todos los statatement

- se busca el `StatementPage` por params[:key_name] 
- ejemeplo:
	si params[:key_name] es `page34evolution_raf` entonces se cargara la clase `Page34evolutionRaf`

- statement como este necesitan de un `StatementCompleteCustomConfig` ya que necesitan su campo `data` el cual es un json strings creado a partir de un excel con campos especificos

- `Page34evlutionRaf` intentara usar `StatementCompleteCustomConfig.send("evolution_asset_pie_1", opts)`
- esto buscara un registro de `statement_complete_custom_configs` en la base de datos donde:
	* `.name` = evolution_asset_pie_1
	* `.version` = opts[:version] probablemente que corresponde al params[:statement_version] de antes
```
