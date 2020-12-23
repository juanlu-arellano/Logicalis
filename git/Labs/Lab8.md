### Instrucciones Laboratorio 8

* Prerequisito: Haber terminado los labs anteriores. Usar repo **formacion-git**

1. Saltar a la rama master:

       $ git checkout master
       Switched to branch 'master'
       Your branch is ahead of 'origin/master' by 1 commit.
         (use "git push" to publish your local commits)

   ![alt rama-5][rama-5]

   [rama-5]: ../imagenes/rama-5.png

2. A continuación, es momento de resolver el problema urgente y no queremos modificar la rama master, así que vamos a crear una nueva rama `fix-01`, sobre la que trabajar hasta resolverlo. Creamos la rama y nos movemos hacia ella:   

       $ git checkout -b fix-01
       Switched to a new branch 'fix-01'

       $ git branch
         dev
       * fix-01
         master

 Para ver mas informacion de las ramas podemos ejecutar:

       $ git branch -v
       dev    0e16342 Update news
       * fix-01 1e12a67 nuevo commit rama master
       master 1e12a67 [ahead 1] nuevo commit rama master

 Para ver que ramas no se han mergeado a la rama actual (fix-01):

       $ git branch --no-merged
       dev

 Veamos con el historico donde estamos:

       $ git log --graph --pretty --oneline
       * 1e12a67 (HEAD -> fix-01, master) nuevo commit rama master
       * a07178f (tag: v3.0, origin/master) version 3.0
       *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
       |\  
       | * 0e61dcc Create from github
       * | 8b9e417 Create file from-local.md
       |/  
       * b986b73 (tag: v2.0) version 2.0
       * aaae799 (tag: v1.0) version 1.0
       * 3a1666d commit hello.txt and cambios.txt

3. Vamos a introducir un cambio que sería nuestro fix y lo confirmamos:

       $ echo "fix hecho en la rama fix-01" >> new-commit.txt

       $ git status
       On branch fix-01
       Changes not staged for commit:
         (use "git add <file>..." to update what will be committed)
         (use "git checkout -- <file>..." to discard changes in working directory)

       	modified:   new-commit.txt

       no changes added to commit (use "git add" and/or "git commit -a")

       $ git commit -a -m "fix hecho en la rama fix-01"
       [fix-01 b27cdea] fix hecho en la rama fix-01
        1 file changed, 1 insertion(+)

4. Comprobamos como se ha movido el HEAD al nuevo commit:

       $ git log --graph --pretty --oneline
       * b27cdea (HEAD -> fix-01) fix hecho en la rama fix-01
       * 1e12a67 (master) nuevo commit rama master
       * a07178f (tag: v3.0, origin/master) version 3.0
       *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
       |\  
       | * 0e61dcc Create from github
       * | 8b9e417 Create file from-local.md
       |/  
       * b986b73 (tag: v2.0) version 2.0
       * aaae799 (tag: v1.0) version 1.0
       * 3a1666d commit hello.txt and cambios.txt

  De forma gráfica esta es la situación:

  ![alt rama-6][rama-6]

  [rama-6]: ../imagenes/rama-6.png

5. Si ahora queremos enviar los cambios de la rama fix-01 a la rama master tendriamos que hacer un **merge**. Para ello nos posicionamos en la rama `master` y desde ahí hacemos el `merge`:

       $ git checkout master
       Switched to branch 'master'

 Si queremos ver que ramas tenemos sin mergear:

       $ git branch --no-merged
        dev
        fix-01

 Hacemos el merge:

       $ git merge fix-01
       Updating 1e12a67..b27cdea
       Fast-forward
        new-commit.txt | 1 +
        1 file changed, 1 insertion(+)

 Comprobamos que ramas estan mergeadas y cuales no:

       $ git branch -v
       dev    0e16342 Update news
       fix-01 b27cdea fix hecho en la rama fix-01
       * master b27cdea [ahead 2] fix hecho en la rama fix-01

       $ git branch --no-merged
        dev

       $ git branch --merged
         fix-01
       * master


6. Con este merge que es de tipo `Fast-forward` como indica la salida del comando, lo que hace git es mover (adelantar) el puntero de la rama `master` al commit al que apunta la rama `fix-01`. Este commit incluye los cambios que se hicieran en esa rama:

       $ git log --graph --pretty --oneline
       * b27cdea (HEAD -> master, fix-01) fix hecho en la rama fix-01
       * 1e12a67 nuevo commit rama master
       * a07178f (tag: v3.0, origin/master) version 3.0
       *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
       |\  
       | * 0e61dcc Create from github
       * | 8b9e417 Create file from-local.md
       |/  
       * b986b73 (tag: v2.0) version 2.0
       * aaae799 (tag: v1.0) version 1.0
       * 3a1666d commit hello.txt and cambios.txt

7. En este punto como ya no vamos a necesitar mas la rama fix-01, podemos borrarla:

        $ git branch -d fix-01
        Deleted branch fix-01 (was b27cdea).

        $ git log --graph --pretty --oneline
        * b27cdea (HEAD -> master) fix hecho en la rama fix-01
        * 1e12a67 nuevo commit rama master
        * a07178f (tag: v3.0, origin/master) version 3.0
        *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
        |\  
        | * 0e61dcc Create from github
        * | 8b9e417 Create file from-local.md
        |/  
        * b986b73 (tag: v2.0) version 2.0
        * aaae799 (tag: v1.0) version 1.0
        * 3a1666d commit hello.txt and cambios.txt

8. La situación actual es la siguiente:

  ![alt rama-7][rama-7]

  [rama-7]: ../imagenes/rama-7.png

9. Vamos a modificar un fichero que se encuentra en ambas ramas, por ejemplo el hello.txt. Este fichero ha sido modificado unicamente en la rama `dev`, cuando hagamos el merge no encontrará conflicto:

       $ git checkout  dev
       Switched to branch 'dev'

       $ echo 'Hola rama dev' > hello.txt

       $ git status
       On branch dev
       Changes not staged for commit:
         (use "git add <file>..." to update what will be committed)
         (use "git checkout -- <file>..." to discard changes in working directory)

       	modified:   hello.txt

       no changes added to commit (use "git add" and/or "git commit -a")

       $ git commit -a -m "Hola desde rama dev"
       [dev 187e4dd] Hola desde rama dev
        1 file changed, 1 insertion(+), 2 deletions(-)

        $ git log --graph --pretty --oneline
        * 187e4dd (HEAD -> dev) Hola desde rama dev
        * 0e16342 Update news
        * a07178f (tag: v3.0, origin/master) version 3.0
        *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
        |\  
        | * 0e61dcc Create from github
        * | 8b9e417 Create file from-local.md
        |/  
        * b986b73 (tag: v2.0) version 2.0
        * aaae799 (tag: v1.0) version 1.0
        * 3a1666d commit hello.txt and cambios.txt

  ![alt rama-8][rama-8]

  [rama-8]: ../imagenes/rama-8.png

10. Ahora ejecutaremos un merge para fusionar las ramas `master` y `dev`. En este caso no sería un merge `Fast-forward`, sino un merge `recursivo` porque hay divergencia entre las ramas que queremos fusionar:

        $ git checkout master
        Switched to branch 'master'

        $ git merge dev -m "Merge branch dev in master"
        Merge made by the 'recursive' strategy.
         hello.txt | 3 +--
         news.txt  | 1 +
         2 files changed, 2 insertions(+), 2 deletions(-)
         create mode 100644 news.txt

        $ cat hello.txt
        Hola rama dev

        $ git log --graph --pretty --oneline
        *   e06561a (HEAD -> master) Merge branch dev in master
        |\  
        | * 187e4dd (dev) Hola desde rama dev
        | * 0e16342 Update news
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


11. Si quisieramos deshacer este merge porque por ejemplo nos hemos equivocado y queremos volver a la situación de antes del merge, podemos hacer un reset al último commit de la rama master antes del merge:

        $ git reset --hard b27cdea
        HEAD is now at b27cdea fix hecho en la rama fix-01

        $ git status
        On branch master
        Your branch is ahead of 'origin/master' by 2 commits.
          (use "git push" to publish your local commits)

        nothing to commit, working tree clean

        $ git branch
          dev
        * master

        $ git log --graph --pretty --oneline
        * b27cdea (HEAD -> master) fix hecho en la rama fix-01
        * 1e12a67 nuevo commit rama master
        * a07178f (tag: v3.0, origin/master) version 3.0
        *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
        |\  
        | * 0e61dcc Create from github
        * | 8b9e417 Create file from-local.md
        |/  
        * b986b73 (tag: v2.0) version 2.0
        * aaae799 (tag: v1.0) version 1.0
        * 3a1666d commit hello.txt and cambios.txt

   Observar como en los logs ya no aparecen los commits correspondientes a la rama `dev`

12. A continuación vamos a modificar el fichero hello.txt también en la rama `master`:

        $ echo 'Hola rama master' > hello.txt
        $ git status
        On branch master
        Your branch is ahead of 'origin/master' by 2 commits.
          (use "git push" to publish your local commits)

        Changes not staged for commit:
          (use "git add <file>..." to update what will be committed)
          (use "git checkout -- <file>..." to discard changes in working directory)

        	modified:   hello.txt

        no changes added to commit (use "git add" and/or "git commit -a")

        $ git add .
        $ git commit -m "Hola desde rama master"
        [master 91f9d87] Hola desde rama master
         1 file changed, 1 insertion(+), 2 deletions(-)

        $ git status
        On branch master
        Your branch is ahead of 'origin/master' by 3 commits.
          (use "git push" to publish your local commits)

        nothing to commit, working tree clean

        $ git log --graph --pretty --oneline
        * 5a6ae93 (HEAD -> master) Hola desde rama master
        * 2815afd fix hecho en la rama fix-01
        * e1ccad3 nuevo commit rama master
        * 7ca0cfa (tag: v3.0, origin/master) version 3.0
        *   5670211 Merge branch 'master' of https://github.com/lissettegar/prueba
        |\  
        | * fd98b47 Create from-github.md
        * | 9ae72a9 Create file from-local.md
        |/  
        * 4f73fa5 (tag: v2.0) version 2.0
        * eec021c (tag: v1.0) verion 1.0
        * a452287 commit hello.txt and cambios.txt


13. Ahora si queremos hacer nuevamente el merge de la ramas, nos daría un error por un **conflicto**. El mismo fichero ha sido modificado en ambas ramas y el contenido no es el mismo. Git nos pide que solucionemos el conflicto:

        $ git merge dev -m "Merge branch dev in master"
        Auto-merging hello.txt
        CONFLICT (content): Merge conflict in hello.txt
        Automatic merge failed; fix conflicts and then commit the result.

14. Si hacemos ahora un git status podemos ver de que se trata el conflicto, en la seccion `Unmerged paths` aparecen los ficheros donde ha encontrado un conflicto, en este caso en el fichero `hello.txt`:

        $ git status
        On branch master
        Your branch is ahead of 'origin/master' by 3 commits.
          (use "git push" to publish your local commits)

        You have unmerged paths.
          (fix conflicts and run "git commit")
          (use "git merge --abort" to abort the merge)

        Changes to be committed:

        	new file:   news.txt

        Unmerged paths:
          (use "git add <file>..." to mark resolution)

        	both modified:   hello.txt

15. Si hacemos un cat del fichero que da el conflicto vemos lo siguiente:

         $ cat hello.txt
         <<<<<<< HEAD
         Hola rama master
         =======
         Hola rama dev
         >>>>>>> dev

   Estos son los marcadores que añade git a los ficheros para señalar los conflictos. Tenemos la opción de quedarnos con los cambios de la rama master, los de la rama dev o hacer un mix entre ambos. Vamos a hacer un mix entre ambos, para ello editamos el fichero con un `vi` y borramos unicamente los marcadores que ha añadido git. El fichero debe quedar así:

        $ cat hello.txt
        Hola rama master
        Hola rama dev

16. El merge ya estaba iniciado (como se puede ver en el git status, indica "All conflicts fixed but you are still merging"), no hay que volver a ejecutarlo, solo hay que añadir el fichero donde hemos solucionado el conflicto y hacer un commit:

         $ git add hello.txt

         $ git status
         On branch master
         Your branch is ahead of 'origin/master' by 3 commits.
           (use "git push" to publish your local commits)

         All conflicts fixed but you are still merging.
           (use "git commit" to conclude merge)

         Changes to be committed:

         	modified:   hello.txt
         	new file:   news.txt

          $ git commit
          [master 059c912] Merge branch dev in master

17. Finalmente podemos comprobar que en los logs de la rama `master` aparecen los commits de la rama `dev`

        $ git log --graph --pretty --oneline
        *   059c912 (HEAD -> master) Merge branch dev in master
        |\  
        | * 187e4dd (dev) Hola desde rama dev
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
