### Configuración de Agentes

#### Configurando un agente en Ubuntu:

1. Crear el usuario Jenkins en la máquina que será agente de Jenkins:

       $ ls -l /var/lib
       $ sudo mkdir /var/lib/jenkins

       $ ls -l /var/lib | grep jenkins
       $ sudo useradd -d /var/lib/jenkins jenkins
       $ sudo chown jenkins:jenkins /var/lib/jenkins

2. Generar clave ssh y copiarla al fichero `/var/lib/jenkins/.ssh/auhorized_keys`:

       $ ssh-keygen

       $ sudo mkdir /var/lib/jenkins/.ssh
       $ cat ./.ssh/id_rsa_pub
       $ sudo vim /var/lib/jenkins/.ssh/auhorized_keys

3. Instalar java:

       $ sudo apt-install openjdk-8-jre-headless

4. Configurar el agente en la consola de Jenkins, opción `Manage Jenkins->Manage Nodes and Cloud`:

![alt ubuntu-agent][ubuntu-agent]

[ubuntu-agent]: ../imagenes/ubuntu-agent.png

![alt ubuntu-agent-3][ubuntu-agent-3]

[ubuntu-agent-3]: ../imagenes/ubuntu-agent-3.png

5. Obtener la clave privada para crear el usuario en Jenkins:

       $ cat ./.ssh/id_rsa

![alt ubuntu-agent-2][ubuntu-agent-2]

[ubuntu-agent-2]: ../imagenes/ubuntu-agent-2.png

6. Terminar de crear el agente segun las indicaciones de las imagenes y guardar. Si ocurre un error al intentar arrancar el agente porque no existe el fichero `known_hosts` en el home del usuario jenkins, corregidlo haciendo un ssh desde el `master` al `agente` para que se cree un fichero `known_hosts` en el master que incluya los datos del agente. A continuación copiar el fichero `known_hosts` al home de jenkins:

       $ sudo cp ./.ssh/known_hosts /var/lib/jenkins/.ssh


#### Configurando un cluster de docker (usando un contenedor Docker in docker):

* Prerequisitos: Tener corriendo el contenedor `jenkis-docker` como se describe en el fichero `installation.md` y un servidor de Jenkins.

1. Instalar el plugin de `docker`. Ir a `Manage Jenkins -> Manage Plugin`:

![alt docker-agent][docker-agent]

[docker-agent]: ../imagenes/docker-agent.png

2. Una vez instalado el plugin de docker, Ir a `Manage Jenkins -> Manage Nodes and Cloud -> Configure CLoud` y hacer click en `Add a new Cloud` y seleccionar  `docker`. Aparecerá una ventana como la que mostramos a continuación, donde tenemos que configurar un nombre para este cloud y el URI del host de docker (en este caso nuestro contenedor dind) `tcp://docker:2376` y luego hacer click en `Test Conexion`:

![alt docker-agent-2][docker-agent-2]

[docker-agent-2]: ../imagenes/docker-agent-2.png

3. Aparecerá el error que vemos en la imagen anterior, para resolver esto, debemos configurar las credenciales del servidor docker. Para ello necesitamos los ficheros del certificado que están en el directorio `/certs` tal y como indicamos en la creación del contenedor. Para obtenerlos ejecutaremos los siguientes comandos en el contenedor donde corre el `dind`:

       $ sudo docker exec jenkins-docker cat /certs/client/key.pem
       $ sudo docker exec jenkins-docker cat /certs/client/cert.pem
       $ sudo docker exec jenkins-docker cat /certs/server/ca.pem

4. Crearemos una nueva credencial de tipo `X.509 Client Certificate` con los datos del certificado que se obtienen en el paso anterior.

5. Seleccionamos la credencial que acabamos de crear y volvemos a testear la conexion. Esta vez se conectará y nos aparecerá la versión de docker:

![alt docker-agent-3][docker-agent-3]

[docker-agent-3]: ../imagenes/docker-agent-3.png

6. El siguiente paso es configurar un agente para ejecutar nuestros jobs. Para ello tenemos que indicarla imagen que usará nuestro agente, rellenar los campos que se muestran en la siguiente imagen:

![alt docker-agent-4][docker-agent-4]

[docker-agent-4]: ../imagenes/docker-agent-4.png

 Los campos relevantes incluyen darle una `etiqueta` y un `nombre`. La `etiqueta`  permite asociar un `job` a un agente en particular, por lo que siempre puede ejecutar una compilación de Maven en un Agente que tenga Maven instalado, por ejemplo.

7. Para verificar si funciona crear un nuevo job en el que seleccionaremos el agente de la plantilla que acabamos de crear:

![alt docker-agent-5][docker-agent-5]

[docker-agent-5]: ../imagenes/docker-agent-5.png

![alt docker-agent-6][docker-agent-6]

[docker-agent-6]: ../imagenes/docker-agent-6.png

8. Lanzar un build del job y comprobar en la consola del job como se ejecuta en un contenedor. Durante el tiempo de ejecución tambien se puede comprobar que se crea un contenedor dentro del contenedor jenkins-docker:

       $ sudo docker exec -it jenkins-docker docker ps
