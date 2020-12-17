### Instrucciones Laboratorio 9

* Prerequisito: Haber terminado los labs anteriores.

1. Desde fuera del diretorio donde tenenemos clonado el repositorio que hemos usado en los laboratorios previos, clonar el mismo repositorio de los labs anteriores pero esta vez le vamos a indicar en que directorio, por  ejemplo **prueba1** (Cambiar en el git clone el nombre de mi repo "https://github.com/lissettegar/prueba.git" por el vuestro):

       $ git clone https://github.com/lissettegar/prueba.git prueba1
       Cloning into 'prueba1'...
       Username for 'https://github.com': lissettegar
       Password for 'https://lissettegar@github.com':
       remote: Enumerating objects: 24, done.
       remote: Counting objects: 100% (24/24), done.
       remote: Compressing objects: 100% (13/13), done.
       remote: Total 24 (delta 5), reused 20 (delta 3), pack-reused 0
       Unpacking objects: 100% (24/24), done.

2. Comprobar los repositorios remotos añadidos al repositorio:

       $ cd prueba1/
       $ git remote -v
       origin	https://github.com/lissettegar/prueba.git (fetch)
       origin	https://github.com/lissettegar/prueba.git (push)

3. Comprobar que al clonar el repo solo aparece la información de la rama `master`, la rama `dev` la creamos en local en el laboratorio anterior pero aun no se ha subido al repositorio remoto. Con esto queremos simular que 2 personas estan trabajando sobre el mismo repositorio:

       $ git branch -vv
       * master 7ca0cfa [origin/master] version 3.0

4. Volver al repo con el que trabajamos en los labs anteriores **prueba** y comprobar que tenemos ambas ramas `master` y `dev`:

       $ cd ../prueba

       $ git branch -vv
         dev    5982dde Hola desde rama dev
       * master cbc85b3 Merge branch dev in master

5. Crear una rama remota para la rama `dev` en el repositorio remoto:

       $ git push origin dev
       Username for 'https://github.com': lissettegar
       Password for 'https://lissettegar@github.com':
       Counting objects: 6, done.
       Delta compression using up to 8 threads.
       Compressing objects: 100% (4/4), done.
       Writing objects: 100% (6/6), 602 bytes | 301.00 KiB/s, done.
       Total 6 (delta 2), reused 0 (delta 0)
       remote: Resolving deltas: 100% (2/2), completed with 1 local object.
       remote:
       remote: Create a pull request for 'dev' on GitHub by visiting:
       remote:      https://github.com/lissettegar/prueba/pull/new/dev
       remote:
       To https://github.com/lissettegar/prueba.git
        * [new branch]      dev -> dev

6. Cambiarse al otro directorio donde tenemos clonado el repo **prueba1** y traer la nueva rama remota que acabamos de crear en el paso anterior:

       $ cd ../prueba1

       $ git fetch origin
       Username for 'https://github.com': lissettegar
       Password for 'https://lissettegar@github.com':
       remote: Enumerating objects: 8, done.
       remote: Counting objects: 100% (8/8), done.
       remote: Compressing objects: 100% (2/2), done.
       remote: Total 6 (delta 2), reused 6 (delta 2), pack-reused 0
       Unpacking objects: 100% (6/6), done.
       From https://github.com/lissettegar/prueba
        * [new branch]      dev        -> origin/dev

7. Movernos a la rama `dev`:

       $ git checkout dev
       Branch 'dev' set up to track remote branch 'dev' from 'origin'.
       Switched to a new branch 'dev'
       1$ git branch -vv
       * dev    5982dde [origin/dev] Hola desde rama dev
         master 7ca0cfa [origin/master] version 3.0

8. Crear una nueva rama a partir de la rama `dev` que se llame `experimental`. Una vez en esta nueva rama añadiremos un nuevo fichero y haremos un commit:

       $ git checkout -b experimental
       Switched to a new branch 'experimental'

       $ echo "creando nueva rama experimental" >exp.txt

       $ git status
       On branch experimental
       Untracked files:
         (use "git add <file>..." to include in what will be committed)

       	exp.txt

       nothing added to commit but untracked files present (use "git add" to track)

       $ git add .
       $ git commit -m "New brach experimental"
       [experimental 01e8b20] New brach experimental
        1 file changed, 1 insertion(+)
        create mode 100644 exp.txt

       $ git log --graph --pretty --oneline
       * 01e8b20 (HEAD -> experimental, origin/experimental) New brach experimental
       * 5982dde (origin/dev, dev) Hola desde rama dev
       * 74c43a8 Update news
       * 7ca0cfa (tag: v3.0, origin/master, origin/HEAD, master) version 3.0
       *   5670211 Merge branch 'master' of https://github.com/lissettegar/prueba
       |\  
       | * fd98b47 Create from-github.md
       * | 9ae72a9 Create file from-local.md
       |/  
       * 4f73fa5 (tag: v2.0) version 2.0
       * eec021c (tag: v1.0) verion 1.0
       * a452287 commit hello.txt and cambios.txt

9. Publicar la rama o lo que es lo mismo crear una rama remota en el repositorio remoto. De esta manera tendremos una copia de nuestro trabajo en el repositorio remoto y además podemos compartirlo:

       $ git push origin experimental
       Username for 'https://github.com': lissettegar
       Password for 'https://lissettegar@github.com':
       Counting objects: 3, done.
       Delta compression using up to 8 threads.
       Compressing objects: 100% (2/2), done.
       Writing objects: 100% (3/3), 323 bytes | 323.00 KiB/s, done.
       Total 3 (delta 1), reused 0 (delta 0)
       remote: Resolving deltas: 100% (1/1), completed with 1 local object.
       remote:
       remote: Create a pull request for 'experimental' on GitHub by visiting:
       remote:      https://github.com/lissettegar/prueba/pull/new/experimental
       remote:
       To https://github.com/lissettegar/prueba.git
        * [new branch]      experimental -> experimental

10. Comprobar en `github` como se ven las ramas en modo gráfico, opción **Insights -> Network**:

![alt rama-remota][rama-remota]

[rama-remota]: ../imagenes/rama-remota.png

11. Traernos la información de todas los nuevos cambios que existen en el repositorio remoto al repo que tenemos clonado en el directorio **prueba**, en este caso la nueva rama remota:

        $ cd ../prueba1
        $ git fetch --all
        Fetching origin
        remote: Enumerating objects: 4, done.
        remote: Counting objects: 100% (4/4), done.
        remote: Compressing objects: 100% (1/1), done.
        remote: Total 3 (delta 1), reused 3 (delta 1), pack-reused 0
        Unpacking objects: 100% (3/3), done.
        From https://github.com/lissettegar/prueba
         * [new branch]      experimental -> origin/experimental

12. Saltamos a la nueva rama `experimental` y comprobamos el contenido del fichero que creamos en esa rama en un paso anterior:

        $ git checkout experimental
        Branch 'experimental' set up to track remote branch 'experimental' from 'origin'.
        Switched to a new branch 'experimental'
        $ git branch -vv
          dev          5982dde Hola desde rama dev
        * experimental 01e8b20 [origin/experimental] New brach experimental
          master       cbc85b3 Merge branch dev in master
        $ git status
        On branch experimental
        Your branch is up to date with 'origin/experimental'.

        nothing to commit, working tree clean

        $ ll
        total 40
        drwxr-xr-x  3 lgarciap lgarciap 4096 Dec 14 18:08 ./
        drwxr-xr-x 31 lgarciap lgarciap 4096 Dec 14 15:21 ../
        -rw-r--r--  1 lgarciap lgarciap   28 Nov 19 12:20 cambios.txt
        -rw-r--r--  1 lgarciap lgarciap   32 Dec 14 18:08 exp.txt
        -rw-r--r--  1 lgarciap lgarciap   21 Nov 19 12:20 from-github.md
        -rw-r--r--  1 lgarciap lgarciap   20 Nov 19 12:20 from-local.md
        drwxr-xr-x  9 lgarciap lgarciap 4096 Dec 14 18:08 .git/
        -rw-r--r--  1 lgarciap lgarciap   14 Dec 14 17:29 hello.txt
        -rw-r--r--  1 lgarciap lgarciap   23 Nov 24 18:17 news.txt
        -rw-r--r--  1 lgarciap lgarciap   13 Nov 19 12:20 version

        $ cat exp.txt
        creando nueva rama experimental

13. La rama `experimental` ya ha cumplido su cometido y queremos fusionarla con la rama `dev`. Para ello desde el repo **prueba1** nos moveremos a la rama `dev` y haremos un `merge` de la rama `experimental`:

        $ cd ../prueba1
        $ git branch
          dev
        * experimental
          master

        $ git checkout dev
        Switched to branch 'dev'
        Your branch is up to date with 'origin/dev'.

        $ git log --graph --pretty --oneline
        * 5982dde (HEAD -> dev, origin/dev) Hola desde rama dev
        * 74c43a8 Update news
        * 7ca0cfa (tag: v3.0, origin/master, origin/HEAD, master) version 3.0
        *   5670211 Merge branch 'master' of https://github.com/lissettegar/prueba
        |\  
        | * fd98b47 Create from-github.md
        * | 9ae72a9 Create file from-local.md
        |/  
        * 4f73fa5 (tag: v2.0) version 2.0
        * eec021c (tag: v1.0) verion 1.0
        * a452287 commit hello.txt and cambios.txt

        $ git merge experimental
        Updating 5982dde..01e8b20
        Fast-forward
         exp.txt | 1 +
         1 file changed, 1 insertion(+)
         create mode 100644 exp.txt

14. Comprobamos como después del merge ambas ramas locales `dev` y `experimental` apuntan al mismo commit `01e8b20`. Con un `git status` veremos que git nos avisa de que nuestra rama `dev` esta un commit por delante de la rama remota `origin\dev` y que es necesario hacer un git push para actualizar la rama remota:

        $ git log --graph --pretty --oneline
        * 01e8b20 (HEAD -> dev, origin/experimental, experimental) New brach experimental
        * 5982dde (origin/dev) Hola desde rama dev
        * 74c43a8 Update news
        * 7ca0cfa (tag: v3.0, origin/master, origin/HEAD, master) version 3.0
        *   5670211 Merge branch 'master' of https://github.com/lissettegar/prueba
        |\  
        | * fd98b47 Create from-github.md
        * | 9ae72a9 Create file from-local.md
        |/  
        * 4f73fa5 (tag: v2.0) version 2.0
        * eec021c (tag: v1.0) verion 1.0
        * a452287 commit hello.txt and cambios.txt

        $ git status
        On branch dev
        Your branch is ahead of 'origin/dev' by 1 commit.
          (use "git push" to publish your local commits)

        nothing to commit, working tree clean

15. Subimos los cambios al repositorio remoto para que quede sincronizado con nuestro repo local y todas las ramas menos la master apuntarán al último commit `01e8b20`:

        $ git push origin dev
        Username for 'https://github.com': lissettegar
        Password for 'https://lissettegar@github.com':
        Total 0 (delta 0), reused 0 (delta 0)
        To https://github.com/lissettegar/prueba.git
           5982dde..01e8b20  dev -> dev

        $ git log --graph --pretty --oneline
        * 01e8b20 (HEAD -> dev, origin/experimental, origin/dev, experimental) New brach experimental
        * 5982dde Hola desde rama dev
        * 74c43a8 Update news
        * 7ca0cfa (tag: v3.0, origin/master, origin/HEAD, master) version 3.0
        *   5670211 Merge branch 'master' of https://github.com/lissettegar/prueba
        |\  
        | * fd98b47 Create from-github.md
        * | 9ae72a9 Create file from-local.md
        |/  
        * 4f73fa5 (tag: v2.0) version 2.0
        * eec021c (tag: v1.0) verion 1.0
        * a452287 commit hello.txt and cambios.txt

16. Comprobar el movimiento de los punteros de las ramas en GitHub:

![alt rama-remota-1][rama-remota-1]

[rama-remota-1]: ../imagenes/rama-remota-1.png

17. A continuación nos moveremos al repo **prueba** y lo actualizaremos con los cambios del repositorio remoto con un `git fetch`. Antes del `git fetch` veremos que nuestra rama `dev` sigue apuntando al commit anterior

        $ cd ../prueba

        $ git log --graph --pretty --oneline
        * 01e8b20 (HEAD -> experimental, origin/experimental) New brach experimental
        * 5982dde (origin/dev, dev) Hola desde rama dev
        * 74c43a8 Update news
        * 7ca0cfa (tag: v3.0, origin/master, origin/HEAD, master) version 3.0
        *   5670211 Merge branch 'master' of https://github.com/lissettegar/prueba
        |\  
        | * fd98b47 Create from-github.md
        * | 9ae72a9 Create file from-local.md
        |/  
        * 4f73fa5 (tag: v2.0) version 2.0
        * eec021c (tag: v1.0) verion 1.0
        * a452287 commit hello.txt and cambios.txt

        $ git fetch origin
        From https://github.com/lissettegar/prueba
           5982dde..01e8b20  dev        -> origin/dev

18. Una vez traidos los cambios del repositorio remoto ambas ramas remotas `dev` y `experimental` apuntan al mismo commit, pero la rama `dev` local sigue apuntando a un commit anterior:

        $ git log --graph --pretty --oneline
        * 01e8b20 (HEAD -> experimental, origin/experimental, origin/dev) New brach experimental
        * 5982dde (dev) Hola desde rama dev
        * 74c43a8 Update news
        * 7ca0cfa (tag: v3.0, origin/master, origin/HEAD, master) version 3.0
        *   5670211 Merge branch 'master' of https://github.com/lissettegar/prueba
        |\  
        | * fd98b47 Create from-github.md
        * | 9ae72a9 Create file from-local.md
        |/  
        * 4f73fa5 (tag: v2.0) version 2.0
        * eec021c (tag: v1.0) verion 1.0
        * a452287 commit hello.txt and cambios.txt

19. Si hacemos un `checkout` a la rama `dev` nos indicará que estamos un commit por detras de la rama remota y que tenemos que hacer un git pull para traernos los cambios:

        $ git checkout dev
        Switched to branch 'dev'
        Your branch is behind 'origin/dev' by 1 commit, and can be fast-forwarded.
          (use "git pull" to update your local branch)

        $ git pull origin dev
        From https://github.com/lissettegar/prueba
         * branch            dev        -> FETCH_HEAD
        Updating 5982dde..01e8b20
        Fast-forward
         exp.txt | 1 +
         1 file changed, 1 insertion(+)
         create mode 100644 exp.txt

20. Despues del git pull podemos comprobar como el puntero de la rama `dev` se ha movido:

        $ git log --graph --pretty --oneline
        * 01e8b20 (HEAD -> dev, origin/experimental, origin/dev, experimental) New brach experimental
        * 5982dde Hola desde rama dev
        * 74c43a8 Update news
        * 7ca0cfa (tag: v3.0, origin/master, origin/HEAD, master) version 3.0
        *   5670211 Merge branch 'master' of https://github.com/lissettegar/prueba
        |\  
        | * fd98b47 Create from-github.md
        * | 9ae72a9 Create file from-local.md
        |/  
        * 4f73fa5 (tag: v2.0) version 2.0
        * eec021c (tag: v1.0) verion 1.0
        * a452287 commit hello.txt and cambios.txt

21. La rama `experimental` ya no la necesitamos así que la podemos borrar tanto en local como en remoto:

        $ git push origin --delete experimental
        Username for 'https://github.com': lissettegar
        Password for 'https://lissettegar@github.com':
        To https://github.com/lissettegar/prueba.git
         - [deleted]         experimental

        $ git branch -d experimental
        Deleted branch experimental (was 01e8b20).

22. Borrar el repositorio clonado en  **prueba1**:

        $ rm -rf prueba1
