### Instrucciones Laboratorio 2 docker - Manejo de Imagenes

En este laboratorio vamos a crear una imagen de las tres formas que hemos comentado, de forma interactiva, a partir de un docker file o desde un fichero comprimido creado a partir de una imagen existente. 
La aplicación es una API de `nodejs` que da la hora actual. Para ellos usaremos `express lib`. Veamos cómo se ve desde la perspectiva de las imágenes de docker.

![alt Imagenes][imagen]

[imagen]: imagenes/Imagenes.png


#### 1. Creando la imagen de forma interactiva (modificando el contenedor)

Cada imagen parte de una imagen base, así que lo primero que haremos será hacer el pull de la imagen que queremos que sea nuestra imagen base:

    $ docker pull node:latest

Ahora ya nos hemos traido la imagen base. Para añadirle capas de forma interactiva, arrancaremos un contenedor usando dicha imagen, entraremos en el y ejecutaremos todo lo que necesitamos para crear nuestra imagen personalizada.

Arrancamos el contenedor:

    $ docker container run -dit --name node-express-server node

Entramos en el contenedor:

    $ docker exec -it node-express-server /bin/bash

Una vez dentro del contenedor crearemos directorios y ficheros y finalmente con un `docker commit` crearemos una nueva imagen ya personalizada.

Dentro del contenedor creamos un directorio:

    $ mkdir /usr/src/app

Nos movemos al directorio:

    $ cd /usr/src/app

Creamos el package.json (confirmar todas las respuestas por defecto):

    $ npm init

Instalar el express:

    $ npm install --save express

Instalamos el vim para usarlo como editor:

    $ apt-get update
    $ apt-get install -y vim

Creamos un fichero index.js:

    $ cat > index.js <<EOF
    const express = require('express');
    const app = express();
    const port = 3080;
    
    app.get('/time', (req,res) => {
        res.send(new Date());
    });
    
    app.get('/', (req,res) => {
        res.send('App Works !!!!');
    });
    
    app.listen(port, ()=> {
        console.log(\`server listening on the port:::\${port}\`);
    });
    EOF

Comprobar si el contenido del fichero index.js es correcto:

    $ cat index.js

Ahora probamos la aplicación ejecutando el index.js:

    $ node index.js
    server listening on the port:::3080

    // ctrl c y exit para salir
    exit

En este punto la imagen esta personalizada tal y como la queremos, ya podemos crear una nueva imagen llamada `my-node-server` a partir de las modificaciones que hemos hecho al contenedor:

    $ docker container commit node-express-server my-node-server

Comprobamos que la imagen se crea correctamente:

    $ docker images
    REPOSITORY                                              TAG                  IMAGE ID            CREATED             SIZE
    my-node-server                                          latest               8e21de81395b        20 seconds ago      994MB

A continuación arrancaremos un contenedor de nombre `current-time` con la imagen que hemos creado:

    $ docker container run -dit --name current-time -p 3080:3080 my-node-server node /usr/src/app/index.js

Para probar la imagen abrir en un navegador `localhost:3080/time`


#### 2. Creando la imagen usando un Dockerfile

Veamos ahora cómo podemos construir la misma imagen a partir de un `dockerfile`. Dockerfile se usa para automatizar el proceso de creación de imágenes. Una vez que tengamos un Dockerfile, se pueden crear tantas imágenes como sea desee.

En el repositorio de la formación esta el `Dockerfile` que usaremos para crear la imagen en el directorio `node-express-server`. En este directorio tambien estan los ficheros `package.json` e `index.js` que se necesitan para la imagen. Nos movemos al directorio `node-express-server`:

    $cd <repo>/kubernetes/Labs-docker/Lab2/node-express-server

Este es el contenido del `Dockerfile`, los pasos que se describen son los mismos que hicimos manualmente en el punto 1:

    # base image
    FROM node:latest

    # setting the work direcotry
    WORKDIR /usr/src/app

    # copy package.json
    COPY ./package.json .

    # install dependencies
    RUN npm install

    # COPY index.js
    COPY ./index.js .

    RUN node -v

    CMD ["node","index.js"]

Para crear la imagen a partir del Dockerfile, ejecutaremos un `docker build`:

    $ docker build -t my-node-server:1.0 .

En este punto tendremos en local la nueva imagen:

    $ docker images
    REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
    my-node-server                1.0                 69fb5d3d952c        2 minutes ago       940MB

 y podriamos arrancar un contenedor a partir de dicha imagen:

    $ docker container run -dit --name new-node-server my-node-server:1.0

Si entramos en el contenedor que acabamos de crear veremos que el directorio `/usr/src/app` contiene los ficheros que se indicaba que se copiaran en el Dockerfile:

    $ docker exec -it new-node-server /bin/bash
    root@4142e9490d90:/usr/src/app# ls -la
    total 36
    drwxr-xr-x  1 root root  4096 Jun 17 14:05 .
    drwxr-xr-x  1 root root  4096 Jun 17 14:05 ..
    -rw-r--r--  1 root root   292 Jun 17 13:28 index.js
    drwxr-xr-x 52 root root  4096 Jun 17 14:05 node_modules
    -rw-r--r--  1 root root 14290 Jun 17 14:05 package-lock.json
    -rw-r--r--  1 root root   555 Jun 17 13:28 package.json

Salir del contenedor con exit

#### 3. Creando la imagen importando un fichero comprimido

Con este método podemos guardar una imagen que tengamos en un fichero `.tar` (con el comando `docker save`) y luego podemos importar la misma imagen que tenemor en el fichero `.tar` donde lo necesitemos (con el comando `docker load`).

Vamos a guardar la imagen que creamos en el caso anterior (my-node-server:1.0) en un fichero .tar:

    $ docker save my-node-server:1.0 > mynodeserver.tar

A continuación vamos a borrar la imagen (si el contenedor aun esta corriendo hay que pararlo y borrarlo antes de borrar la imagen):

    $ docker stop new-node-server
    new-node-server
    $ docker rm new-node-server
    new-node-server
    
    $ docker image ls |grep my-node-server
    my-node-server                                          1.0                  4c9d7b781a89        8 minutes ago       945MB
    my-node-server                                          latest               8e21de81395b        21 hours ago        994MB

    $ docker image rm my-node-server:1.0

Y ahora vamos a volver a crear la imagen a partir del fichero tar:

    $ docker load < mynodeserver.tar
    bf5297e6e7ac: Loading layer [==================================================>]  3.072kB/3.072kB
    3c6a1dc4cadc: Loading layer [==================================================>]  4.096kB/4.096kB
    0175ea967955: Loading layer [==================================================>]  4.029MB/4.029MB
    955b95942808: Loading layer [==================================================>]  3.584kB/3.584kB
    Loaded image: my-node-server:1.0

A partir de aquí se podría arrancar un contenedor usando la imagen que hemos cargado. Esta opción es muy útil si se quiere compartir imagenes.
