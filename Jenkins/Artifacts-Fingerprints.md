### Artifacts y Fingerprints

1. Crear Instalador de Maven en `Manage Jenkins -> Global Tool Configuration -> Add Maven`. Llamarlo `M3` y `guardar`.

2. Volver al `dashboard` y crear un nuevo Item llamado `webdev` de tipo `Folder`. Dejar los valores por defecto y `guardar`

3. Crear un nuevo job dentro del Folder `webdev` de tipo `Freestyle` que se llame `index`.

4. En la sección `Source Code Management` seleccionar `Git` y configurar la URL del repositorio: https://github.com/lissettegar/content-cje-prebuild.git

5. En la sección `Build`,crear ` build step` de tipo `Invoke top-level Maven targets`. Configurar los siguientes valores:

       Maven Version: M3
       Goals: clean package

6. Añadir otro `build step` de tipo `Execute shell` y añadir el siguiente comando:

       bin/makeindex

7. En la sección `Post-build Actions`, añadir un `post-build action > Archive the artifacts`. Configurar los siguientes valores:

       Files to archive field: index.jsp

    Hacer Click en Advanced.... y seleccionar `Fingerprint all archived artifacts`

    Guardar el proyecto.

 8. Ejecutar un Build del proyecto `index`. Una vez termine ir a la opción `Workspace` y ver el contenido del fichero `index.jsp`, opción `view`. Aparecerá el mensaje **"Hello the Tomcat server is running!"**

9. Instalar el `Copy Artifact Plugin`

10. Crear un nuevo Folder que se llame `backend` y dentro de este folder crear un proyecto `Freestyle` que se llame `tomcat`

11. En la sección `Source Code Management` seleccionar `Git` y configurar la URL del repositorio: https://github.com/lissettegar/content-jenkinscert.git

12. En la sección Build, crear ` build step` de tipo `Copy artifacts from another project`. Configurar los siguientes valores:

        Project name: webdev/index
        Artifacts to copy: index.jsp
        Target directory: src/main/webapp

13. Añadir otro `build step` de tipo `Invoke top-level Maven targets` y configurar los siguientes valores:

        Maven Version: M3
        Goals: clean package

14.  En la sección `Post-build Actions`, añadir un `post-build action > Publish JUnit test result report`. Configurar:

         Test report XMLs: target/surefire-reports/*.xml"

15. Guardar los cambios y ejecutar un build de este job. Una vex termine ir a `Workspace > src > main > webapp` y ver el contenido del fichero `index.jsp`, opción `view`. Aparecerá el mensaje correctamente.
