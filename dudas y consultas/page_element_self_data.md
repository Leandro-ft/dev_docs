oye, daniel me dijo que te consulte sobre algunos componentes

si no te acuerdas te resumo:
todos los PageElementt tienen los metodos self.data, parse_data y render

para seguirme puede ir mirando `index_table.rb`

si `render` no recibe el argumento `data` entonces se ejecutara `self.data`
si se ejecuta `self.data` este pasara por `data_parse` antes
si se pasa `data` como argumento, este tambien pasara por `parse_data` en `generic_export.rb => render_page_element`

entonces, render siempre espera el resultado de `parse_data` 

estoy hacindo test de integracion, necesito poder testar todos los page_elements de forma rapida para solo svg o pdf, por lo que no me interesa pedir data de la base de datos, osea tengo que saltarme `self.data`

la solucion seria usar `params[:data]` como el codigo lo sugiere, y funciona en la mayoria de page_elements
pero en page_elements como `IndexTable` y otros se llama `self.data` dentro de render de forma obligatoria por segunda vez, obligando a que hayas pasado o no `params[:data]` se ejecute `self.data` y si no pasaste nada pues se ejecutara `self.data` 2 veces

la razon yo sospecho que es porque añadieron por ejemplo en `IndexTable` la necesidad de el atributo `headers` para al final de render poder instanciar `GenericTable`

lo ideal seria modificar `parse_data` para que lo devuelva, pero parece que se opto por parchearlo agregando un `self.data[:headers]`, inhabilitando el uso de `data[:params]` y acoplando render a self.data

daniel me dijo que te pregunte porque podria saber si hay alguna razon de peso para poner ese segundo `self.data` o fue solo un parche rapido o miedo a modificar parse_data etc, porque en esos casos me gustaria modificarlo para que simplente `parse_data` devuelva `headers` y listo asi puedo hacer los test con `data[:params]` sin problemas

recuerdo que esto pasada con otros componentes tambien como `AssetsDetailClpNotClpGroupedByAttributesAndCustodian` que necesita de `output` y como `parse_data` no lo esta devolviendo a pesar de que lo recibe, pues hay un `self.data[:output]` en render