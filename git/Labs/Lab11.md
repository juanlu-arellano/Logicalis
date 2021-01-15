### Instrucciones Laboratorio 11

* Prerequisito: Haber terminado los labs anteriores.

### Cherry-Picks

* Prerequisito: Haber terminado los labs anteriores.

1. Nos Posicionamos en el repositorio **formacion-git** y creamos una nueva rama `new-feature` a partir de la rama `dev`, para ello haremos lo siguiente:

       $ git checkout dev
       Switched to branch 'dev'

       $ git log --graph --pretty --oneline
       * b74700d (HEAD -> dev, tag: v4.0, origin/master, origin/dev, master) version 4.0
       * cb4cb70 (fix-02) fix hecho en la rama fix-02
       * d14c280 tercera linea en cambios.txt
       *   3a4c264 Merge branch 'master' into dev
       |\  
       | *   059c912 Merge branch dev in master
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

       $ git checkout -b new-feature
       Switched to a new branch 'new-feature'

       $ git log --graph --pretty --oneline
       * b74700d (HEAD -> new-feature, tag: v4.0, origin/master, origin/dev, master, dev) version 4.0
       * cb4cb70 (fix-02) fix hecho en la rama fix-02
       * d14c280 tercera linea en cambios.txt
       *   3a4c264 Merge branch 'master' into dev
       |\  
       | *   059c912 Merge branch dev in master
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

   ![alt cherry-pick1][cherry-pick1]

   [cherry-pick1]: ../imagenes/cherry-pick1.png

2. Una vez creada la rama `new-feature`, crearemos 2 nuevos commits en dicha rama:

       $ echo "nueva feature" >>new-feature.txt
       $ git add .
       $ git commit -m "new feature"
       [new-feature 7dd4ce7] new feature
        1 file changed, 1 insertion(+)
        create mode 100644 new-feature.txt

       $ echo "nueva feature adicional" >>new-feature-add.txt
       $ git add .
       $ git commit -m "new feature add"
       [new-feature bade9da] new feature add
        1 file changed, 1 insertion(+)
        create mode 100644 new-feature-add.txt

       $ git log --graph --pretty --oneline
       * bade9da (HEAD -> new-feature) new feature add
       * 7dd4ce7 new feature
       * b74700d (tag: v4.0, origin/master, origin/dev, master, dev) version 4.0
       * cb4cb70 (fix-02) fix hecho en la rama fix-02
       * d14c280 tercera linea en cambios.txt
       *   3a4c264 Merge branch 'master' into dev
       |\  
       | *   059c912 Merge branch dev in master
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

   ![alt cherry-pick2][cherry-pick2]

   [cherry-pick2]: ../imagenes/cherry-pick2.png

3. Mientras tanto, añadimos un nuevo cambio en la rama `dev`:

       $ git checkout dev
       Switched to branch 'dev'

       $ echo "nuevos cambios en la rama dev" >>news.txt
       $ git add .

       $ git commit -m "nuevos cambios en la rama dev"
       [dev 3080ead] nuevos cambios en la rama dev
        1 file changed, 1 insertion(+)

       $ git log --graph --pretty --oneline
       * 3080ead (HEAD -> dev) nuevos cambios en la rama dev
       * b74700d (tag: v4.0, origin/master, origin/dev, master) version 4.0
       * cb4cb70 (fix-02) fix hecho en la rama fix-02
       * d14c280 tercera linea en cambios.txt
       *   3a4c264 Merge branch 'master' into dev
       |\  
       | *   059c912 Merge branch dev in master
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

4. A continuación nos queremos traer únicamente el cambio del **primer commit** (en el ejemplo es el 7dd4ce7 new feature) a nuestra rama de `dev` usando `git cherry-pick`. Para ello nos posicionamos en la rama `dev` y desde ahí hacemos el cherry-pick:

       $ git cherry-pick 7dd4ce7
       [dev 23b1cbb] new feature
        Date: Fri Jan 8 10:48:54 2021 +0100
        1 file changed, 1 insertion(+)
        create mode 100644 new-feature.txt

       $ git status
       On branch dev
       nothing to commit, working tree clean

       $ git log --graph --pretty --oneline
       * 23b1cbb (HEAD -> dev) new feature
       * 3080ead nuevos cambios en la rama dev
       * b74700d (tag: v4.0, origin/master, origin/dev, master) version 4.0
       * cb4cb70 (fix-02) fix hecho en la rama fix-02
       * d14c280 tercera linea en cambios.txt
       *   3a4c264 Merge branch 'master' into dev
       |\  
       | *   059c912 Merge branch dev in master
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


   ![alt cherry-pick3][cherry-pick3]

   [cherry-pick3]: ../imagenes/cherry-pick3.png

5. Por último vamos a subir los cambios de la rama `dev` a nuestro repositorio remoto:

       $ git push origin dev
       Username for 'https://github.com': lissettegar
       Password for 'https://lissettegar@github.com':
       Counting objects: 6, done.
       Delta compression using up to 8 threads.
       Compressing objects: 100% (5/5), done.
       Writing objects: 100% (6/6), 592 bytes | 592.00 KiB/s, done.
       Total 6 (delta 2), reused 0 (delta 0)
       remote: Resolving deltas: 100% (2/2), completed with 1 local object.
       To https://github.com/lissettegar/formacion-git.git
          b74700d..23b1cbb  dev -> dev

       $ git log --graph --pretty --oneline
       * 23b1cbb (HEAD -> dev, origin/dev) new feature
       * 3080ead nuevos cambios en la rama dev
       * b74700d (tag: v4.0, origin/master, master) version 4.0
       * cb4cb70 (fix-02) fix hecho en la rama fix-02
       * d14c280 tercera linea en cambios.txt
       *   3a4c264 Merge branch 'master' into dev
       |\  
       | *   059c912 Merge branch dev in master
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
