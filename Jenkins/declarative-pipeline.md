### Pipeline declarativo

* Hacer las modificaciones del `Jenkinsfile` usando el `Declarative Director Generator`

1. Ir a la configuración del pipeline `some-code`, borrar los parametros que tiene definido y guardarlo.

2. Modificar el Jenkinsfile en repositorio `some-code` para que los parametros que se usan en el script sean variables de entorno globales. Usar la directiva `environment`. Parametros `PRIMERNUM` y `SEGUNDONUM`:

3. Subir los cambios al repositorio y comprobar que se ejecuta el build correctamente. Comprobar el contenido del fichero `script-file.txt`.

4. Modificar nuevamente el `Jenkinsfile` para añadir una sección `post` para las siguientes condiciones:

       always
           echo 'One way or another, I have finished'
           deleteDir() /* clean up our workspace */

       success
           echo 'I succeeded!'

       unstable
           echo 'I am unstable :/'

       failure
           echo 'I failed :('

5. Subir los cambios al repositorio y comprobar que se ejecuta el build correctamente.

6. Modificar nuevamente el `Jenkinsfile` esta vez para cambiar el agente. Ahora queremos que en lugar de ser `any`que corra en un contenedor docker con imagen `centos:latest`

7. Subir los cambios al repositorio y comprobar que se ejecuta el build correctamente.

8. Modificar el Jenkinsfile para cambiar los valores por defecto de las variables `PRIMERNUM` `SEGUNDONUM` usando un `input`.

9. Subir los cambios al repositorio y comprobar que se ejecuta el build correctamente.
