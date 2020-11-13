### ¿Qué estamos construyendo?

Estamos creando una API de `nodejs` que da la hora actual. Para ellos usaremos `express lib`. Veamos cómo se ve desde la perspectiva de las imágenes de docker.

![alt Imagenes][imagen]

[imagen]: imagenes/Imagenes.png


Veamos como contruimos esta imagen de las tres formas:

#### 1. De forma interactiva

Como sabemos, cada imagen parte de una imagen base, por lo tanto tenemos que hacer el pull de la imagen base:

    $ docker pull node:latest

Ahora ya tenemos la imagen base. Para añadirle capas arrancaremos un contenedor usando dicha imagen y entraremos en el:

    $ docker container run -dit --name node-express-server node
    $ docker exec -it node-express-server /bin/bash

A continuación crearemos directorios y ficheros y con un `docker commit` crearemos una nueva imagen añadiendo capas:

    // make a directory
    $ mkdir /usr/src/app

    // cd to that directory
    $ cd /usr/src/app

    //create package.json (confirm all default answer)
    $ npm init

    //install express
    $ npm install --save express

    //install vim to edit the file on command line
    $ apt-get update
    $ apt-get install -y vim

    // create a file here and put all the content here
    // i for insert, esc for command mode, : and wq! after editing

    $ vi index.js

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
        console.log(`server listening on the port:::${port}`);
    });

    // list all the files
    $ ls


Ahora probemos el contenedor y hagamos el `commit` de los cambios:

    // run the index.js under /usr/src/app
    $ node index.js
    server listening on the port:::3080

    // ctrl c and exit
    exit

    // commit the container into an image
    $ docker container commit node-express-server my-node-server

    //test it
    $ docker images
    REPOSITORY                                              TAG                  IMAGE ID            CREATED             SIZE
    my-node-server                                          latest               8e21de81395b        20 seconds ago      994MB

Ya tenemos una nueva imagen creada a partir de otra existe llamada `my-node-server`. Ejecutemos un contenedor con dicha imagen y probemosla:

    $ docker container run -dit --name current-time -p 3080:3080 my-node-server node /usr/src/app/index.js

Para probar la imagen abrir en un navegador `localhosts:3080/time`


#### 2. Usando un Dockerfile

Veamos ahora cómo podemos construir la misma imagen a partir de un `dockerfile`. Dockerfile se usa para automatizar el proceso de creación de imágenes. Una vez que tengamos un Dockerfile, se pueden crear tantas imágenes como sea desee.

En el repositorio de la formación esta el dockerfile que usaremos:

https://github.com/lissettegar/formacion-mayoral/tree/master/kubernetes/Labs-docker/Lab2/node-express-server

Este es el contenido, los pasos que se describen son los mismos que hicimos manualmente en el paso anterior:

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

Para crear la imagen a partir del Dockerfile:


En este punto tendremos en local la nueva imagen y podriamos arrancar un contenedor como hicimos en el caso anterior:

    $ docker container run -dit --name new-node-derver my-node-server:1.0
    $ docker exec -it new-node-server /bin/bash
    root@4142e9490d90:/usr/src/app# ls -la
    total 36
    drwxr-xr-x  1 root root  4096 Jun 17 14:05 .
    drwxr-xr-x  1 root root  4096 Jun 17 14:05 ..
    -rw-r--r--  1 root root   292 Jun 17 13:28 index.js
    drwxr-xr-x 52 root root  4096 Jun 17 14:05 node_modules
    -rw-r--r--  1 root root 14290 Jun 17 14:05 package-lock.json
    -rw-r--r--  1 root root   555 Jun 17 13:28 package.json


#### 3. Importando un fichero comprimido

Con este método, si tenemos una imagen, podemos guardarla en un fichero .tar con el comando `docker save` y luego podemos importar la misma imagen del tar donde lo necesitemos con el comando `docker load`.

Vamos a guardar la imagen que creamos en el caso anterior en un fichero tar:

    $ docker save my-node-server:1.0 > mynodeserver.tar

A continuación vamos a borrar la imagen (si el contenedor aun esta corriendo hay que pararlo y borrarlo antes de borrar la imagen):

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

A partir de aquí se podría arrancar un contenedor usando la imagen que hemos cargado.
