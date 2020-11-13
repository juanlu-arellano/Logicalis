### Preparación entorno para Laboratorios de docker

#### 1. Creación maquina virtual Ubuntu para instalar docker:

https://www.manusoft.es/linux/configurar-una-maquina-virtual-con-ubuntu-en-virtualbox/

Añadir guest additions:

https://kifarunix.com/install-virtualbox-guest-additions-on-ubuntu-20-04/

#### 2. Instalar docker:

https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository

#### 3. Comprobar versión de Docker
Podemos comprobar la versión de Docker ejecutando el comando

	docker --version

y de esta manera nos aseguraremos de tener una versión compatible de Docker.

O ejecutando los siguientes comando podemos ver aún más detalles sobre la instalación de nuestro docker

	$ docker --version
	Docker version 18.06.0-ce, build 0ffa825

	$ docker info
	Containers: 0
	Running: 0
	Paused: 0
	Stopped: 0
	Images: 0
	Server Version: 17.12.0-ce
	Storage Driver: overlay2
	...

#### 4. Comprobar instalación de Docker

4.1. En un terminal de la máquina virtual, ejecute los siguientes comandos para comprobar que inicialmente no hay ningún contenedor creado
(la opción -a hace que se muestren también los contenedores detenidos, sin ella se muestran sólo los contenedor que estén en
marcha):

	$ sudo docker ps -a

o también

	$ sudo docker container ls -a

	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS
	PORTS               NAMES

4.2. Compruebe que inicialmente tampoco hay de ninguna imagen en local:

	$ sudo docker image ls

	REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE

4.3. Docker crea los contenedores a partir de imágenes locales (ya descargadas), pero si al crear el contenedor no se dispone de la imagen local, Docker descarga la imagen de su repositorio público. Ejecutar el siguiente comando para crear un contendor con la aplicación de ejemplo `hello-world`. La imagen de este contenedor se llama `hello-world`:

	$ sudo docker run hello-world

Como no tenemos todavía la imagen en registro local, Docker descarga la imagen, crea el contenedor y lo pone en marcha.
En este caso, la aplicación que contiene el contenedor `hello-world` simplemente escribe un mensaje de salida al arrancar e inmediatamente se detiene el contenedor. La respuesta será similar a esta:

	Unable to find image 'hello-world:latest' locally
	latest: Pulling from library/hello-world
	1b930d010525: Pull complete
	Digest: sha256:4fe721ccc2e8dc7362278a29dc660d833570ec2682f4e4194f4ee23e415e1064
	Status: Download newer image from hello-wolrd:latest

	Hello from Docker!
	This message shows that your installation appears to be working correctly.
	...

4.4. Si listamos ahora las imágenes existentes ...

	$ sudo docker image ls

... se mostrará información de la imagen creada:

	REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
	hello-world         latest              fce209e99eb9        4 weeks ago         1.84 kB

Si listamos ahora los contenedores existentes ...

	$ sudo docker ps -a

... se mostrará información del contenedor creado:

	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS
	PORTS               NAMES
	614B4c431ffa        hello-world         "/hello"            2 minutes ago       Exited (0) 2 minutes
	ago                 hopeful_elbakyan

4.5. Cada contenedor tiene un identificador (ID) y un nombre distinto. Docker "bautiza" los contenedores con un nombre peculiar, compuesto de un adjetivo y un apellido. Podemos crear tantos contenedores como queramos a partir de una imagen. Una vez la imagen está disponible localmente, Docker no necesita descargarla y el proceso de creación del contenedor es inmediato (aunque en el caso de `hello-world` la descarga es rápida, con imágenes más grandes la descarga inicial puede tardar un rato)

4.6. Normalmente se aconseja usar siempre la opción `-d`, que arranca el contenedor en segundo plano (detached) y permite seguir teniendo acceso a la shell (aunque con hello-world no es estrictamente necesario porque el contenedor hello-world se detiene automáticamente tras mostrar el mensaje).

Al crear el contenedor `hello-world` con la opción `-d` no se muestra el mensaje, simplemente muestra el identificador completo del contenedor.

	$ sudo docker run -d hello-world
	1ae54736196021523a2b21c123fd671253e62150daccd882374

4.7. Si listamos los contenedores existentes ...

	$ sudo docker ps -a

... se mostrarán los dos contenedores:

	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS
	PORTS               NAMES
	1ae547361960        hello-world         "/hello"            4 seconds ago       Exited (0) 3 seconds
	ago                 distracted_banach
	614B4c431ffa        hello-world         "/hello"            5 minutes ago       Exited (0) 5 minutes
	ago                 hopeful_elbakyan

Los contenedores se pueden destruir mediante el comando rm, haciendo referencia a ellos mediante su nombre o su id.
No es necesario indicar el id completo, basta con escribir los primeros caracteres (de manera que no haya ambigüedades).

4.8. Borre los dos contenedores existentes:

	$ sudo docker rm 1ae
	1ae
	$ sudo docker rm hopefukl_elbakyan
	hopefukl_elbakyan

4.9. Compruebe que ya no quedan contenedores:

	$ sudo docker ps -a
	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS
	PORTS               NAMES

4.10. Podemos dar nombre a los contenedores al crearlos:

	$ sudo docker run -d --name=hola-1 hello-world

Al haber utilizado la opción `-d` únicamente se mostrará el ID completo del contenedor:

	54e9827bd10ab2825e1b3e4d3bf7a8cbdf778b472359c655d72d9c09e753500a

4.11. Si listamos los contenedores existentes ...

	$ sudo docker ps -a

... se mostrará el contenedor con el nombre que hemos indicado:

	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS
	PORTS               NAMES
	54e9827bd10a        hello-world         "/hello"            4 seconds ago       Exited (0) 3 seconds
	ago                 hola-1

4.12. Si intentamos crear un segundo contenedor con un nombre ya utilizado ...

	$ sudo docker run -d --name=hola-1 hello-world

Docker nos avisará de que no es posible:

	docker: Error response from daemon: Conflict. The container name "/hola-1" is already in use by con
	tainer "54e9827bd10ab2825e1b3e4d3bf7a8cbdf778b472359c655d72d9c09e753500a". You have to remove (or re
	name) that container to be able to reuse that name.
	See 'docker run --help'.
