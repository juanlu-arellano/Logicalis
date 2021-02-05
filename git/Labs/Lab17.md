### Laboratorio 17

### Desplegar GitLab y gitlab runner en docker

1. Gitlab Con docker run:

       export GITLAB_HOME=/srv/gitlab

       sudo docker run --detach \
       --hostname ha1ocp \
       --publish 10.20.77.50:8443:443 \
       --publish 10.20.77.50:8090:8090 \
       --publish 10.20.77.50:2222:22 \
       --name gitlab \
       --privileged=true \
       --restart always \
       --volume $GITLAB_HOME/config:/etc/gitlab \
       --volume $GITLAB_HOME/logs:/var/log/gitlab \
       --volume $GITLAB_HOME/data:/var/opt/gitlab \
       gitlab/gitlab-ee:latest

       # docker exec -it gitlab /bin/bash
       # vi /etc/gitlab/gitlab.rb
       external_url "http://ha1ocp:8090"

       gitlab_rails['gitlab_shell_ssh_port'] = 2222

       # gitlab-ctl reconfigure

2. Gitlab con docker compose:

       $ vi docker-compose.yml
       web:
         image: 'gitlab/gitlab-ee:latest'
         container_name: 'gitlab'
         restart: always
         hostname: 'localhost'
         environment:
           GITLAB_OMNIBUS_CONFIG: |
             external_url 'http://localhost'
             gitlab_rails['smtp_enable'] = true
             gitlab_rails['smtp_address'] = "smtp.gmail.com"
             gitlab_rails['smtp_port'] = 587
             gitlab_rails['smtp_user_name'] = "user@gmail.com"
             gitlab_rails['smtp_password'] = "app-password"
             gitlab_rails['smtp_domain'] = "smtp.gmail.com"
             gitlab_rails['smtp_authentication'] = "login"
             gitlab_rails['smtp_enable_starttls_auto'] = true
             gitlab_rails['smtp_tls'] = false
             gitlab_rails['smtp_openssl_verify_mode'] = 'peer'
             # Add any other gitlab.rb configuration here, each on its own line
         ports:
           - '81:80'
           - '443:443'
           - '22:22'
           - '587:587'
         volumes:
           - '/srv/gitlab/config:/etc/gitlab'
           - '/srv/gitlab/logs:/var/log/gitlab'
           - '/srv/gitlab/data:/var/opt/gitlab'

3. Desplegar un Gitlab runner en docker:

       docker run -d --name gitlab-runner --restart always \
            -v /srv/gitlab-runner/config:/etc/gitlab-runner \
            -v /var/run/docker.sock:/var/run/docker.sock \
            gitlab/gitlab-runner:latest

4. Registrar el runner en gitlab:

       docker run --rm -it -v /srv/gitlab-runner/config:/etc/gitlab-runner gitlab/gitlab-runner register         

      4.1. Configuar la URL de gitlab

      4.2. Configurar el token del gitlab para los runners.

      4.3. Descripción del runner.

      4.4. Añadir un tag.

      4.5. Configurar el ejecutor del runner, en este caso `docker`.

      4.6. La imagen por defecto para el contenedor por si no se define en el fichero `.gitlab-ci.yml`. Por ejemplo centos:latest

5. Vamos a probar el runner con una aplicacion sencilla que lo que hace es desplegar una pagina de GitLab. Para ello usaremos el proyecto hello-world en `gitlab.com` y lo desplegaremos usando un runner de docker.
