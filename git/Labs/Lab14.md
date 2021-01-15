### Instrucciones Laboratorio 14

* Prerequisito: Haber terminado los labs anteriores.

### git reset

Ya hemos visto cómo usar `git checkout` para mover el `HEAD` a un commit diferente. Este movimiento del `HEAD` no actualiza a donde apunta una rama, unicamente mueve el HEAD a un commit. Por el contrario `git reset` se usa para actualizar una rama especificamente para deshacer commits. En este lab vamos a practicar como deshacer un cambio con `git reset`.

1. Partiremos de estar en la rama `dev`:

       $ git branch
       * dev
         fix-02
         master
         new-feature

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

2. Vamos a añadir un nuevo commit:

       $ echo "cuarta linea" >>cambios.txt
       $ cat cambios.txt
       primera linea
       segunda linea
       tercera linea
       cuarta linea

       $ git add .

       $ git status
       On branch dev
       Changes to be committed:
       (use "git reset HEAD <file>..." to unstage)

       modified:   cambios.txt

       $ git commit -m "cuarta linea en cambios.txt"
       [dev 99580da] cuarta linea en cambios.txt
       1 file changed, 1 insertion(+)

       $ git log --graph --pretty --oneline
       * 99580da (HEAD -> dev) cuarta linea en cambios.txt
       * 23b1cbb (origin/dev) new feature
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

3. Veamos en este punto el contenido de cada una de los arboles y como los `blobs` tanto de `HEAD` como de `index` coinciden:

 Contenido del HEAD:

       $ git ls-tree -r HEAD
       100644 blob 7335aeee4ed3aeab1b6e912b9983da14adf6c2d9	cambios.txt
       100644 blob 68149a44070fc13d1955586288fbb0fa8f3025dd	exp.txt
       100644 blob 26ed59ad5ab6e28a3a800859a5b3c463226cd71e	from-github.md
       100644 blob bc3a9a6e3f469b1fc373dac02377df04c0479729	from-local.md
       100644 blob 9a5b60345d4182a2fb66350a722dd0987a4c6542	hello.txt
       100644 blob c0583e0f51837b2e44128c45c99b91249bef7fba	new-commit.txt
       100644 blob e4d82658abe47fe1d26693ce3cb2d5437baa9394	new-feature.txt
       100644 blob 3668580ecaed66c95f9e21efcc4aa84fdf4652b6	news.txt
       100644 blob 8d1e0a01cb1414e9564dc78285a288b759b17800	version

 Contenido del index:

       $ git ls-files --stage
       100644 7335aeee4ed3aeab1b6e912b9983da14adf6c2d9 0	cambios.txt
       100644 68149a44070fc13d1955586288fbb0fa8f3025dd 0	exp.txt
       100644 26ed59ad5ab6e28a3a800859a5b3c463226cd71e 0	from-github.md
       100644 bc3a9a6e3f469b1fc373dac02377df04c0479729 0	from-local.md
       100644 9a5b60345d4182a2fb66350a722dd0987a4c6542 0	hello.txt
       100644 c0583e0f51837b2e44128c45c99b91249bef7fba 0	new-commit.txt
       100644 e4d82658abe47fe1d26693ce3cb2d5437baa9394 0	new-feature.txt
       100644 3668580ecaed66c95f9e21efcc4aa84fdf4652b6 0	news.txt
       100644 8d1e0a01cb1414e9564dc78285a288b759b17800 0	version

 Contenido del working directory:

        $ tree .
       .
       ├── cambios.txt
       ├── exp.txt
       ├── from-github.md
       ├── from-local.md
       ├── hello.txt
       ├── new-commit.txt
       ├── new-feature.txt
       ├── news.txt
       └── version

4. A continuación vamos a hacer un `soft reset`. Este reset moverá la rama `dev` y el `HEAD` a un commit anterior, lo podemos comprobar con un `git log`:

       $ git reset --soft HEAD~

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

5. Vamos a comprobar ahora este `reset` que efectos ha tenido:

 Comprobamos que el fichero `cambios.txt` vuelve a aparecer como pendiente de `commit`:

       $ git status
       On branch dev
       Changes to be committed:
         (use "git reset HEAD <file>..." to unstage)

       	modified:   cambios.txt

 Comprobamos como el `blob` del fichero `cambios.txt` que contiene ahora el HEAD es diferente de el que contiene `index` (stage area). En el index sigue estando el contenido del ultimo commit que hemos hecho pero el HEAD ahora tiene el contenido anterior a dicho commit:

       $ git ls-files --stage
       100644 7335aeee4ed3aeab1b6e912b9983da14adf6c2d9 0	cambios.txt
       100644 68149a44070fc13d1955586288fbb0fa8f3025dd 0	exp.txt
       100644 26ed59ad5ab6e28a3a800859a5b3c463226cd71e 0	from-github.md
       100644 bc3a9a6e3f469b1fc373dac02377df04c0479729 0	from-local.md
       100644 9a5b60345d4182a2fb66350a722dd0987a4c6542 0	hello.txt
       100644 c0583e0f51837b2e44128c45c99b91249bef7fba 0	new-commit.txt
       100644 e4d82658abe47fe1d26693ce3cb2d5437baa9394 0	new-feature.txt
       100644 3668580ecaed66c95f9e21efcc4aa84fdf4652b6 0	news.txt
       100644 8d1e0a01cb1414e9564dc78285a288b759b17800 0	version

       $ git cat-file blob 7335aeee4ed3aeab1b6e912b9983da14adf6c2d9
       primera linea
       segunda linea
       tercera linea
       cuarta linea

       $ git ls-tree -r HEAD
       100644 blob 5a949ae7794ebc56a95841d2cd1700b604ba3fdc	cambios.txt
       100644 blob 68149a44070fc13d1955586288fbb0fa8f3025dd	exp.txt
       100644 blob 26ed59ad5ab6e28a3a800859a5b3c463226cd71e	from-github.md
       100644 blob bc3a9a6e3f469b1fc373dac02377df04c0479729	from-local.md
       100644 blob 9a5b60345d4182a2fb66350a722dd0987a4c6542	hello.txt
       100644 blob c0583e0f51837b2e44128c45c99b91249bef7fba	new-commit.txt
       100644 blob e4d82658abe47fe1d26693ce3cb2d5437baa9394	new-feature.txt
       100644 blob 3668580ecaed66c95f9e21efcc4aa84fdf4652b6	news.txt
       100644 blob 8d1e0a01cb1414e9564dc78285a288b759b17800	version

       $ git cat-file blob 5a949ae7794ebc56a95841d2cd1700b604ba3fdc
       primera linea
       segunda linea
       tercera linea

 Comprobamos que el contenido del fichero en el `working directory no ha cambiado`:

       $ cat cambios.txt
       primera linea
       segunda linea
       tercera linea
       cuarta linea

6. Ahora vamos a volver al commit que añadimos al principio de este lab para estar nuevamente en la situación de partida:

       $ git reset --soft HEAD@{1}

       $ git status
       On branch dev
       nothing to commit, working tree clean

       $ git log --graph --pretty --oneline
       * 99580da (HEAD -> dev) cuarta linea en cambios.txt
       * 23b1cbb (origin/dev) new feature
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

7. Estando nuevamente en la situación de partida vamos a hacer un `reset --mixed` y comprobaremos las consecuencias:

       $ git reset --mixed HEAD~
       Unstaged changes after reset:
       M	cambios.txt

 Comprobamos como en esta ocasión el fichero `cambios.txt` ya no aparece tampoco en el stage area o `index`:

       $ git status
       On branch dev
       Changes not staged for commit:
         (use "git add <file>..." to update what will be committed)
         (use "git checkout -- <file>..." to discard changes in working directory)

       	modified:   cambios.txt

       no changes added to commit (use "git add" and/or "git commit -a")

 Ahora el contenido del `index` y del `HEAD` es el mismo (lo que había en el commit anterior), pero el fichero `cambios.txt` del `working directory` sigue teniendo el útimo cambio:

       $ git ls-files --stage
       100644 5a949ae7794ebc56a95841d2cd1700b604ba3fdc 0	cambios.txt
       100644 68149a44070fc13d1955586288fbb0fa8f3025dd 0	exp.txt
       100644 26ed59ad5ab6e28a3a800859a5b3c463226cd71e 0	from-github.md
       100644 bc3a9a6e3f469b1fc373dac02377df04c0479729 0	from-local.md
       100644 9a5b60345d4182a2fb66350a722dd0987a4c6542 0	hello.txt
       100644 c0583e0f51837b2e44128c45c99b91249bef7fba 0	new-commit.txt
       100644 e4d82658abe47fe1d26693ce3cb2d5437baa9394 0	new-feature.txt
       100644 3668580ecaed66c95f9e21efcc4aa84fdf4652b6 0	news.txt
       100644 8d1e0a01cb1414e9564dc78285a288b759b17800 0	version

       $ git ls-tree -r HEAD
       100644 blob 5a949ae7794ebc56a95841d2cd1700b604ba3fdc	cambios.txt
       100644 blob 68149a44070fc13d1955586288fbb0fa8f3025dd	exp.txt
       100644 blob 26ed59ad5ab6e28a3a800859a5b3c463226cd71e	from-github.md
       100644 blob bc3a9a6e3f469b1fc373dac02377df04c0479729	from-local.md
       100644 blob 9a5b60345d4182a2fb66350a722dd0987a4c6542	hello.txt
       100644 blob c0583e0f51837b2e44128c45c99b91249bef7fba	new-commit.txt
       100644 blob e4d82658abe47fe1d26693ce3cb2d5437baa9394	new-feature.txt
       100644 blob 3668580ecaed66c95f9e21efcc4aa84fdf4652b6	news.txt
       100644 blob 8d1e0a01cb1414e9564dc78285a288b759b17800	version

       $ git cat-file blob 5a949ae7794ebc56a95841d2cd1700b604ba3fdc
       primera linea
       segunda linea
       tercera linea

       $ cat cambios.txt
       primera linea
       segunda linea
       tercera linea
       cuarta linea

8. Volvamos nuevamente a la situación de partida:

       $ git reflog
       23b1cbb (HEAD -> dev, origin/dev) HEAD@{0}: reset: moving to HEAD~
       99580da HEAD@{1}: reset: moving to HEAD@{1}
       23b1cbb (HEAD -> dev, origin/dev) HEAD@{2}: reset: moving to HEAD~
       99580da HEAD@{3}: commit: cuarta linea en cambios.txt

       $ git reset --mixed HEAD@{3}
       $ git status
       On branch dev
       nothing to commit, working tree clean
       lgarciap@lgarciap-ThinkPad-T480:~/Documents/MAYORAL/FORMACION/formacion-git$ git log --graph --pretty --oneline
       * 99580da (HEAD -> dev) cuarta linea en cambios.txt
       * 23b1cbb (origin/dev) new feature
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

9. Por último vamos a hacer un `reset --hard`. Este reset cambiará el `HEAD`, el `index` y el `working directory`, por eso se considera peligroso, porque tambien modifica el contenido directorio de trabajo:

       $ git reset --hard HEAD~
       HEAD is now at 23b1cbb new feature

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

 Comprobamos ahora el contenido del fichero `cambios.txt` en el `index`, el `HEAD` y el `working directory`:

       $ cat cambios.txt
       primera linea
       segunda linea
       tercera linea

       $ git ls-tree -r HEAD
       100644 blob 5a949ae7794ebc56a95841d2cd1700b604ba3fdc	cambios.txt
       100644 blob 68149a44070fc13d1955586288fbb0fa8f3025dd	exp.txt
       100644 blob 26ed59ad5ab6e28a3a800859a5b3c463226cd71e	from-github.md
       100644 blob bc3a9a6e3f469b1fc373dac02377df04c0479729	from-local.md
       100644 blob 9a5b60345d4182a2fb66350a722dd0987a4c6542	hello.txt
       100644 blob c0583e0f51837b2e44128c45c99b91249bef7fba	new-commit.txt
       100644 blob e4d82658abe47fe1d26693ce3cb2d5437baa9394	new-feature.txt
       100644 blob 3668580ecaed66c95f9e21efcc4aa84fdf4652b6	news.txt
       100644 blob 8d1e0a01cb1414e9564dc78285a288b759b17800	version

       $ git ls-files --stage
       100644 5a949ae7794ebc56a95841d2cd1700b604ba3fdc 0	cambios.txt
       100644 68149a44070fc13d1955586288fbb0fa8f3025dd 0	exp.txt
       100644 26ed59ad5ab6e28a3a800859a5b3c463226cd71e 0	from-github.md
       100644 bc3a9a6e3f469b1fc373dac02377df04c0479729 0	from-local.md
       100644 9a5b60345d4182a2fb66350a722dd0987a4c6542 0	hello.txt
       100644 c0583e0f51837b2e44128c45c99b91249bef7fba 0	new-commit.txt
       100644 e4d82658abe47fe1d26693ce3cb2d5437baa9394 0	new-feature.txt
       100644 3668580ecaed66c95f9e21efcc4aa84fdf4652b6 0	news.txt
       100644 8d1e0a01cb1414e9564dc78285a288b759b17800 0	version

       $ git cat-file blob 5a949ae7794ebc56a95841d2cd1700b604ba3fdc
       primera linea
       segunda linea
       tercera linea
