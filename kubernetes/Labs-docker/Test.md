57. Which instruction sets the base image for the subsequent builds in the Dokcerfile?

FROM

58. No instruction can precede FROM in the Dockerfile. Is this statement correct?

No. ARG is the only instruction can precede FROM

59. What are the two forms for the RUN instruction?

shell form: RUN <command>
exec form: RUN ["executable", "param1", "param2"]

60. What does the RUN instruction do in the Dockerfile?

The RUN instruction will execute any commands in a new layer on top of the current image and commit the results.

61. The RUN command normally utilizes cache from the previous build. Which flag should you specify for the build not to use cache?

--no-cachedocker build --no-cache .

62. Is there any other instruction that can invalidate the cache?

Yes. ADD

63. How many forms that CMD instruction has?

CMD ["executable","param1","param2"] (exec form, this is the preferred form)CMD ["param1","param2"] (as default parameters to ENTRYPOINT)CMD command param1 param2 (shell form)

64. If CMD instruction provides default arguments for the ENTRYPOINT instruction, both should be specified in JSON format. Is this statement correct?

Yes

65. What is the purpose of the CMD instruction in the Dockerfile?

The main purpose of a CMD is to provide defaults for an executing container. These defaults can include an executable, or they can omit the executable, in which case you must specify an ENTRYPOINT instruction as well.

66. How to make your container execute the same executable every time?

use ENTRYPOINT in combination with CMD

67. What is the purpose of the LABEL instruction in the Dockerfile?

It adds metadata to the Image

68. How to check the labels for the current image?

docker inspect // Under Labels section

69. The EXPOSE instruction actually publish the port. Is this statement correct?

No. It serves as a type of documentation between the image publisher and image consumer

70. What should you do to actually publish the ports?

use -p flag when running a container

71. What is the purpose of the ENV instruction in the Dockerfile?

ENV <key> <value>an ENV instruction sets the enviroment value to the key and it is available for the subsequent build steps and in the running container as well.

72. How to change the environment variables when running containers?

docker run --env <key>=<value>

73. What is the difference between ADD and COPY instructions?

ADD [--chown=<user>:<group>] <src>... <dest>The ADD instruction copies new files, directories or remote file URLs from <src> and adds them to the filesystem of the image at the path <dest>.COPY [--chown=<user>:<group>] <src>... <dest>The COPY instruction copies new files or directories from <src> and adds them to the filesystem of the container at the path <dest>.

74. What is ENTRYPOINT instruction in the Dockerfile?

An ENTRYPOINT allows you to configure a container that will run as an executable.Command line arguments to docker run <image> will be appended after all elements in an exec form ENTRYPOINT, and will override all elements specified using CMD.

75. How can you override the ENTRYPOINT instruction?

docker run --entrypoint

93. How to remove unused images?

docker image prune

94. How to see the history of the image?

docker image history

98. How to copy an image from the docker hub to a local repository?

// pull an image from the Docker Hub
docker pull ubuntu// tag an image
docker tag ubuntu:16.04 localhost:5000/my-ubuntu// push the image
docker push localhost:5000/my-ubuntu

100. How to display the layers of the Docker image?

docker image inspect //under Layers section

101. How to create a Docker image from archive or stdin?

docker image load// example
docker image load -i example.tar

103. Each layer is only a set of differences from the layer before it. The layers are stacked on top of each other. Is this statement about the image correct?

Yes

104. When you create a container It adds one writable layer on top of all the layers of the image. Is this statement about the image correct?

yes

116. How to pull an image from the repository?

docker pull [OPTIONS] NAME[:TAG|@DIGEST]// pulling from docker hub by default
docker pull debian// pulling from other repositories
docker pull myregistry.local:5000/testing/test-image

118. How to remove all images which are not used by existing containers?

docker image prune -a

120. How to remove an image?

docker rmi <IMAGE ID>

182. How to publish a port so that it can be accessed externally?

docker run -p 127.0.0.1:$HOSTPORT:$CONTAINERPORT --name CONTAINER -t <image>

239. What is the difference between bind mounts and volumes?

Volumes are completely managed by docker
Bind Mounts are dependent on the host directory structure

240. Volumes don’t increase the size of the containers. Is this statement correct and why?

Yes. Because volumes live outside of containers

241. What should we use if we want to persist the data beyond the lifecycle of the containers?

Volumes

242. How to create a Volume?

docker volume create my-volume

243. How to list docker volumes?

docker volume ls

244. How to inspect docker volume?

docker volume inspect my-vol

245. How to remove docker volumes?

docker volume rm my-vol

246. If you start a container with a volume that does not yet exist, Docker creates the volume for you. Is this statement correct?

Yes

247. How to create a volume myvol2 with a docker run?

docker run -d \
  --name devtest \
  -v myvol2:/app \
  nginx:latest

248. How to verify the volume is created with the container?

// Look for the mounts section
docker inspect devtest

249. How to create a volume with the --mount flag?

docker run -d \
  --name devtest \
  --mount source=myvol2,target=/app \
  nginx:latest

250. How to remove all unused images not just dangling images?

docker system prune --all
