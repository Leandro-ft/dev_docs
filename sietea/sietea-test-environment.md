## Arquitectura de directorios

* se toma el dominio (modulo mayor que contiene otros modulos o clases) como directorio base:

- hay 3 tipos de directorios raizes dentro de `test/`
  * `test/support` -> todo los recursos adicionales a `app/` que se usan para correr los tests
    - logica general: llevan un _support por convencios, son modulos que contienen logica miselanea (de todo tipo y no pertenece exclusivamente a ningun dominio)
    - recursos ordenadas por dominio como `mocks, stub, fake` y todo tipo de data que se use para este o sus sub-modulos especificos
    ejemplo: 
      * archivo original : `lib/high_charts_to_image_v2/bar.rb`
      * recursos
        - `test/support/high_charts_to_image_v2/bar/mocks`
        - `test/support/high_charts_to_image_v2/bar/mocks/mocks.rb`
        - `test/support/high_charts_to_image_v2/bar/mocks/special_mocks.rb`
        - `test/support/high_charts_to_image_v2/bar/stubs`
        - `test/support/high_charts_to_image_v2/bar/factories`

  * `test/out` -> todas las salidas o archvos generador por los test ordenadas por dominio y nombrados segun el test que los genero `usando __method__.to.s`
    ejemplo:
      - `test/out/high_charts_to_image_v2/bar/test_svg.svg`
      - `test/out/high_charts_to_image_v2/bar/test_svg_minimal.svg`
      - `test/out/high_charts_to_image_v2/bar/test_svg_with_outlined.svg`
      - `test/out/high_charts_to_image_v2/bar/test_svg_inverted_axes.svg`
      - `test/out/high_charts_to_image_v2/bar/test_svg_to_pdf.pdf`
      - `test/out/high_charts_to_image_v2/bar/test_svg_to_jpg.jpg`
    - este directorio va en el .gitignore, los subdirectorios se generan recursivamente

  * `test/cualquier otro que no sean los anteriores` -> los demas directorios segun la convencion de rails seran un reflejo exacto de la estructura de directorios del proyecto que en su interior contendran archivos `*_test.rb` en vez de el archivo original
  ejemplo : 
    - archivo original: `lib/high_charts_to_image_v2/bar.rb`
    - test: `test/lib/high_charts_to_image_v2/bar_test.rb`