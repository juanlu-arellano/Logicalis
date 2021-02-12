### Jenkins API – CLI

#### Jenkins API

1. Para usar el API de Jenkins debemos generar un token que usaremos para autenticarnos. Generar el token desde el menú de Nombre de usuario `Configure->Add new token->Generate`. Copiar el token que se crea.

2. Crear un nuevo job `freestyle` con nombre `apijob`, con  un build step de tipo `execute shell`:

       echo "hello from the api"

3. Guardar y ejecutar el build para comprobar que funciona correctamente.

4. Desde un terminal ejecutar el siguiente curl. No devolverá nada pero podemos ver que se ha ejecutado un nuevo build en la consola de jenkins:

       curl -X POST -u <usuario>:<token> http://<jenkins-server>:8080/job/apijob/build

5. Ahora vamos a obtener el fichero de configuración de un proyecto:

       curl -u <usuario>:<token> http://<jenkins-server>:8080/job/apijob/config.xml

6. Esta configuración la podemos descargar, modificar, por ejemplo:

       curl -u <usuario>:<token> http://<jenkins-server>:8080/job/apijob/config.xml >apiconfig.xml

       vi apiconfig.xml  **Cambiar el mensaje del echo**

7. Una vez cambiado el mensaje del echo y subimos la configuración:

       curl -X POST -u <usuario>:<token> http://<jenkins-server>:8080/job/apijob/config.xml -d "@apiconfig.xml"

8. Ejecutar un nuevo build:

       curl -X POST -u <usuario>:<token> http://<jenkins-server>:8080/job/apijob/build

9. Volver a la consola de la ejecución del Job para comprobar que se ha ejecutado y que aparece el nuevo mensaje del echo.

#### Jenkins CLI

1. Descargar el fichero jenkins-cli.jar desde jenkins en:

http://192.168.1.50:8080/cli

2. Una vez descargado ir al directorio donde esta y ejecutar:

 Para ver todos los comandos:

       java -jar jenkins-cli.jar -auth <usuario>:<token> -s http://<jenkins-server>:8080/ -webSocket help

 Para listar los jobs:

       java -jar jenkins-cli.jar -auth <usuario>:<token> -s http://<jenkins-server>:8080/ list-jobs

 Para ver la ayuda de un comando en concreto:

       java -jar jenkins-cli.jar -auth <usuario>:<token> -s http://<jenkins-server>:8080/ help build

 Para ejecutar un job y ver la salida de la consola:
 
       java -jar jenkins-cli.jar -auth <usuario>:<token> -s http://<jenkins-server>:8080/ build apijob -s -v
