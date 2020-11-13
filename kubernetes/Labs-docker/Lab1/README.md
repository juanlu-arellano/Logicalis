#### 1. Ejecutar un proceso dentro de un contenedor

Comando para ejecutar un contenedor con un proceso ejecutándose dentro:

    $ docker container run centos ping -c 5 127.0.0.1

La salida sería como esta:

    Unable to find image 'centos:latest' locally
    latest: Pulling from library/centos
    85432449fd0f: Pull complete
    Digest: sha256:3b1a65e9a05...
    Status: Downloaded newer image for centos:latest
    PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
    64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.022 ms
    64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.019 ms
    64 bytes from 127.0.0.1: icmp_seq=3 ttl=64 time=0.029 ms
    64 bytes from 127.0.0.1: icmp_seq=4 ttl=64 time=0.030 ms
    64 bytes from 127.0.0.1: icmp_seq=5 ttl=64 time=0.029 ms

    --- 127.0.0.1 ping statistics ---
    5 packets transmitted, 5 received, 0% packet loss, time 4103ms

En el ejemplo anterior, la imagen del contenedor es `centos` y el proceso que se está ejecutando dentro del contenedor de centos es `ping -c 5 127.0.0.1`, que hace un ping a la dirección de loopback cinco veces hasta que se detiene.

La primera línea es la siguiente:

    Unable to find image 'centos:latest' locally

Esto te indica que Docker no encontró una imagen llamada `centos:latest` en el registro local, así que Docker busca la imagen de algún registro externo. Por defecto, tu entorno Docker está configurado de modo que las imágenes se extraen de Docker Hub en hub.docker.com. Esto se expresa en la segunda línea de la siguiente forma:

    latest: Pulling from library/centos

Las tres líneas de respuesta son las siguientes:

    85432449fd0f: Pull complete
    Digest: sha256:3b1a65e9a05...
    Status: Downloaded newer image for centos:latest

Estas indican que Docker ha descargado con éxito la imagen, centos:latest, de Docker Hub.

Todas las líneas siguientes de la salida son generadas por el proceso que ejecutaste dentro del contenedor, en este caso es el `ping`.

Es posible que también hayas notado que la palabra clave `latest` aparece algunas veces. Cada imagen tiene una versión (también llamada etiqueta), y si no especifica una versión explícitamente, Docker la asume automáticamente como la última versión (latest).

Si ejecutas el contenedor anterior nuevamente en tu entorno, las primeras cinco líneas de la salida no aparecerán, ya que Docker encontrará la imagen del contenedor en el cregistry local, por lo que no tendrá que descargarla. Inténtalo y verifica:

    $ docker container run centos ping -c 5 127.0.0.1


#### 2. Ejecutar un contenedor en background

El comando Docker sería el siguiente:

    $ docker container run -d --name quotes alpine /bin/sh -c "while :; do echo 'esto es una prueba'; printf '\n'; sleep 5; done"

En la expresión anterior, usaste dos nuevos parámetros de línea de comando, `-d` y `–name`. La `-d` le dice a Docker que ejecute el proceso del contenedor como un Demonio de Linux. El parámetro `–name` se puede utilizar para dar al contenedor un nombre explícito en este ejemplo (quotes).

Si no especificas un nombre de contenedor explícito, Docker asignará automáticamente al contenedor un nombre aleatorio pero único.

Una conclusión importante es que el nombre del contenedor debe ser único. Asegúrate de que el contenedor de citas textuales esté activo y en ejecución:

    $ docker container ls -l

Comprueba los logs del contenedor:

    $ docker logs <CONTAINER_ID>


#### 3. Listando contenedores

A medida que continúes ejecutando contenedores a lo largo del tiempo tendrás muchos en tu sistema. Para averiguar qué se está ejecutando actualmente en tu host, puede utilizar el comando container ls de la siguiente manera:

    $ docker container ls

Esto mostrará una lista de todos los contenedores actualmente en ejecución.

Por defecto, Docker genera siete columnas con los siguientes significados:

![alt Container-ls][imagen1]

[imagen1]: imagenes/Container-ls.png


Si deseas listar todos los contenedores definidos en tu sistema, puede utilizar el parámetro `-a` o `–all` como se muestra a continuación:

    $ docker container ls -a

Se mostrará una lista de contenedores en cualquier estado, ya sea created, running o exited (creado, ejecutándose o terminado respectivamente).

A veces, puede ser que solo quieras listar los ID de todos los contenedores. Para lograr esto, tienes que utilizar el parámetro -q:

    $ docker container ls -q

Podrías preguntarte cuál sería la utilidad de esto. Aquí hay un ejemplo:

    $ docker container rm -f $(docker container ls -a -q)

El comando anterior elimina todos los contenedores actualmente definidos en el sistema, incluidos los detenidos. El comando rm significa remover.


#### 4. Detener y ejecutar contenedores

A veces, necesitarás detener temporalmente un contenedor en ejecución. Inicia nuevamente el contenedor del ejemplo anterior:

    $ docker container run -d --name quotes alpine /bin/sh -c "while :; do echo 'esto es una prueba'; printf '\n'; sleep 5; done"

Ahora, puedes detener este contenedor con el siguiente comando:

    $ docker container stop quotes

Cuando intentes detener el contenedor, probablemente notarás que tarda un tiempo (unos 10 segundos) hasta que se ejecuta. ¿Por qué sucede de esta manera? Docker envía la Señal SIGTERM de linux al proceso principal que se ejecuta dentro del contenedor.

Si el proceso no termina por su cuenta, Docker espera 10 segundos antes de enviar SIGKILL, lo que detiene el proceso a la fuerza y finaliza el contenedor.

En el comando anterior, el nombre del contenedor se utiliza para especificar el contenedor específico que se debe detener. Se puede utilizar el ID del contenedor en su lugar.

#### 5. ¿Cómo obtener el ID de un contenedor?

Hay varias maneras de hacerlo. La forma manual es listar todos los contenedores en ejecución y encontrar el que estás buscando en la lista. Sólo tienes que copiar su ID desde allí.

Una forma más automatizada es usar shell scripting y variables de entorno. Por ejemplo, si quieres obtener el ID del contenedor del ejercicio anterior, aquí hay un ejemplo:

    $ export CONTAINER_ID = $(docker container ls | grep quotes | awk '{print $1}')

Aquí utilizamos AWK para obtener el primer campo que es el ID del contenedor. Ahora, en lugar de utilizar el nombre del contenedor, puedes utilizar la variable $CONTAINER_ID en tu expresión:

    $ docker container stop $CONTAINER_ID

Una vez que detienes la ejecución del contenedor, su estado cambia a Exited (terminado).

Puedes reiniciar un contenedor detenido con el comando `docker container start`.


#### 6. Eliminando contenedores

El comando para eliminar un contenedor es el siguiente:

    $ docker container rm <container ID>

Alternativamente, también puedes usar este comando:

    $ docker container rm <container name>

A veces, eliminar un contenedor en ejecución no funcionará; Si desea forzar la eliminación, puedes utilizar el parámetro de línea de comandos `-f` o `–force`.
