### Shared Libraries


1. Hacer un fork del repositorio https://github.com/lissettegar/shredlib.git. Si quereis modificar el fichero src/emails.groovy para poner los nombres y emails que querais.

2. Crear un `Folder` llamado `shared`. En la configuración del `Folder` ir a la seccion `Pipeline Libraries` y configurar un shared Library que se llame `mail_list`.

3. Configurar `default version: master`

4. Configurar el repositorio donde esta la libreria que vamos a configurar como compartida indicando vuestro repo que acabais de crear con el `fork` y hacer click en validate

5. Guardar el folder y dentro de el crear un nuevo pipeline llamado `libexample` con el siguiente pipeline:

       pipeline {
           agent any

           stages {
               stage('buildit') {
                   steps {
                       script{
                           mail = new emails()
                           mail.mails.each {println "name: $it.key , email: $it.value"}
                       }
                   }
               }
           }
       }

6. Guardar el pipeline y ejecutar un build que fallará pq aun no hemos definido la libreria así que no encuentra la clase "emails".

7. Actualizar el pipeline para poner la referencia de la libreria al inicio del pipeline:

       @Library('mail_list') _

8. Guardar el pipeline y ejecutarlo.

9. Comprobar en la consola que se carga la libreria:

       "Loading library mail_list@master"


10. Ahora lo vamos a confgurar de manera Global. Ir a `Manage Jenkins -> System Configuration -> Global Pipeline Libraries ...`.

11. Añadir una library que se llame `mail_global`. Default version `master` y configurar el repo.

12. Guardar y volver a la configuración del pipeline `libexample` y modificarlo para que use la libreria globlal en lugar de la local.

        @Library('mail_global') _

13. Ejecutar un nuevo build y ver en la consola como trae el repositorio de las libreria, esta vez la global:

        "Loading library mail_global@master"
