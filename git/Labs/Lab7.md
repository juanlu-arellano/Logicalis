### Instrucciones Laboratorio 7

* Prerequisito: Haber terminado los labs anteriores.

### Añadir una nueva rama


1. Vamos a crear una nueva rama y ver que sucede. Lo primero que haremos será comprobar en que rama estamos y luego crearemos una rama de desarrollo.

       $ git branch -a
       * master
         remotes/origin/master

       $ git branch dev

2. Volvemos a comprobar en que rama estamos y comprobamos que seguimos posicionados en la rama `master`. El comando `git branch` no nos hace saltar de rama:

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
       7ca0cfa0e74f83eaa67b6ea56d38a63ad13d8d99

       $ cat .git/refs/heads/master
       7ca0cfa0e74f83eaa67b6ea56d38a63ad13d8d99

       $ cat .git/HEAD
       ref: refs/heads/master


5. Tambien podemos ver en los logs como ambas ramas apuntan a dicho commit:

       $ git log --oneline
       7ca0cfa (HEAD -> master, tag: v3.0, origin/master, dev) version 3.0
       5670211 Merge branch 'master' of https://github.com/lissettegar/prueba
       9ae72a9 Create file from-local.md
       fd98b47 Create from-github.md
       4f73fa5 (tag: v2.0) version 2.0
       eec021c (tag: v1.0) verion 1.0
       a452287 commit hello.txt and cambios.txt

6. Si comprobamos el contenido de los ficheros `.git/logs/refs/heads/dev` y `.git/logs/refs/heads/master` podemos comprobar que cada uno contiene los logs de cada rama:

       $ cat .git/logs/refs/heads/dev
       0000000000000000000000000000000000000000 7ca0cfa0e74f83eaa67b6ea56d38a63ad13d8d99 Lissette García <lissette.garcia@es.logicalis.com> 1606209248 +0100	branch: Created from master

       $ cat .git/logs/refs/heads/master
       0000000000000000000000000000000000000000 f54869598dea474ea9fc8a41bd7cdd016d3e7de9 Lissette García <lissette.garcia@es.logicalis.com> 1605687811 +0100	commit (initial): commit hello.txt
       f54869598dea474ea9fc8a41bd7cdd016d3e7de9 a4522872d7c80eef8e17b3d12baa81ba53b81008 Lissette García <lissette.garcia@es.logicalis.com> 1605688259 +0100	commit (amend): commit hello.txt and cambios.txt
       a4522872d7c80eef8e17b3d12baa81ba53b81008 eec021cc70adec40e44c2cdead96d3efa2100606 Lissette García <lissette.garcia@es.logicalis.com> 1605692194 +0100	commit: verion 1.0
       eec021cc70adec40e44c2cdead96d3efa2100606 4f73fa5f9d9350f5bf8b3747967f28b173f18f0b Lissette García <lissette.garcia@es.logicalis.com> 1605692360 +0100	commit: version 2.0
       4f73fa5f9d9350f5bf8b3747967f28b173f18f0b 9ae72a99243d92f38ee0c7bf34c2381206d990e2 Lissette García <lissette.garcia@es.logicalis.com> 1605711006 +0100	commit: Create file from-local.md
       9ae72a99243d92f38ee0c7bf34c2381206d990e2 5670211fc758e8d16faab39760479d0417459962 Lissette García <lissette.garcia@es.logicalis.com> 1605711111 +0100	pull origin master: Merge made by the 'recursive' strategy.
       5670211fc758e8d16faab39760479d0417459962 7ca0cfa0e74f83eaa67b6ea56d38a63ad13d8d99 Lissette García <lissette.garcia@es.logicalis.com> 1605782313 +0100	commit: version 3.0

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
        [dev 74c43a8] Update news
         1 file changed, 1 insertion(+)
         create mode 100644 news.txt

11. Después de este commit la situación es la siguiente:

         $ git log --graph --pretty --oneline
        * 74c43a8 (HEAD -> dev) Update news
        * 7ca0cfa (tag: v3.0, origin/master, master) version 3.0
        *   5670211 Merge branch 'master' of https://github.com/lissettegar/prueba
        |\  
        | * fd98b47 Create from-github.md
        * | 9ae72a9 Create file from-local.md
        |/  
        * 4f73fa5 (tag: v2.0) version 2.0
        * eec021c (tag: v1.0) verion 1.0
        * a452287 commit hello.txt and cambios.txt


 ![alt rama-3][rama-3]

 [rama-3]: ../imagenes/rama-3.png

 12. Que pasa si ahora nos movemos a la rama master?

         $ git checkout master
         Switched to branch 'master'

     Podemos ver que la rama actual vuelve a ser la `master`:

         $ git branch
           dev
         * master

     Que el `HEAD` apunta a la rama master:

         $ cat .git/HEAD
         ref: refs/heads/master

     Que no hay referencia a la rama `dev` en los logs:

         $ git log --graph --pretty --oneline
         * 7ca0cfa (HEAD -> master, tag: v3.0, origin/master) version 3.0
         *   5670211 Merge branch 'master' of https://github.com/lissettegar/prueba
         |\  
         | * fd98b47 Create from-github.md
         * | 9ae72a9 Create file from-local.md
         |/  
         * 4f73fa5 (tag: v2.0) version 2.0
         * eec021c (tag: v1.0) verion 1.0
         * a452287 commit hello.txt and cambios.txt

     Y que el fichero que creamos en la rama dev `news.txt` no aparece en el directorio de trabajo:

         $ ll
         total 32
         drwxr-xr-x  3 lgarciap lgarciap 4096 Nov 24 12:52 ./
         drwxr-xr-x 29 lgarciap lgarciap 4096 Nov 19 17:02 ../
         -rw-r--r--  1 lgarciap lgarciap   28 Nov 19 12:20 cambios.txt
         -rw-r--r--  1 lgarciap lgarciap   21 Nov 19 12:20 from-github.md
         -rw-r--r--  1 lgarciap lgarciap   20 Nov 19 12:20 from-local.md
         drwxr-xr-x  9 lgarciap lgarciap 4096 Nov 24 12:52 .git/
         -rw-r--r--  1 lgarciap lgarciap   18 Nov 18 09:22 hello.txt
         -rw-r--r--  1 lgarciap lgarciap   13 Nov 19 12:20 version

     De manera gráfica esta sería la situación:

   ![alt rama-4][rama-4]

   [rama-4]: ../imagenes/rama-4.png

13. En este punto vamos a añadir un nuevo fichero en la rama master:

        $ echo "nuevo commit rama master" >> new-commit.txt
        lgarciap@lgarciap-ThinkPad-T480:~/Documents/REPOS/prueba$ git status
        On branch master
        Untracked files:
          (use "git add <file>..." to include in what will be committed)

        	new-commit.txt

        nothing added to commit but untracked files present (use "git add" to track)

        $ git add .

        $ git commit -m "nuevo commit rama master"
        [master e1ccad3] nuevo commit rama master
         1 file changed, 1 insertion(+)
         create mode 100644 new-commit.txt

        $ git status
        On branch master
        nothing to commit, working tree clean

        $ git log --graph --pretty --oneline
        * e1ccad3 (HEAD -> master) nuevo commit rama master
        * 7ca0cfa (tag: v3.0, origin/master) version 3.0
        *   5670211 Merge branch 'master' of https://github.com/lissettegar/prueba
        |\  
        | * fd98b47 Create from-github.md
        * | 9ae72a9 Create file from-local.md
        |/  
        * 4f73fa5 (tag: v2.0) version 2.0
        * eec021c (tag: v1.0) verion 1.0
        * a452287 commit hello.txt and cambios.txt

14. Cual sería la situación actual?

 La rama `master` apunta ahora al nuevo commit:

        $ cat .git/refs/heads/master
        e1ccad34ccc21fcc0f552f715927d9187dd8f0bd

 La rama `dev` sigue apuntando a su último commit:

        $ cat .git/refs/heads/dev
        74c43a83a4bf72c37db6d945d60429f24c1d08a6

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
        * 74c43a8 (HEAD -> dev) Update news
        * 7ca0cfa (tag: v3.0, origin/master) version 3.0
        *   5670211 Merge branch 'master' of https://github.com/lissettegar/prueba
        |\  
        | * fd98b47 Create from-github.md
        * | 9ae72a9 Create file from-local.md
        |/  
        * 4f73fa5 (tag: v2.0) version 2.0
        * eec021c (tag: v1.0) verion 1.0
        * a452287 commit hello.txt and cambios.txt

        $ ll
        total 36
        drwxr-xr-x  3 lgarciap lgarciap 4096 Nov 24 13:22 ./
        drwxr-xr-x 29 lgarciap lgarciap 4096 Nov 19 17:02 ../
        -rw-r--r--  1 lgarciap lgarciap   28 Nov 19 12:20 cambios.txt
        -rw-r--r--  1 lgarciap lgarciap   21 Nov 19 12:20 from-github.md
        -rw-r--r--  1 lgarciap lgarciap   20 Nov 19 12:20 from-local.md
        drwxr-xr-x  9 lgarciap lgarciap 4096 Nov 24 13:22 .git/
        -rw-r--r--  1 lgarciap lgarciap   18 Nov 18 09:22 hello.txt
        -rw-r--r--  1 lgarciap lgarciap   23 Nov 24 13:22 news.txt
        -rw-r--r--  1 lgarciap lgarciap   13 Nov 19 12:20 version
