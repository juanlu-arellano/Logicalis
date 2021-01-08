### Instrucciones Laboratorio 10

* Prerequisito: Haber terminado los labs anteriores.

1. Nos posicionamos en el repo **formacion-git** y nos movemos a la rama master. La situación de partida es la siguiente:

       $ cd formacion-git
       $ git checkout master
       Switched to branch 'master'
       Your branch is ahead of 'origin/master' by 6 commits.
         (use "git push" to publish your local commits)

       $ git log --graph --pretty --oneline
       *   059c912 (HEAD -> master) Merge branch dev in master
       |\  
       | * 187e4dd Hola desde rama dev
       | * 0e16342 Update news
       * | 91f9d87 Hola desde rama master
       * | b27cdea fix hecho en la rama fix-01
       * | 1e12a67 nuevo commit rama master
       |/  
       * a07178f (tag: v3.0, origin/master) version 3.0
       *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
       |\  
       | * 0e61dcc Create from github
       * | 8b9e417 Create file from-local.md
       |/  
       * b986b73 (tag: v2.0) version 2.0
       * aaae799 (tag: v1.0) version 1.0
       * 3a1666d commit hello.txt and cambios.txt

   ![alt rama-9][rama-9]

   [rama-9]: ../imagenes/rama-9.png

2. Tenemos 2 ramas, la rama master y la rama dev:

       $ git branch -v
       dev    c040330 New brach experimental
       * master 059c912 [ahead 6] Merge branch dev in master

3. Comprobemos el estado del repositorio local, si hay cambios pendiente de subir a nuestro repositorio remoto, los subimos con un `git push`:

       $ git push origin master

       Username for 'https://github.com': lissettegar
       Password for 'https://lissettegar@github.com':
       Counting objects: 12, done.
       Delta compression using up to 8 threads.
       Compressing objects: 100% (9/9), done.
       Writing objects: 100% (12/12), 1.14 KiB | 1.14 MiB/s, done.
       Total 12 (delta 4), reused 0 (delta 0)
       remote: Resolving deltas: 100% (4/4), completed with 1 local object.
       To https://github.com/lissettegar/formacion-git.git
          a07178f..059c912  master -> master

4. La rama `master` contiene cambios que no están en la rama de `dev` y necesitamos actualizarla, así que haremos un merge de la rama `master` a la rama `dev`:

       $ git checkout dev
       Switched to a new branch 'dev'

       $ git branch -av
       * dev                   c040330 New brach experimental
         master                059c912 Merge branch dev in master
         remotes/origin/dev    c040330 New brach experimental
         remotes/origin/master 059c912 Merge branch dev in master

       $ git merge master
       Merge made by the 'recursive' strategy.
        hello.txt      | 1 +
        new-commit.txt | 2 ++
        2 files changed, 3 insertions(+)
        create mode 100644 new-commit.txt

5. Comprobemos el historico:

       $ git log --graph --pretty --oneline
       *   3a4c264 (HEAD -> dev) Merge branch 'master' into dev
       |\  
       | *   059c912 (origin/master, master) Merge branch dev in master
       | |\  
       | * | 91f9d87 Hola desde rama master
       | * | b27cdea fix hecho en la rama fix-01
       | * | 1e12a67 nuevo commit rama master
       * | | c040330 (origin/dev) New brach experimental
       | |/  
       |/|   
       * | 187e4dd Hola desde rama dev
       * | 0e16342 Update news
       |/  
       * a07178f (tag: v3.0) version 3.0
       *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
       |\  
       | * 0e61dcc Create from github
       * | 8b9e417 Create file from-local.md
       |/  
       * b986b73 (tag: v2.0) version 2.0
       * aaae799 (tag: v1.0) version 1.0
       * 3a1666d commit hello.txt and cambios.txt

  ![alt rama-10][rama-10]

  [rama-10]: ../imagenes/rama-10.png

6. Vamos a crear una nueva rama para una nuevo fix sobre la rama `dev`:

       $ git checkout -b fix-02
       Switched to a new branch 'fix-02'

7. Hacemos los cambios necesarios para este nuevo fix y los confirmamos:

       $ echo "fix hecho en la rama fix-02" >> new-commit.txt
       lgarciap@lgarciap-ThinkPad-T480:~/Documents/MAYORAL/FORMACION/formacion-git$ cat new-commit.txt
       nuevo commit rama master
       fix hecho en la rama fix-01
       fix hecho en la rama fix-02

       $ git status
       On branch fix-02
       Changes not staged for commit:
         (use "git add <file>..." to update what will be committed)
         (use "git checkout -- <file>..." to discard changes in working directory)

       	modified:   new-commit.txt

       no changes added to commit (use "git add" and/or "git commit -a")

       $ git add .

       $ git commit -m "fix hecho en la rama fix-02"
       [fix-02 9866522] fix hecho en la rama fix-02
        1 file changed, 1 insertion(+)

8. Observemos el historial. Estamos en la rama `fix-02` en el último commit:

       $ git log --graph --pretty --oneline
       * 9866522 (HEAD -> fix-02) fix hecho en la rama fix-02
       *   3a4c264 (dev) Merge branch 'master' into dev
       |\  
       | *   059c912 (origin/master, master) Merge branch dev in master
       | |\  
       | * | 91f9d87 Hola desde rama master
       | * | b27cdea fix hecho en la rama fix-01
       | * | 1e12a67 nuevo commit rama master
       * | | c040330 (origin/dev) New brach experimental
       | |/  
       |/|   
       * | 187e4dd Hola desde rama dev
       * | 0e16342 Update news
       |/  
       * a07178f (tag: v3.0) version 3.0
       *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
       |\  
       | * 0e61dcc Create from github
       * | 8b9e417 Create file from-local.md
       |/  
       * b986b73 (tag: v2.0) version 2.0
       * aaae799 (tag: v1.0) version 1.0
       * 3a1666d commit hello.txt and cambios.txt

9. Por otro lado, la rama de desarrollo tambien tiene nuevos cambios:

       $ git checkout dev
       Switched to branch 'dev'

       $ echo "tercera linea" >> cambios.txt

       $ cat cambios.txt
       primera linea
       segunda linea
       tercera linea

10. Confirmamos dichos cambios:

        $ git status
        On branch dev
        Changes not staged for commit:
          (use "git add <file>..." to update what will be committed)
          (use "git checkout -- <file>..." to discard changes in working directory)

       	modified:   cambios.txt

        no changes added to commit (use "git add" and/or "git commit -a")

        $ git add .

        $ git commit -m "tercera linea en cambios.txt"
        [dev d14c280] tercera linea en cambios.txt
         1 file changed, 1 insertion(+)


11. Observemos el historial la creación del nuevo commit en la rama `dev`:

        $ git log --graph --pretty --oneline
        * d14c280 (HEAD -> dev) tercera linea en cambios.txt
        *   3a4c264 Merge branch 'master' into dev
        |\  
        | *   059c912 (origin/master, master) Merge branch dev in master
        | |\  
        | * | 91f9d87 Hola desde rama master
        | * | b27cdea fix hecho en la rama fix-01
        | * | 1e12a67 nuevo commit rama master
        * | | c040330 (origin/dev) New brach experimental
        | |/  
        |/|   
        * | 187e4dd Hola desde rama dev
        * | 0e16342 Update news
        |/  
        * a07178f (tag: v3.0) version 3.0
        *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
        |\  
        | * 0e61dcc Create from github
        * | 8b9e417 Create file from-local.md
        |/  
        * b986b73 (tag: v2.0) version 2.0
        * aaae799 (tag: v1.0) version 1.0
        * 3a1666d commit hello.txt and cambios.txt

 Esto es lo que tenemos graficamente:

 ![alt rama-11][rama-11]

 [rama-11]: ../imagenes/rama-11.png

12. A continuación queremos incluir los cambios de la rama `fix-02` en la rama `dev`, pero en esta ocasión no queremos hacer un merge, sino que queremos reorganizar (**rebase**). Para ello deberemos movernos hacia la rama `fix-02` y desde ahí hacer el rebase:

        $ git checkout fix-02
        Switched to branch 'fix-02'

        $ git rebase dev
        First, rewinding head to replay your work on top of it...
        Applying: fix hecho en la rama fix-02

13. En el historico podemos ver como tras el rebase el historial es lineal, la divergencia que habiamos creado al crear la rama `fix-02` y tener cambios en ambas ramas ha desaparecido y en su lugar se ha creado un nuevo commit con los cambios de la rama `fix-02` sobre el último commit de la rama `dev`:

        $ git log --graph --pretty --oneline
        * cb4cb70 (HEAD -> fix-02) fix hecho en la rama fix-02
        * d14c280 (dev) tercera linea en cambios.txt
        *   3a4c264 Merge branch 'master' into dev
        |\  
        | *   059c912 (origin/master, master) Merge branch dev in master
        | |\  
        | * | 91f9d87 Hola desde rama master
        | * | b27cdea fix hecho en la rama fix-01
        | * | 1e12a67 nuevo commit rama master
        * | | c040330 (origin/dev) New brach experimental
        | |/  
        |/|   
        * | 187e4dd Hola desde rama dev
        * | 0e16342 Update news
        |/  
        * a07178f (tag: v3.0) version 3.0
        *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
        |\  
        | * 0e61dcc Create from github
        * | 8b9e417 Create file from-local.md
        |/  
        * b986b73 (tag: v2.0) version 2.0
        * aaae799 (tag: v1.0) version 1.0
        * 3a1666d commit hello.txt and cambios.txt

 Quedando así:

 ![alt rama-12][rama-12]

 [rama-12]: ../imagenes/rama-12.png

 14. A continuación si hacemos un merge desde la rama `dev` de la rama `fix-02`, lo que estamos haciendo es mover el puntero de la rama `dev` al último commit, sería un Fast-forward:

         $ git checkout dev
         Switched to branch 'dev'

         $ git log --graph --pretty --oneline
         * d14c280 (HEAD -> dev) tercera linea en cambios.txt
         *   3a4c264 Merge branch 'master' into dev
         |\  
         | *   059c912 (origin/master, master) Merge branch dev in master
         | |\  
         | * | 91f9d87 Hola desde rama master
         | * | b27cdea fix hecho en la rama fix-01
         | * | 1e12a67 nuevo commit rama master
         * | | c040330 (origin/dev) New brach experimental
         | |/  
         |/|   
         * | 187e4dd Hola desde rama dev
         * | 0e16342 Update news
         |/  
         * a07178f (tag: v3.0) version 3.0
         *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
         |\  
         | * 0e61dcc Create from github
         * | 8b9e417 Create file from-local.md
         |/  
         * b986b73 (tag: v2.0) version 2.0
         * aaae799 (tag: v1.0) version 1.0
         * 3a1666d commit hello.txt and cambios.txt

         $ git merge fix-02
         Updating d14c280..cb4cb70
         Fast-forward
          new-commit.txt | 1 +
          1 file changed, 1 insertion(+)

         $ git log --graph --pretty --oneline
         * cb4cb70 (HEAD -> dev, fix-02) fix hecho en la rama fix-02
         * d14c280 tercera linea en cambios.txt
         *   3a4c264 Merge branch 'master' into dev
         |\  
         | *   059c912 (origin/master, master) Merge branch dev in master
         | |\  
         | * | 91f9d87 Hola desde rama master
         | * | b27cdea fix hecho en la rama fix-01
         | * | 1e12a67 nuevo commit rama master
         * | | c040330 (origin/dev) New brach experimental
         | |/  
         |/|   
         * | 187e4dd Hola desde rama dev
         * | 0e16342 Update news
         |/  
         * a07178f (tag: v3.0) version 3.0
         *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
         |\  
         | * 0e61dcc Create from github
         * | 8b9e417 Create file from-local.md
         |/  
         * b986b73 (tag: v2.0) version 2.0
         * aaae799 (tag: v1.0) version 1.0
         * 3a1666d commit hello.txt and cambios.txt

  Quedando así:

  ![alt rama-13][rama-13]

  [rama-13]: ../imagenes/rama-13.png

15. Por último con todos estos cambios crearemos una nueva versión de nuestro código que integraremos en la rama master:

        $ echo "version 4.0" >> version
        $ git add .

  Cofirmamos los cambios:

        $ git commit -m "version 4.0"
        [dev b74700d] version 4.0
        1 file changed, 1 insertion(+)

        $ git log --graph --pretty --oneline
        * b74700d (HEAD -> dev) version 4.0
        * cb4cb70 (fix-02) fix hecho en la rama fix-02
        * d14c280 tercera linea en cambios.txt
        *   3a4c264 Merge branch 'master' into dev
        |\  
        | *   059c912 (origin/master, master) Merge branch dev in master
        | |\  
        | * | 91f9d87 Hola desde rama master
        | * | b27cdea fix hecho en la rama fix-01
        | * | 1e12a67 nuevo commit rama master
        * | | c040330 (origin/dev) New brach experimental
        | |/  
        |/|   
        * | 187e4dd Hola desde rama dev
        * | 0e16342 Update news
        |/  
        * a07178f (tag: v3.0) version 3.0
        *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
        |\  
        | * 0e61dcc Create from github
        * | 8b9e417 Create file from-local.md
        |/  
        * b986b73 (tag: v2.0) version 2.0
        * aaae799 (tag: v1.0) version 1.0
        * 3a1666d commit hello.txt and cambios.txt

  Actualizamos la rama `dev` el repositorio remoto:

        $ git push origin dev
        Username for 'https://github.com': lissettegar
        Password for 'https://lissettegar@github.com':
        Counting objects: 11, done.
        Delta compression using up to 8 threads.
        Compressing objects: 100% (9/9), done.
        Writing objects: 100% (11/11), 1.17 KiB | 1.17 MiB/s, done.
        Total 11 (delta 4), reused 0 (delta 0)
        remote: Resolving deltas: 100% (4/4), completed with 1 local object.
        To https://github.com/lissettegar/formacion-git.git
           c040330..b74700d  dev -> dev

        $ git log --graph --pretty --oneline
        * b74700d (HEAD -> dev, origin/dev) version 4.0
        * cb4cb70 (fix-02) fix hecho en la rama fix-02
        * d14c280 tercera linea en cambios.txt
        *   3a4c264 Merge branch 'master' into dev
        |\  
        | *   059c912 (origin/master, master) Merge branch dev in master
        | |\  
        | * | 91f9d87 Hola desde rama master
        | * | b27cdea fix hecho en la rama fix-01
        | * | 1e12a67 nuevo commit rama master
        * | | c040330 New brach experimental
        | |/  
        |/|   
        * | 187e4dd Hola desde rama dev
        * | 0e16342 Update news
        |/  
        * a07178f (tag: v3.0) version 3.0
        *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
        |\  
        | * 0e61dcc Create from github
        * | 8b9e417 Create file from-local.md
        |/  
        * b986b73 (tag: v2.0) version 2.0
        * aaae799 (tag: v1.0) version 1.0
        * 3a1666d commit hello.txt and cambios.txt

  Nos movemos a la rama master para hacer el merge:

        $ git checkout master
        Switched to branch 'master'
        Your branch is up to date with 'origin/master'.

        $ git log --graph --pretty --oneline
        *   059c912 (HEAD -> master, origin/master) Merge branch dev in master
        |\  
        | * 187e4dd Hola desde rama dev
        | * 0e16342 Update news
        * | 91f9d87 Hola desde rama master
        * | b27cdea fix hecho en la rama fix-01
        * | 1e12a67 nuevo commit rama master
        |/  
        * a07178f (tag: v3.0) version 3.0
        *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
        |\  
        | * 0e61dcc Create from github
        * | 8b9e417 Create file from-local.md
        |/  
        * b986b73 (tag: v2.0) version 2.0
        * aaae799 (tag: v1.0) version 1.0
        * 3a1666d commit hello.txt and cambios.txt

        $ git merge dev
        Updating 059c912..b74700d
        Fast-forward
         cambios.txt    | 1 +
         exp.txt        | 1 +
         new-commit.txt | 1 +
         version        | 1 +
         4 files changed, 4 insertions(+)
         create mode 100644 exp.txt

        $ git log --graph --pretty --oneline
        * b74700d (HEAD -> master, origin/dev, dev) version 4.0
        * cb4cb70 (fix-02) fix hecho en la rama fix-02
        * d14c280 tercera linea en cambios.txt
        *   3a4c264 Merge branch 'master' into dev
        |\  
        | *   059c912 (origin/master) Merge branch dev in master
        | |\  
        | * | 91f9d87 Hola desde rama master
        | * | b27cdea fix hecho en la rama fix-01
        | * | 1e12a67 nuevo commit rama master
        * | | c040330 New brach experimental
        | |/  
        |/|   
        * | 187e4dd Hola desde rama dev
        * | 0e16342 Update news
        |/  
        * a07178f (tag: v3.0) version 3.0
        *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
        |\  
        | * 0e61dcc Create from github
        * | 8b9e417 Create file from-local.md
        |/  
        * b986b73 (tag: v2.0) version 2.0
        * aaae799 (tag: v1.0) version 1.0
        * 3a1666d commit hello.txt and cambios.txt

  Añadimos un tag para la nueva versión y subimos los cambios y tags al repositorioremoto:

         $ git tag -a v4.0 b74700d -m "Version 4.0"

         $ git log --graph --pretty --oneline
         * b74700d (HEAD -> master, tag: v4.0, origin/dev, dev) version 4.0
         * cb4cb70 (fix-02) fix hecho en la rama fix-02
         * d14c280 tercera linea en cambios.txt
         *   3a4c264 Merge branch 'master' into dev
         |\  
         | *   059c912 (origin/master) Merge branch dev in master
         | |\  
         | * | 91f9d87 Hola desde rama master
         | * | b27cdea fix hecho en la rama fix-01
         | * | 1e12a67 nuevo commit rama master
         * | | c040330 New brach experimental
         | |/  
         |/|   
         * | 187e4dd Hola desde rama dev
         * | 0e16342 Update news
         |/  
         * a07178f (tag: v3.0) version 3.0
         *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
         |\  
         | * 0e61dcc Create from github
         * | 8b9e417 Create file from-local.md
         |/  
         * b986b73 (tag: v2.0) version 2.0
         * aaae799 (tag: v1.0) version 1.0
         * 3a1666d commit hello.txt and cambios.txt

         $ git push origin master
         Username for 'https://github.com': lissettegar
         Password for 'https://lissettegar@github.com':
         Total 0 (delta 0), reused 0 (delta 0)
         To https://github.com/lissettegar/formacion-git.git
            059c912..b74700d  master -> master
