## pre testing problems
[x] callbacks sucios con \n , espacios y valores invisibles 
[x] en entorno de 'test' se estaba cargando una url de highcharts equivocada
  * se ha creado `fix_callback` y se usa al definir `body` en `base.rb`

## post testing problems
[] error en la linea 77 de area_line.rb al utilizar `[:chart][:style]` porque no esta definido :style en el options layout
[] waterfall y heatmap no tienen fondo transparent porque no esta definido en `options` ni `options_from_params`
[] chart type es modificable a travez de `options_from_params`, cosa que no tiene sentido con la conceptualizacion de la clase
[] si highcharts-export malinterpreta un array como `series` se queda pillado y rails no tira error de timeout o algo asi

## standard flow
- se instancia el objeto
- se pasan options en el constructor
- el constructor pasa options a `options_from_params` y asigna el resultado a `@options`
- se llama a `render` que se hereda de `Base` el cual construye el body de la peticion y la envia a `highcharts-export` para generar el `svg`
- se guarda el `svg` y se revisa si el archivo se ha creado con exito