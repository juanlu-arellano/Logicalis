### Instalación de Jenkins en docker

#### Minimum hardware requirements:

* 256 MB of RAM
* 1 GB of drive space (although 10 GB is a recommended minimum if running Jenkins as a Docker container)

#### Recommended hardware configuration for a small team:

* 1 GB+ of RAM
* 50 GB+ of drive space

1. Configurar una red para jenkins:

       $ sudo docker network create jenkins

2. Para ejecutar los comandos de Docker dentro de los nodos de Jenkins, descargue y ejecute la imagen de `docker:dind`:

       $ sudo docker run --name jenkins-docker --rm --detach \
         --privileged --network jenkins --network-alias docker \
         --env DOCKER_TLS_CERTDIR=/certs \
         --volume jenkins-docker-certs:/certs/client \
         --volume jenkins-data:/var/jenkins_home \
         --publish 2376:2376 docker:dind

3. Crear imagen a partir del siguiente `Dockerfile`:

       FROM jenkins/jenkins:2.263.3-lts-jdk11
       USER root
       RUN apt-get update && apt-get install -y apt-transport-https \
              ca-certificates curl gnupg2 \
              software-properties-common
       RUN curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
       RUN apt-key fingerprint 0EBFCD88
       RUN add-apt-repository \
              "deb [arch=amd64] https://download.docker.com/linux/debian \
              $(lsb_release -cs) stable"
       RUN apt-get update && apt-get install -y docker-ce-cli
       USER jenkins
       RUN jenkins-plugin-cli --plugins blueocean:1.24.4

       $ docker build -t myjenkins-blueocean:1.1 .

4. Arrancar contenedor a partir de la imagen creada en el punto anterior:

       $ sudo docker run \
         --name jenkins-blueocean \
         --rm \
         --detach \
         --network jenkins \
         --env DOCKER_HOST=tcp://docker:2376 \
         --env DOCKER_CERT_PATH=/certs/client \
         --env DOCKER_TLS_VERIFY=1 \
         --publish 8080:8080 \
         --publish 50000:50000 \
         --volume jenkins-data:/var/jenkins_home \
         --volume jenkins-docker-certs:/certs/client:ro \
         myjenkins-blueocean:1.1

5. Para buscar la pass de desbloqueo:

       $ sudo docker logs myjenkins-blueocean

       o

       $ sudo docker exec jenkins-blueocean cat /var/jenkins_home/secrets/initialAdminPassword

6. Abrir Jenkins en un navegador y desbloquear con la password obtenida en el paso anterior.

7. Instalar los pluggins sugeridos.

8. Crear el primer usuario `administrador`, por ejemplo `lgarciap/12341234`.
