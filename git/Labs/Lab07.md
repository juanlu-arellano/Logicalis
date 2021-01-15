### Instrucciones Laboratorio 7

* Prerequisito: Haber terminado los labs anteriores.

### Añadir una nueva rama

1. Vamos a crear una nueva rama y ver que sucede. Lo primero que haremos será comprobar en que rama estamos y luego crearemos una rama de desarrollo.

       $ git branch -a
       * master
         remotes/origin/master

       $ git branch dev

2. Volvemos a comprobar en que rama estamos y comprobamos que seguimos posicionados en la rama `master`. El comando `git branch` no nos hace saltar de rama, solo la crea:

       $ git branch -a
         dev
       * master
         remotes/origin/master

3. Una vez creada la rama podemos ver que se han creado nuevos elementos en el directorio .git:

       $ tree .git
       .git
       ├── branches
       ├── COMMIT_EDITMSG
       ├── config
       ├── description
       ├── FETCH_HEAD
       ├── gitk.cache
       ...
       ├── index
       ├── info
       │   └── exclude
       ├── logs
       │   ├── HEAD
       │   └── refs
       │       ├── heads
       │       │   ├── dev
       │       │   └── master
       │       └── remotes
       │           └── origin
       │               └── master
       ...
       ├── ORIG_HEAD
       ├── pid
       └── refs
           ├── heads
           │   ├── dev
           │   └── master
           ├── remotes
           │   └── origin
           │       └── master
           └── tags
               ├── v1.0
               ├── v2.0
               └── v3.0

 Estos elementos son los ficheros .git/logs/refs/heads/dev y .git/refs/heads/dev

4. El archivo .git/refs/heads/dev apunta a la confirmación actual de la rama `dev`, que se corresponde con el último commit. A este mismo commit apunta la rama `master` y el `HEAD` apunta a la rama master:

       $ cat .git/refs/heads/dev
       a07178fa70319546a6e4c72b4893f9fa2d8b53a9

       $ cat .git/refs/heads/master
       a07178fa70319546a6e4c72b4893f9fa2d8b53a9

       $ cat .git/HEAD
       ref: refs/heads/master


5. Tambien podemos ver en los logs como ambas ramas apuntan a dicho commit:

       $ git log --oneline
       a07178f (HEAD -> master, tag: v3.0, origin/master, dev) version 3.0
       3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
       0e61dcc Create from github
       8b9e417 Create file from-local.md
       b986b73 (tag: v2.0) version 2.0
       aaae799 (tag: v1.0) version 1.0
       3a1666d commit hello.txt and cambios.txt

6. Si comprobamos el contenido de los ficheros `.git/logs/refs/heads/dev` y `.git/logs/refs/heads/master` podemos comprobar que cada uno contiene los logs de cada rama:

       $ cat .git/logs/refs/heads/dev
       0000000000000000000000000000000000000000 a07178fa70319546a6e4c72b4893f9fa2d8b53a9 Lissette García <lissette.garcia@es.logicalis.com> 1608723342 +0100	branch: Created from master

       $ cat .git/logs/refs/heads/master
       0000000000000000000000000000000000000000 9fb6511a8cceff613bd047d9ed05c0a4495b3b49 Lissette García <lissette.garcia@es.logicalis.com> 1608714591 +0100	commit (initial): commit hello.txt
       9fb6511a8cceff613bd047d9ed05c0a4495b3b49 3a1666d9149d5b135980c2ab4cdb833a973a4750 Lissette García <lissette.garcia@es.logicalis.com> 1608716814 +0100	commit (amend): commit hello.txt and cambios.txt
       3a1666d9149d5b135980c2ab4cdb833a973a4750 aaae799b5936836415e13d7e41117afe02cb83e9 Lissette García <lissette.garcia@es.logicalis.com> 1608718358 +0100	commit: version 1.0
       aaae799b5936836415e13d7e41117afe02cb83e9 b986b73fc0e81eb765d96057b46309efa8abe0c4 Lissette García <lissette.garcia@es.logicalis.com> 1608718537 +0100	commit: version 2.0
       b986b73fc0e81eb765d96057b46309efa8abe0c4 8b9e417db52fdc711a3463a472abb177c70c9581 Lissette García <lissette.garcia@es.logicalis.com> 1608719801 +0100	commit: Create file from-local.md
       8b9e417db52fdc711a3463a472abb177c70c9581 3c6a550790a4d09403c0603ccedf6c3e34c6749a Lissette García <lissette.garcia@es.logicalis.com> 1608720112 +0100	pull origin master: Merge made by the 'recursive' strategy.
       3c6a550790a4d09403c0603ccedf6c3e34c6749a a07178fa70319546a6e4c72b4893f9fa2d8b53a9 Lissette García <lissette.garcia@es.logicalis.com> 1608720848 +0100	commit: version 3.0

7. Esta es la situación actual graficamente. Ambas ramas apuntan al mismo commit y el `HEAD` apunta a la rama master porque aun no hemos saltado de rama:

 ![alt rama-1][rama-1]

 [rama-1]: ../imagenes/rama-1.png

8. A continuación nos movemos a la rama `dev`:

       $ git checkout dev
       Switched to branch 'dev'

9. Ahora podemos comprobar que la rama actual es la rama `dev` y a donde apunta el `HEAD`:

       $ git branch -a
       * dev
         master
         remotes/origin/master

       $ cat .git/HEAD
       ref: refs/heads/dev

 ![alt rama-2][rama-2]

 [rama-2]: ../imagenes/rama-2.png


10. A continuación vamos a añadir un fichero a la rama `dev`:

        $ echo "nueva info en rama dev" >> news.txt
        $ git status
        On branch dev
        Untracked files:
          (use "git add <file>..." to include in what will be committed)

        	news.txt

        nothing added to commit but untracked files present (use "git add" to track)

        $ git add .
        $ git status
        On branch dev
        Changes to be committed:
          (use "git reset HEAD <file>..." to unstage)

        	new file:   news.txt

        $ git commit -m "Update news"
        [dev 0e16342] Update news
         1 file changed, 1 insertion(+)
         create mode 100644 news.txt

11. Después de este commit la situación es la siguiente:

         $ git log --graph --pretty --oneline
         * 0e16342 (HEAD -> dev) Update news
         * a07178f (tag: v3.0, origin/master, master) version 3.0
         *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
         |\  
         | * 0e61dcc Create from github
         * | 8b9e417 Create file from-local.md
         |/  
         * b986b73 (tag: v2.0) version 2.0
         * aaae799 (tag: v1.0) version 1.0
         * 3a1666d commit hello.txt and cambios.txt

 ![alt rama-3][rama-3]

 [rama-3]: ../imagenes/rama-3.png

 12. Que pasa si ahora nos movemos a la rama master?

         $ git checkout master
         Switched to branch 'master'
         Your branch is up to date with 'origin/master'.

     Podemos ver que la rama actual vuelve a ser la `master`:

         $ git branch
           dev
         * master

     Que el `HEAD` apunta a la rama master:

         $ cat .git/HEAD
         ref: refs/heads/master

     Que no hay referencia a la rama `dev` en los logs:

         $ git log --graph --pretty --oneline
         * a07178f (HEAD -> master, tag: v3.0, origin/master) version 3.0
         *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
         |\  
         | * 0e61dcc Create from github
         * | 8b9e417 Create file from-local.md
         |/  
         * b986b73 (tag: v2.0) version 2.0
         * aaae799 (tag: v1.0) version 1.0
         * 3a1666d commit hello.txt and cambios.txt

     Y que el fichero que creamos en la rama dev `news.txt` no aparece en el directorio de trabajo:

         $ ll
         total 32
         drwxr-xr-x 3 lgarciap lgarciap 4096 Dec 23 12:52 ./
         drwxrwxr-x 7 lgarciap lgarciap 4096 Dec 23 10:56 ../
         -rw-r--r-- 1 lgarciap lgarciap   28 Dec 23 12:03 cambios.txt
         -rw-r--r-- 1 lgarciap lgarciap   21 Dec 23 12:03 from-github.md
         -rw-r--r-- 1 lgarciap lgarciap   20 Dec 23 12:03 from-local.md
         drwxr-xr-x 8 lgarciap lgarciap 4096 Dec 23 12:52 .git/
         -rw-r--r-- 1 lgarciap lgarciap   18 Dec 23 10:06 hello.txt
         -rw-r--r-- 1 lgarciap lgarciap   13 Dec 23 12:03 version

     De manera gráfica esta sería la situación:

   ![alt rama-4][rama-4]

   [rama-4]: ../imagenes/rama-4.png

13. En este punto vamos a añadir un nuevo fichero en la rama master:

        $ echo "nuevo commit rama master" >> new-commit.txt
        $ git status
        On branch master
        Your branch is up to date with 'origin/master'.

        Untracked files:
          (use "git add <file>..." to include in what will be committed)

        	new-commit.txt

        nothing added to commit but untracked files present (use "git add" to track)

        $ git add .

        $ git commit -m "nuevo commit rama master"
        [master 1e12a67] nuevo commit rama master
         1 file changed, 1 insertion(+)
         create mode 100644 new-commit.txt

        $ git status
        On branch master
        nothing to commit, working tree clean

        $ git log --graph --pretty --oneline
        * 1e12a67 (HEAD -> master) nuevo commit rama master
        * a07178f (tag: v3.0, origin/master) version 3.0
        *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
        |\  
        | * 0e61dcc Create from github
        * | 8b9e417 Create file from-local.md
        |/  
        * b986b73 (tag: v2.0) version 2.0
        * aaae799 (tag: v1.0) version 1.0
        * 3a1666d commit hello.txt and cambios.txt

14. Cual sería la situación actual?

 La rama `master` apunta ahora al nuevo commit:

        $ cat .git/refs/heads/master
        1e12a6755a9513876d638aba7999f89ee1656f86

 La rama `dev` sigue apuntando a su último commit:

        $ cat .git/refs/heads/dev
        0e163421dc15802ebc9d70fc3f70da60d5ba7dbe

 Y el `HEAD` continua apuntando a la rama `master`:

        $ cat .git/HEAD
        ref: refs/heads/master

  De manera gráfica:

 ![alt rama-5][rama-5]

 [rama-5]: ../imagenes/rama-5.png


15. Si saltaramos ahora a la rama `dev`, el HEAD se movería y en nuestro directorio de trabajo tendriamos unicamente los ficheros que contiene esa rama:

        $ git checkout dev
        Switched to branch 'dev'

        $ git log --graph --pretty --oneline
        * 0e16342 (HEAD -> dev) Update news
        * a07178f (tag: v3.0, origin/master) version 3.0
        *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
        |\  
        | * 0e61dcc Create from github
        * | 8b9e417 Create file from-local.md
        |/  
        * b986b73 (tag: v2.0) version 2.0
        * aaae799 (tag: v1.0) version 1.0
        * 3a1666d commit hello.txt and cambios.txt

        $ ll
        total 36
        drwxr-xr-x 3 lgarciap lgarciap 4096 Dec 23 12:58 ./
        drwxrwxr-x 7 lgarciap lgarciap 4096 Dec 23 10:56 ../
        -rw-r--r-- 1 lgarciap lgarciap   28 Dec 23 12:03 cambios.txt
        -rw-r--r-- 1 lgarciap lgarciap   21 Dec 23 12:03 from-github.md
        -rw-r--r-- 1 lgarciap lgarciap   20 Dec 23 12:03 from-local.md
        drwxr-xr-x 8 lgarciap lgarciap 4096 Dec 23 12:58 .git/
        -rw-r--r-- 1 lgarciap lgarciap   18 Dec 23 10:06 hello.txt
        -rw-r--r-- 1 lgarciap lgarciap   23 Dec 23 12:58 news.txt
        -rw-r--r-- 1 lgarciap lgarciap   13 Dec 23 12:03 version
