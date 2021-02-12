### Crear un Pipeline desde el GUI de Jenkins

1. Crear un nuevo Proyecto que se llame `demo-pipeline` de tipo `Pipeline`

2. Configurar 2 parametros para nuestro Pipeline de tipo `string` `PRIMERNUM` y `SEGUNDONUM` y darle valores por defecto:

3. En la sección **Pipeline** elegir el ejemplo `Hello World` como plantilla.

4. Modificar la plantilla para tener 3 stages que se llamen `gitcheckout`, `build` y `post`.

5. Usar la opción `Pipeline Sintax` para configurar los siguientes steps en cada uno de nuestros stages:

 5.1. Stage `gitcheckout`: hacer un clone del repositorio `some-code` que se creó en la práctica **build-scm**. Por ejemplo: http://192.168.1.50:81/lgarciap/some-code.git

 5.2. Stage `build`: ejecutar los siguientes shell commands:

       chmod +x ./script-test.sh
       ./script-test.sh > script-file.txt

 5.3. Stage `post`: Guardar el fichero generado en el stage anterior `script-file.txt` como artifact.

6. Guardar el Job y ejecutar un build.

7. Comprobar que se ejecuta correctamente y ver la salida de la consola de la ejecución del `Pipeline`.

8. Comprobar que el fichero guardado como `artifact` contiene la ejecución del script.

9. Ir a la configuración del Job y copiar el contenido del `Pipeline` en el fichero `Jenkisfile` en nuestro repositorio `some-code`. Hacemos un commit y subimos los cambios al repositorio remoto.

10. Modificar el Pipeline para que obenga el pipeline desde un SCM. Indicamos la URL de nuestro repositorio y Seleccionamos la rama, en este caso la `master` (valor por defecto).

11. Configuramos que nuestro pipeline donde está nuestro `Jenkinsfile`. Como estará en el root directory de nuestro repo se puede dejar el valor por defecto.

12. Guardamos el Job, ejecutamos un Build y comprobamos que funcina correctamente.

13. A continuación vamos a añadir un `Webhook` en el repositorio de código para que se ejecute el pipeline automaticamente al hacer un push al repositorio. Seguir las indicaciones de la práctica `build-scm.md`.

14. Crear un nuevo fichero en el repositorio subir los cambios al repositorio remoto y comprobar que se ejecuta el Pipeline.
