### Jenkins SCM – Integración con Gitlab

En este laboratorio vamos a probar las integraciones de Jenkins y GitLab

* Para poder usar IPs internas en gitlab hay que configurar lo siguiente:

Admin -> Settings -> Network -> Outbound Requests -> Allow requests to the local network from hooks and services

1. Crear en `Gitlab` local un repositorio público con el nombre `some-code` que contenga el fichero `script-test.sh` con el siguiente contenido:

       #!/bin/bash

       echo "Esto es un ejemplo de un script"
       echo "corriendo en un build step"
       NUM=$(($PRIMERNUM + $SEGUNDONUM))
       echo "El numero total es :" $NUM

2. En Jenkins crear un item de tipo `Folder` con nombre `scm`.

3. Dentro de la carpeta `scm` crear un proyecto de tipo `freestyle` que se llame `scm-gitlab`.

4. En el proyecto seleccionar `Git` como `Source Code Management` e indicar la url del repo que se ha creado en el punto 1 (en mi caso http://192.168.1.50:81/lgarciap/some-code.git)

5. No necesitamos añadir ninguna credencial porque es un repositorio público.

6. En la rama dejaremos `*/master` tal y como aparece.

7. Añadir parametros al Job: `PRIMERNUM` y `SEGUNDONUM`, como `string parameter`, e indicarle algún valor numérico por defecto.

8. Añadir un `Build step` de tipo `Execute shell` en el que se de permisos de ejecucion al script que tenemos en el repo y se ejecute:

       chmod +x ./script-test.sh
       ./script-test.sh

9. Guardar y ejecutar un build. Comprobar que el build se ejecuta correctamente y comprobar la salida de la consola.

### Configuración usando Webhook

10. A continuación vamos a modificar el job para que este proceso de hacer el build sea automatico, para ello tenemos que configurar un webhook. El primer paso sería instalar el `GitLab Plugin`.

11. Una vez instalado el `Gitlab Plugin`, volvemos al job y en la sección de `Build Triggers` aparecerá la opción `Build when a change is pushed to GitLab. GitLab webhook URL...`. Seleccionamos esta opción.

12. Seleccionamos tambien las opciones `Accepted Merge Request Events` y `Closed Merge Request Events`.

13. Pinchamos en `Advanced` para generar un `Token` para nuestro webhook. Copiamos el token generado en un editor.

14. Crear un `Post-build Actions`, elegir `Publish build status to GitLab` y guardar el Job.

15. A continuación vamos al proyecto `some-code` en GitLab y crearemos un webhook en la opción `Settings->Webhooks`. Indicamos la URL de nuestro proyecto de Jenkins por ejemplo http://192.168.1.50:8080/project/scm/job/scm-gitlab (importante cambiar en la URL del job donde pone job debe ser project), copiamos el token que hemos generado en el paso 13, deshabilitar el SSL y guardar el webhook.

16. Hacer un Test de un `Push` y comprobar que se ejecuta el job.

### Configuración usando Jenkins Integration

17. Crear un `personal access token` para autorizar a Jenkins el acceso a GitLab. Para ello en GitLab, en el `Settings` de la cuenta ir a `Access Tokens` en el menú de la derecha. Crear el token con el `API scope` seleccionado. Guardar el valor del token, se usará al configurar el plugin de GitLab en Jenkins.

18. Configurar el plugin de `GitLab`. Ir a `Manage Jenkins -> Configure System` a la sección `GitLab` y marcar la opción `Enable authentication for ‘/project’ end-point`. Hacer click en `Add` para crear un nuevo `GitLab Connection`. Asignarle un nombre, copiar la url del GitLab, por ejemplo http://192.168.1.50:81 y crear una Credencial de tipo `GitLab API token` en la que pondremos el token que obtuvimos en el paso anterior, indicando una descripción.

19. Seleccionar la credencial que acabamos de crear y testear la conexión. Asegurarse de que da ok antes de continuar. Guardar los cambios.

20. Crear un nuevo job dentro de la carpeta `scm`, por ejemplo `scm-jenkins-ci` a partir del job `scm-gitlab`.

21. Ir a GitLab al repositorio opción `Settings -> Integration -> Jenkins CI`. Seleccionar `Enable Integration -> Active`. Seleccionar los siguientes `Triggers`: Push, Merge request, Tag push.

22. Configurar la URL de Jenkins, por ejemplo: http://192.168.1.50:8080

23. Configurar el `Project name: scm/scm-jenkins-ci`.

24. Indicar las credenciales del usuario de Jenkins y hacer Click en `Test settings`, comprobar que funciona correctamente y guardar los cambios.

25. Comprobar que se arranca un build del job y que funciona correctamente.
