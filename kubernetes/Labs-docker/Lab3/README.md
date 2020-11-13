### Almacenamiento Persistente

En este ejercicio vamos a crear un contenedor que incluya un servidor Apache en funcionamiento a partir de una imagen pública, modificar el contenido del contenedor, crear una nueva imagen para crear contenedores personalizados y añadir almacenamiento persistente.

#### 1. Crear el contenedor Apache

Cree un contenedor que contenga un servidor Apache a partir de la imagen bitnami/apache. La opción `-P` hace que Docker asigne de forma aleatoria un puerto de la máquina virtual al puerto asignado a Apache en el contenedor. La imagen `bitnami/apache` asigna a Apache el puerto 8080 del contenedor para conexiones http y el puerto 8443 para conexiones https.

    $ sudo docker run -d -P --name=apache-1 bitnami/apache


Consulte el puerto del host utilizado por el contenedor ...

    $ sudo docker ps -a

... se mostrará el contenedor con el nombre que hemos indicado:

    CONTAINER ID        IMAGE               COMMAND                  CREATED                  STATUS
          PORTS                                              NAMES
    44c9e89bdfd2        bitnami/apache      "/app-entrypoint.sh …"   About a minute ago       Up About a min
    ute   0.0.0.:32769->8080/tcp, 0.0.0.0:32768->8443/tcp   apache-1

Abra en el navegador la página inicial del contenedor y compruebe que se muestra una página que dice "It works!".

En este ejemplo la url seria localhost:32769

#### 2. Modificar la página inicial del contenedor Apache

En este apartado vamos a modificar la página web inicial de Apache del contenedor que hemos creado en el paso anterior.

Tenga en cuenta que modificar el contenido de un contenedor tal y como vamos a hacer en este apartado sólo es aconsejable en un entorno de desarrollo, pero no es aconsejable en un entorno de producción porque va en contra de la "filosofía" de Docker. Los contenedores de Docker están pensados como objetos de "usar y tirar", es decir, para ser creados, destruidos y creados de nuevo tantas veces como sea necesario y en la cantidad que sea necesaria. En el apartado siguiente realizaremos la misma tarea de una forma más conveniente, modificando no el contenedor sino la imagen a partir de la cual se crean los contenedores.

Cree un segundo contenedor que contenga un servidor Apache a partir de la imagen bitnami/apache:

    $ sudo docker run -d -P --name=apache-2 bitnami/apache

Consulte el puerto del host utilizado por el contenedor ...

    $ sudo docker ps -a

... se mostrarán los dos contenedores creados:

    CONTAINER ID        IMAGE               COMMAND                  CREATED                  STATUS
          PORTS                                               NAMES
    c5041d12aabd        bitnami/apache      "/app-entrypoint.sh …"   About a minute ago       Up About a min
    ute    0.0.0.:32771->8080/tcp, 0.0.0.0:32770->8443/tcp   apache-2
    44c9e89bdfd2        bitnami/apache      "/app-entrypoint.sh …"   5 minutes ago            Up 5 min
    utes   0.0.0.:32769->8080/tcp, 0.0.0.0:32768->8443/tcp    apache-1

Cree la nueva página index.html ...

    $ vi index.html

    <!DOCTYPE html>
    <html lang="es">
    <head>
      <meta charset="utf-8">
      <title>Apache en Docker</title>
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
    </head>

    <body>
      <h1>¡Hola, mundo!</h1>
    </body>
    </html>

Entre en la shell del contenedor apache-2 y nos movemos al dir donde esta el index.html:

    $ sudo docker exec -it apache-2 /bin/bash
    $ cd /opt/bitnami/apache/htdocs
    $ cat index.html
    <html><body><h1>It works!</h1></body></html>

Salga de la shell del contenedor y copie el fichero index.html que hemos creado en el contenedor:

    $ sudo docker cp index.html apache-2:/opt/bitnami/apache/htdocs/index.html

Ahora abra el navegador de la aplicacion apache-2 y compruebe el cambio. En este ejemplo la url es localhost:32768

#### 4. Crear una nueva imagen para personalizar el contenedor (usando Dockerfile)

En este caso ya tenemos el index.html del paso anterior y el contenido del Dockerfile puede ser por ejemplo este:

    $ vi Dockerfile
    FROM bitnami/apache
    COPY index.html /opt/bitnami/apache/htdocs/index.html

Crear la imagen a partir del Dockerfile (con la opcion -t indicamos el nombre que va a tener la imagen y el . indica que el Dockerfile esta en el directorio actual):

    $ sudo docker build -t barto/mi-apache .

Cree un contenedor a partir de la nueva imagen:

    $ sudo docker run -d -P --name=mi-apache-1 barto/mi-apache

Abra en el navegador la página inicial del nuevo contenedor y compruebe que se ha modificado.


#### 5. Almacenamiento persistente usando bind mount

En este ejemplo, vamos a volver a aprovechar la imagen bitnami/apache. Esta imagen está configurada para que el directorio `/opt/bitnami/apache/htdocs/htdocs` contenga la pagina de inicio del apache.

Si al crear el contenedor enlazamos el directorio `/opt/bitnami/apache/htdocs/htdocs` con un directorio del host, el contenedor servirá las páginas contenidas en el directorio del host.

Cree un directorio en el host que contendrá las páginas web:

    $ sudo mkdir -p /home/barto/web

Cree un contenedor Apache. La opción `--mount` permite crear el enlace entre el directorio del host y el contenedor. La opción tiene tres argumentos separados por comas pero sin espacios: type=bind,source=ORIGEN-EN-MÁQUINA-VIRTUAL,target=DESTINO-EN-CONTENEDOR. Ambos directorios deben existir previamente.   

    $ sudo docker run -d -P --name=apache-bind-1 --mount type=bind,source=/home/barto/web,target=/opt/bitnami/apache/htdocs bitnami/apache

Compruebe con un navegador que la web no muestra pagina de inicio.

Cree un fichero index.html:

    $ sudo vi /home/barto/web/index.html

... por ejemplo con el contenido siguiente

    <!DOCTYPE html>
    <html lang="es">
    <head>
      <meta charset="utf-8">
      <title>Apache en Docker</title>
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
    </head>

    <body>
      <h1>¡Hola, amigo!</h1>
    </body>
    </html>

Actualice el navegador y compruebe que se muestra la pagina de inicio creada.

#### 6. Almacenamiento persistente usando Volumenes

Cree un contenedor Apache.

La opción `--mount` permite crear el volumen . La opción tiene tres argumentos separados por comas pero sin espacios: type=volume,source=NOMBRE-DEL-VOLUMEN,target=DESTINO-EN-CONTENEDOR. El directorio de destino debe existir previamente.

    $ sudo docker run -d -P --name=apache-volume-1 --mount type=volume,source=vol-apache,target=/opt/bitnami/apache/htdocs bitnami/apache

Compruebe que se ha creado el contenedor y consulte el puerto asignado:

    $ sudo docker ps
    CONTAINER ID        IMAGE               COMMAND                  CREATED                  STATUS
          PORTS                                              NAMES
    3b1bcc5a67f3        bitnami/apache      "/app-entrypoint.sh …"   About a minute ago       Up About a min
    ute   0.0.0.:32769->8080/tcp, 0.0.0.0:32768->8443/tcp   apache-volume-1

Abra en el navegador la página inicial del contenedor y compruebe que se muestra la página inicial habitual de esta imagen. Muestra "It works".

Compruebe que se ha creado un volumen con el nombre asignado al crear el contenedor:

    $ sudo docker volume ls
    DRIVER              VOLUME NAME
    local               vol-apache

Los volúmenes son entidades independientes de los contenedores, pero para acceder al contenido del volumen hay que hacerlo a través contenedor, más exactamente a través del directorio indicado al crear el contenedor.

Entre en el contenedor y liste el directorio `/opt/bitnami/apache/htdocs`:

    $ sudo docker exec -it apache-volume-1 /bin/bash
    $ cd /opt/bitnami/apache/htdocs
    $ ls -l
    total 4
    -rw-rw-r-- 1 root root 45 Jun 11  2007 index.html

Modifique esa página web. Para ello, salga del contenedor:

    I have no name!@3b1bcc5a67f3:$ exit

... y cree un nuevo fichero index.html:

    $ sudo vi index.html

Por ejemplo, con el siguiente contenido:

    <!DOCTYPE html>
    <html lang="es">
    <head>
      <meta charset="utf-8">
      <title>Apache en Docker</title>
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
    </head>

    <body>
      <h1>¡Hola de nuevo, amigo!</h1>
    </body>
    </html>

Copie el fichero index.html en el directorio /app del contenedor (aunque realmente se copiará en el volumen):

    $ sudo docker cp index.html apache-volume-1:/opt/bitnami/apache/htdocs

Actualice el navegador y compruebe que se muestra la página recién creada.

Cree ahora un nuevo contenedor que use el mismo volumen:

    $ sudo docker run -d -P --name=apache-volume-2 --mount type=volume,source=vol-apache,target=/opt/bitnami/apache/htdocs bitnami/apache

Compruebe que se ha creado el contenedor y consulte el puerto asignado:

    $ sudo docker ps

Abra la web del nuevo contenedor en un navegador y compruebe que es la misma pagina web que se modficó para el contenedor anterior.

Los volúmenes son independientes de los contenedores, pero Docker tiene en cuenta qué volúmenes están siendo utilizados por un contenedor.

Si intenta borrar el volumen del ejemplo anterior mientras los contenedores están en marcha, Docker muestra un mensaje de error que indica los contenedores afectados:

    $ sudo docker volume rm vol-apache
    Error response from daemon: remove vol-apache: volume is in use - [a6c8a30f7b1dc7a4ef165046daff226ee
    1d6a69573269ca24d57b5b4b6802881, 3b1bcc5a67f38853810972b1da8a67148fad78c6cd6f22b2c823d141be59c81c]

Detenga los contenedores:

    $ sudo docker stop apache-volume-1
    $ sudo docker stop apache-volume.2

Si intenta de nuevo borrar el volumen del ejemplo anterior ahora que los contenedores están detenidos, Docker sigue mostrando el mensaje de error que indica los contenedores afectados:

    $ sudo docker volume rm vol-apache
    Error response from daemon: remove vol-apache: volume is in use - [a6c8a30f7b1dc7a4ef165046daff226ee
    1d6a69573269ca24d57b5b4b6802881, 3b1bcc5a67f38853810972b1da8a67148fad78c6cd6f22b2c823d141be59c81c]

Borre los contenedores:

    $ sudo docker rm apache-volume-1
    $ sudo docker rm apache-volume.2

Si intenta de nuevo borrar el volumen del ejemplo anterior ahora que no hay contenedores que utilicen el volumen, Docker ahora sí que borrará el volumen:

    $ sudo docker volume rm vol-apache
    vol-apache

Compruebe que el volumen ya no existe:

    $ sudo docker volume ls
    DRIVER              VOLUME NAME

Tenga en cuenta que al borrar un volumen, los datos que contenía el volumen se pierden para siempre, salvo que hubiera realizado una copia de seguridad.
