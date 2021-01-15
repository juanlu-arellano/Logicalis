### Instrucciones Laboratorio 16

### Gitlab

En este Laboratorio pondremos en práctica algunas funciones básicas de GitLab

1. Entrar en `gitlab.com` y crear un usuario si no se tiene. Si se crea el usuario a partir del usuario de `github` luego se podrán crear proyectos a partir de los repositorios de que se tengan en `github`.

2. Añadir la clave ssh de la maquina donde tenemos nuestros repos para poder hacer pulls y push sin que nos pida identificarnos.

Ir a la opción `Menu de usuario -> Settings` , como se indica en la siguiente imagen:

![alt gitlab-user-settings][gitlab-user-settings]

[gitlab-user-settings]: ../imagenes/gitlab-user-settings.png

Opción `SSH Keys`:

![alt gitlab-user-settings-ssh][gitlab-user-settings-ssh]

[gitlab-user-settings-ssh]: ../imagenes/gitlab-user-settings-ssh.png

Generar la clave ssh las siguiendo las indicaciones:

![alt gitlab-user-settings-ssh-2][gitlab-user-settings-ssh-2]

[gitlab-user-settings-ssh-2]: ../imagenes/gitlab-user-settings-ssh-2.png

Una vez generada la clave copiarla siguiendo las indicaciones:

![alt gitlab-user-settings-ssh-3][gitlab-user-settings-ssh-3]

[gitlab-user-settings-ssh-3]: ../imagenes/gitlab-user-settings-ssh-3.png

3. Crear proyecto en Gitlab. Para crear un proyecto desde la opción `+ -> New Project`

Seleccionar `Crear un proyecto en blanco:`

![alt gitlab-project-1][gitlab-project-1]

[gitlab-project-1]: ../imagenes/gitlab-project-1.png

Dar un nombre al proyecto, indicar `Visibilidad Privada` y crearlo:

![alt gitlab-project-2][gitlab-project-2]

[gitlab-project-2]: ../imagenes/gitlab-project-2.png

3. Crear un nuevo repositorio local para el repositorio que acabamos de crear en vuestra maquina, siguiendo las indicaciones señaladas:

![alt gitlab-project-3][gitlab-project-3]

[gitlab-project-3]: ../imagenes/gitlab-project-3.png

4. Volver a `gitlab.com` y en la Opción `Project overview` comprobar que aparece el fichero `README.md` que acabamos de crear:

![alt gitlab-project-4][gitlab-project-4]

[gitlab-project-4]: ../imagenes/gitlab-project-4.png

5. A continuación vamos a crear una nueva rama en el repo desde `gitlab`:

![alt gitlab-branch-1][gitlab-branch-1]

[gitlab-branch-1]: ../imagenes/gitlab-branch-1.png

![alt gitlab-branch-2][gitlab-branch-2]

[gitlab-branch-2]: ../imagenes/gitlab-branch-2.png

6. El siguiente paso será proteger la rama `master` para que no se pueda hacer push a ella. Vamos al Menu de la izquierda `Settings->Repository` y expandimos la opción `Protected Branches`:

![alt gitlab-branch-3][gitlab-branch-3]

[gitlab-branch-3]: ../imagenes/gitlab-branch-3.png

Comprobamos que la rama `master` aparece protegida para que solo los `Mantainers` pueda hacer `merge` y `push`. Lo vamos a cambiar para que nadie pueda hacer `push`:

![alt gitlab-branch-4][gitlab-branch-4]

[gitlab-branch-4]: ../imagenes/gitlab-branch-4.png

7. Ahora desde nuestro repo local vamos a traer la rama que creamos en el repo remoto y nos movemos a ella:

       $ git pull
       From gitlab.com:lissettegar/formacion-gitlab
        * [new branch]      dev        -> origin/dev
       Already up to date.

       $ git log --graph --pretty --oneline
       * 22f0cbd (HEAD -> master, origin/master, origin/dev) add README

       $ git checkout dev
       Branch 'dev' set up to track remote branch 'dev' from 'origin'.
       Switched to a new branch 'dev'

8. Añadimos cambio desde la rama `dev` y lo subimos al repo remoto:

       $ echo "añadido desde la rama dev" >>dev.txt
       $ git add .
       $ git commit -m "nuevo cambio desde rama dev"
       [dev 633da56] nuevo cambio desde rama dev
        1 file changed, 1 insertion(+)
        create mode 100644 dev.txt
        $ git push origin dev
        Counting objects: 3, done.
        Delta compression using up to 8 threads.
        Compressing objects: 100% (2/2), done.
        Writing objects: 100% (3/3), 330 bytes | 330.00 KiB/s, done.
        Total 3 (delta 0), reused 0 (delta 0)
        remote:
        remote: To create a merge request for dev, visit:
        remote:   https://gitlab.com/lissettegar/formacion-gitlab/-/merge_requests/new?merge_request%5Bsource_branch%5D=dev
        remote:

        To gitlab.com:lissettegar/formacion-gitlab.git
           22f0cbd..633da56  dev -> dev

9. Nos movemos a la rama master y añadimos tambien un cambio e intentamos subirlo a la rama `master` y veremos que falla porque no tenemos permiso de hacer `push` a la rama `master`:

        $ git checkout master
        Switched to branch 'master'
        Your branch is up to date with 'origin/master'.
        $ echo "añadido desde la rama master" >>master.txt
        $ git add .
        $ git commit -m "nuevo cambio desde rama master"
        [master 732af77] nuevo cambio desde rama master
         1 file changed, 1 insertion(+)
         create mode 100644 master.txt

        $ git push origin master
        Counting objects: 3, done.
        Delta compression using up to 8 threads.
        Compressing objects: 100% (2/2), done.
        Writing objects: 100% (3/3), 335 bytes | 335.00 KiB/s, done.
        Total 3 (delta 0), reused 0 (delta 0)
        remote: GitLab: You are not allowed to push code to protected branches on this project.
        To gitlab.com:lissettegar/formacion-gitlab.git
         ! [remote rejected] master -> master (pre-receive hook declined)
        error: failed to push some refs to 'git@gitlab.com:lissettegar/formacion-gitlab.git'

10. Vamos a deshacer el commit que hemos hecho en la rama `master`. Hemos configurado que no se pueda hacer push, por lo que todas las integraciones a la rama `master` se tienen que hacer con `Merge Request`:

        $ git reset --hard HEAD^
        HEAD is now at 22f0cbd add README

11. En el paso 8 cuando hicimos el `push` de la rama `dev` vimos que nos aparecia un mensaje como este:

        remote: To create a merge request for dev, visit:
        remote:   https://gitlab.com/lissettegar/formacion-gitlab/-/merge_requests/new?merge_request%5Bsource_branch%5D=dev

 Si abrimos el enlace que os aparece o bien si vamos a gitlab en la vista del repo aparecerá una opción para crear un `Merge Request` para hacer un merge de la rama `dev` a la rama `master`    

![alt gitlab-branch-5][gitlab-branch-5]

[gitlab-branch-5]: ../imagenes/gitlab-branch-5.png

12. Ahora crearemos el `Merge Request` desde una de las 2 opciones anteriores, o tambien se puede hacer en la Opcion `Merge Request` en el Menu de la izquierda. Comprobaremos que en la parte superior indica que es un Merge desde la rama dev a la rama master y rellenar los campos señalado (desmarcar la opción de que se borre la rama después del merge) y pulsamos en el botón `Submit merge request`:

![alt gitlab-merge-1][gitlab-merge-1]

[gitlab-merge-1]: ../imagenes/gitlab-merge-1.png

13. Para aprobarla iremos a la opción `Merge Request` y la abriremos. Antes de dar al boton `Merge` podemos comprobar los cambios que se han hecho para ver si le damos el aprobado o no:

![alt gitlab-merge-3][gitlab-merge-3]

[gitlab-merge-3]: ../imagenes/gitlab-merge-3.png

14. Para aprobar o rechazar el Merge, volvemos a la pestaña `Overview`.

Si por ejemplo queremos rechazarlo, ponemos un comentario y pulsamos el botón `Comment & close merge request`. Con esto se cerrará el merge y una vez corregido el problema se haría un nuevo `Merge Request`:

![alt gitlab-merge-4][gitlab-merge-4]

[gitlab-merge-4]: ../imagenes/gitlab-merge-4.png

Si por el contrario esta todo bien y lo vamos a aprobar pulsamos el boton `Approve` luego en el boton `Merge` y se puede escribir un comentario y pulsar el boton `Comment`

![alt gitlab-merge-6][gitlab-merge-6]

[gitlab-merge-6]: ../imagenes/gitlab-merge-6.png

15. Por último si queremos traer a nuestro repo local los cambios de la rama master tendremos que hacer un git pull:

        $ git branch
          dev
        * master

        $ git pull
        remote: Enumerating objects: 1, done.
        remote: Counting objects: 100% (1/1), done.
        remote: Total 1 (delta 0), reused 0 (delta 0), pack-reused 0
        Unpacking objects: 100% (1/1), done.
        From gitlab.com:lissettegar/formacion-gitlab
           22f0cbd..690082e  master     -> origin/master
        Updating 22f0cbd..690082e
        Fast-forward
         dev.txt | 1 +
         1 file changed, 1 insertion(+)
         create mode 100644 dev.txt
