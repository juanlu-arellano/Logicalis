### Instrucciones Laboratorio 5

* Prerequisito: Haber terminado los labs anteriores. No es indispensable pero si se han terminado los resultados de los comandos serán similares a los ejemplos.

### Repositorios remotos

1. Crear un repositorio en github (se necesita tener una cuenta en github):

 ![alt github-1][github-1]

 [github-1]: ../imagenes/github-1.png

2. El resultado será similar a este:

 ![alt github-2][github-2]

 [github-2]: ../imagenes/github-2.png

3. El repositorio de los labs anteriores (**formacion-git**) no tiene configurado ningun repositorio remoto, comprobarlo y añadir como remoto el repositorio que acabamos de crear en github:

       $ git remote -v
       $ git remote add origin https://github.com/lissettegar/formacion-git.git
       $ git remote -v
       origin	https://github.com/lissettegar/formacion-git.git (fetch)
       origin	https://github.com/lissettegar/formacion-git.git (push)

4. A continuación haremos un push para sincronizar el repositorio remoto con los datos del repositorio local:

       $ git push -u origin master
       Username for 'https://github.com': lissettegar
       Password for 'https://lissettegar@github.com':
       Counting objects: 11, done.
       Delta compression using up to 8 threads.
       Compressing objects: 100% (6/6), done.
       Writing objects: 100% (11/11), 881 bytes | 881.00 KiB/s, done.
       Total 11 (delta 1), reused 0 (delta 0)
       remote: Resolving deltas: 100% (1/1), done.
       To https://github.com/lissettegar/formacion-git.git
        * [new branch]      master -> master
       Branch 'master' set up to track remote branch 'master' from 'origin'.

5. En este punto ya tenemos sincronizados los repositorios, a continuacion añadiremos un fichero en el repositorio remoto para simular un push de otra persona (opción **Add File -> New File**) con el nombre y el contenido que se indica en la siguiente imagen, indicar un mensaje de commit y hacer click en **Commit new file**:

 ![alt github-3][github-3]

 [github-3]: ../imagenes/github-3.png

6. Tambien crearemos un fichero en el repositorio local y lo confirmaremos:

       $ git status
       On branch master
       nothing to commit, working tree clean

       $ echo '## hola desde local' > from-local.md

       $ git status
       On branch master
       Untracked files:
         (use "git add <file>..." to include in what will be committed)

       	from-local.md

       nothing added to commit but untracked files present (use "git add" to track)

       $ git add .
       $ git status
       On branch master
       Changes to be committed:
         (use "git reset HEAD <file>..." to unstage)

       	new file:   from-local.md

        $ git commit -m "Create file from-local.md"
        [master 8b9e417] Create file from-local.md
         1 file changed, 1 insertion(+)
         create mode 100644 from-local.md

7. Que pasará ahora si intentamos subir los cambios al repositorio remoto?

       $ git push origin master
       Username for 'https://github.com': lissettegar
       Password for 'https://lissettegar@github.com':
       To https://github.com/lissettegar/formacion-git.git
        ! [rejected]        master -> master (fetch first)
       error: failed to push some refs to 'https://github.com/lissettegar/formacion-git.git'
       hint: Updates were rejected because the remote contains work that you do
       hint: not have locally. This is usually caused by another repository pushing
       hint: to the same ref. You may want to first integrate the remote changes
       hint: (e.g., 'git pull ...') before pushing again.
       hint: See the 'Note about fast-forwards' in 'git push --help' for details.


8. Tal y como indica el mensaje anterior es necesario traer a nuestro repo local los cambios del repositorio remoto antes de subir nuestros cambios, lo haremos con un `git pull`. Al hacer el pull aparecerá un mensaje para añadir un mensaje al commit:

       $ git pull origin master

       Merge branch 'master' of https://github.com/lissettegar/formacion-git

       # Please enter a commit message to explain why this merge is necessary,
       # especially if it merges an updated upstream into a topic branch.
       #
       # Lines starting with '#' will be ignored, and an empty message aborts
       # the commit.

 Añadir un commit message y guardar el fichero `wq!`

       remote: Enumerating objects: 4, done.
       remote: Counting objects: 100% (4/4), done.
       remote: Compressing objects: 100% (2/2), done.
       remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
       Unpacking objects: 100% (3/3), done.
       From https://github.com/lissettegar/formacion-git
        * branch            master     -> FETCH_HEAD
          b986b73..0e61dcc  master     -> origin/master
       Merge made by the 'recursive' strategy.
        from-github.md | 1 +
        1 file changed, 1 insertion(+)
        create mode 100644 from-github.md

9. Finalmente ya tenemos en local el fichero que añadimos en remoto y ya podemos iniciar el push:

       $ ll
       total 32
       drwxr-xr-x 3 lgarciap lgarciap 4096 Dec 23 11:41 ./
       drwxrwxr-x 7 lgarciap lgarciap 4096 Dec 23 10:56 ../
       -rw-r--r-- 1 lgarciap lgarciap   28 Dec 23 11:14 cambios.txt
       -rw-r--r-- 1 lgarciap lgarciap   21 Dec 23 11:41 from-github.md
       -rw-r--r-- 1 lgarciap lgarciap   20 Dec 23 11:36 from-local.md
       drwxr-xr-x 8 lgarciap lgarciap 4096 Dec 23 11:42 .git/
       -rw-r--r-- 1 lgarciap lgarciap   18 Dec 23 10:06 hello.txt
       -rw-r--r-- 1 lgarciap lgarciap   13 Dec 23 11:14 version

       $ git push origin master
       Username for 'https://github.com': lissettegar
       Password for 'https://lissettegar@github.com':
       Counting objects: 5, done.
       Delta compression using up to 8 threads.
       Compressing objects: 100% (4/4), done.
       Writing objects: 100% (5/5), 570 bytes | 570.00 KiB/s, done.
       Total 5 (delta 2), reused 0 (delta 0)
       remote: Resolving deltas: 100% (2/2), completed with 1 local object.
       To https://github.com/lissettegar/formacion-git.git
          0e61dcc..3c6a550  master -> master
