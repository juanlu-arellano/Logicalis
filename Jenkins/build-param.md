### Build con Parametros, Simple Trigger y Build Monitor View

En este laboratorio vamos a practicar como añadir parametros a un Job, como crear un Trigger para que un Job ejecute otro Job automáticamente y como configurar monitores para los Proyectos.

#### Build con Parametros

1. Crear un job de tipo `freestyle` llamado `build-params`.
2. Añadir la siguiente descripción y ver con la opción `Preview`:

       <h4>Ejemplo Job freestyle con Parametros</h4>

3. En la sección `Build`, añadir un step `Ejecución de una shell` que incluya el siguiente comando:

       uname -a

4. Guardar el job y ejecutar el build.

5. Ver el resultado del Build en la consola consola.

6. Modificar el job para que guarde la salida del comando en un fichero:

       uname -a >>fichero$BUILD_ID.txt

7. Ejecutar otro build comprobar que en el Workspase aparece el fichero y que se puede ver el contenido y mostrar.

8. Modificar el Job habilitando la opción `Delete workspace before build starts` y añadir patron para los ficheros `*.txt`.

9. Ejecutar otro build, ir a la consola y comprobar el mensaje de que se limpia el WS.

10. Modificar el Job para añadir un parametro. En la sección `General` opción `This project is parameterized->Add Parameter->String Parameter` Añadir el siguiente parametro:
Name: Nombre
Default Value: El nombre que se quiera
Descripción: La descripción que se quiera

11. Añadir un nuevo al Build un nuevo step de tipo `Execute shell` en el que se ejecute el siguiente comando:

        echo " Bienvenido a Jenkis" $Nombre

12. Ejecutar un nuevo Build.

#### Simple Build Trigger

1. Crear un `Folder` que se llame `Triggers` y Guardarlo con los valores por defecto.

2. Dentro de la carpeta `Triggers` crear un Job que se llame `echo1` de tipo `freestyle`
3. Añadirle un Build de tipo `Execute shell` con el siguiente comando:

       echo "este es el job echo1" >> output.txt

4. Ejecutar un build para asegurar q el job funciona.

5. Crear un nuevo job en la carpeta `Triggers` que se llame `echo2` de tipo `freestyle`.
6. En la sección `Build Triggers` seleccionar la opción `Build after other projects are built` e indicar el otro proyecto en `Project to watch: ./echo1` para que este job se arranque cuando termine el otro. Seleccionar la opción `Trigger only if build is stable`

7. Añadir un `Build Step` de tipo `Execute shell` con el siguiente comando:

       echo "hola desde el job echo2" >> /var/jenkins_home/workspace/Triggers/echo1/output.txt

8. Ejecutar nuevamente el job `echo1` y comprobar como cuando termina autamaticamente se ejecuta el job `echo2`

#### Configurar Build Monitor View

1. Instalar el Plugin `Build Monitor View`.

2. Una vez instalado, en la pestaña **+** al lado de **All**, o  bien en la opción **New View**, crear una nueva `View` que se llame por ejemplo `Build-status`. Seleccionar la opción `Build Monitor View` y guardar. En la siguiente ventana, añadir la descripción que se quiera y seleccionar todos los jobs que se quieran monitorizar.

3. Acceder al Monitor, pinchando en la vista que acabamos de crear.

#### Configurar Status Monitor

1. Instalar el plugin `Status Monitor`.

2. Aparecerá una nueva opción en el menu `Dashboard` llamada `Status Monitor`.

3. Añadir a la configuración de los Jobs que querais monitorizar una acción `Post Build` de tipo `Status Monitor`

4. Acceder al monitor y comprobar como aparecen los jobs que se hayan añadido.
