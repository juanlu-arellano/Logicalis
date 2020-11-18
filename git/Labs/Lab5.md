### Instrucciones Laboratorio 5

### Repositorios remotos

1. Crear un repositorio en github (se necesita tener una cuenta en github):

 ![alt github-1][github-1]

 [github-1]: ../imagenes/github-1.png

2. El resultado será similar a este:

 ![alt github-2][github-2]

 [github-2]: ../imagenes/github-2.png

3. El repositorio de los labs 2 y 3 no tiene configurado ningun repositorio remoto, comprobarlo y añadir como remoto el repositorio que acabamos de crear en github:

       $ git remote -v
       $ git remote add origin https://github.com/lissettegar/prueba.git
       $ git remote -v
       origin	https://github.com/lissettegar/prueba.git (fetch)
       origin	https://github.com/lissettegar/prueba.git (push)

4. A continuación haremos un push para sincronizar el repositorio remoto con los datos del repositorio local:

       $ git push origin master
       Username for 'https://github.com': lissettegar
       Password for 'https://lissettegar@github.com':
       Counting objects: 11, done.
       Delta compression using up to 8 threads.
       Compressing objects: 100% (6/6), done.
       Writing objects: 100% (11/11), 880 bytes | 880.00 KiB/s, done.
       Total 11 (delta 1), reused 0 (delta 0)
       remote: Resolving deltas: 100% (1/1), done.
       To https://github.com/lissettegar/prueba.git
        * [new branch]      master -> master

5. En este punto ya tenemos sincronizados los repositorios, a continuacion añadiremos un fichero en el repositorio remoto para simular un push de otra persona:

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
        [master 9ae72a9] Create file from-local.md
         1 file changed, 1 insertion(+)
         create mode 100644 from-local.md

7. Que pasará ahora si intentamos subir los cambios al repositorio remoto?

       $ git push origin master
       Username for 'https://github.com': lissettegar
       Password for 'https://lissettegar@github.com':
       To https://github.com/lissettegar/prueba.git
        ! [rejected]        master -> master (fetch first)
       error: failed to push some refs to 'https://github.com/lissettegar/prueba.git'
       hint: Updates were rejected because the remote contains work that you do
       hint: not have locally. This is usually caused by another repository pushing
       hint: to the same ref. You may want to first integrate the remote changes
       hint: (e.g., 'git pull ...') before pushing again.
       hint: See the 'Note about fast-forwards' in 'git push --help' for details.

8. Tal y como indica el mensaje anterior es necesario hacer un pull del repositorio remoto antes de subir nuestros cambios:

       $ git pull origin master
       Username for 'https://github.com': lissettegar
       Password for 'https://lissettegar@github.com':

    Al hacer el pull aparecerá un mensaje para añadir un mensaje al commit

       Merge branch 'master' of https://github.com/lissettegar/prueba

       # Please enter a commit message to explain why this merge is necessary,
       # especially if it merges an updated upstream into a topic branch.
       #
       # Lines starting with '#' will be ignored, and an empty message aborts
       # the commit.

    Añadir un commit message y guardar el fichero

       From https://github.com/lissettegar/prueba
        * branch            master     -> FETCH_HEAD
       Merge made by the 'recursive' strategy.
        from-github.md | 1 +
        1 file changed, 1 insertion(+)
        create mode 100644 from-github.md

9. Finalmente ya tenemos en local el fichero que añadimos en remoto y ya podemos iniciar el push:

       $ ll
       total 32
       drwxr-xr-x  3 lgarciap lgarciap 4096 Nov 18 15:51 ./
       drwxr-xr-x 28 lgarciap lgarciap 4096 Nov 18 13:13 ../
       -rw-r--r--  1 lgarciap lgarciap   28 Nov 18 10:37 cambios.txt
       -rw-r--r--  1 lgarciap lgarciap   21 Nov 18 15:51 from-github.md
       -rw-r--r--  1 lgarciap lgarciap   20 Nov 18 15:49 from-local.md
       drwxr-xr-x  8 lgarciap lgarciap 4096 Nov 18 15:52 .git/
       -rw-r--r--  1 lgarciap lgarciap   18 Nov 18 09:22 hello.txt
       -rw-r--r--  1 lgarciap lgarciap   13 Nov 18 10:38 version

       $ git push origin master
       Username for 'https://github.com': lissettegar
       Password for 'https://lissettegar@github.com':
       Counting objects: 5, done.
       Delta compression using up to 8 threads.
       Compressing objects: 100% (4/4), done.
       Writing objects: 100% (5/5), 566 bytes | 566.00 KiB/s, done.
       Total 5 (delta 2), reused 0 (delta 0)
       remote: Resolving deltas: 100% (2/2), completed with 1 local object.
       To https://github.com/lissettegar/prueba.git
          fd98b47..5670211  master -> master
                                                                    
