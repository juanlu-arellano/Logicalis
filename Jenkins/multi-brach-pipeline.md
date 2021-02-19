### Multibranch Pipeline

1. Ir al repositorio `some-code`y modificar el `Jenkinsfile` para eliminar las lineas de input que añadimos en un laboratorio previo. Borrar estas lineas:

       input {
          message 'Entre los valores Primernun y Segundonum'
          parameters {
            string defaultValue: '', description: '', name: 'PRIMERNUM', trim: false
            string defaultValue: '', description: '', name: 'SEGUNDONUM', trim: false
          }
       }

2. Añadir los cambios y hacer un commit.

3. Crear una nueva rama llamada `develop` a partir de la rama master.

4. Subir los cambios y la rama al repositorio remoto.

#### Usando como Branch Source un repositorio de Git

5. Crear un nuevo proyecto de tipo `Multibranch Pipeline`, ir a la sección `Branch Source-> Add Source -> Git` y configurar la URL del repositorio: por ejemplo: http://192.168.1.50:81/lgarciap/some-code.git

6. Definir en `Behaviors : Discover branches`, dejar el resto de valores por defecto y guardar el Pipeline.

7. Al guardar nos llevará a la ventana de la opción `Scan Multibranch Pipeline Log` donde aparece en que ramas ha encontrado un `Jenkinsfile`. En nuestro caso lo encontrará en ambas ramas por lo que se ejecutará un build de cada una de ellas.

8. Ir a la ventana de estado del Pipeline y ver que se han ejecutado ambos. Comprobar la consola de ejecución de ambos builds.

9. Ir al repositorio y crear una nueva rama llamada `testing`. Subir la nueva rama al repositorio y desde Jenkins ejecutar un nuevo `Scan Multibranch Pipeline Now` y observar como reconoce la nueva rama y que tiene un `Jenkinsfile` y se ejecuta un build unicamente para esa rama.

10. Borrar el `Jenkinsfile` de la rama `testing`, subir los cambios al repositorio remoto y ejecutar nuevamente un `Scan Multibranch Pipeline Now`. Comprobar como ya no aparece la rama testing como una de las ramas registradas en el Pipeline.

11. Si queremos automatizar la ejecución del Pipeline cuando se ejecute en `Push` podemos configurar en el Jenkinsfile un `Trigger`. Moverse a una de las ramas que tiene Jenkinsfile y modificarlo para incluir la siguiente linea despues del agent y antes del stages:

        triggers { gitlab() }

12. Subir los cambios al repositorio. El Pipeline no se ejecutará porque aun Jenkins no tiene los cambios. Ejecutar nuevamente un `Scan Multibranch Pipeline Now` y comprobar que se ejecuta un nuevo build para la rama donde se ha hecho el cambio.

13. Crear un nuevo fichero en dicha rama del repo y probar a hacer un push. Comprobar que en esta ocasión si se ejecuta el build automáticamente.
