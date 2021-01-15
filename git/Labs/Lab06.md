### Instrucciones Laboratorio 6

* Prerequisito: Haber terminado los labs anteriores. Usaremos el repositorio de los labs anteriores **formacion-git**

### Etiquetado

1. Mostrar el historial de confirmaciones:

       $ git log --pretty=oneline
       3c6a550790a4d09403c0603ccedf6c3e34c6749a (HEAD -> master, origin/master) Merge branch 'master' of https://github.com/lissettegar/formacion-git
       0e61dccb03040da58a8dc6680b11fe4d9f9d057d Create from github
       8b9e417db52fdc711a3463a472abb177c70c9581 Create file from-local.md
       b986b73fc0e81eb765d96057b46309efa8abe0c4 version 2.0
       aaae799b5936836415e13d7e41117afe02cb83e9 version 1.0
       3a1666d9149d5b135980c2ab4cdb833a973a4750 commit hello.txt and cambios.txt

2. Etiquetar el commit de la version 1.0 como etiqueta ligera y comprobar como aparece en los logs:

       $ git tag v1.0 aaae799

       $ git log --pretty=oneline
       3c6a550790a4d09403c0603ccedf6c3e34c6749a (HEAD -> master, origin/master) Merge branch 'master' of https://github.com/lissettegar/formacion-git
       0e61dccb03040da58a8dc6680b11fe4d9f9d057d Create from github
       8b9e417db52fdc711a3463a472abb177c70c9581 Create file from-local.md
       b986b73fc0e81eb765d96057b46309efa8abe0c4 version 2.0
       aaae799b5936836415e13d7e41117afe02cb83e9 (tag: v1.0) version 1.0
       3a1666d9149d5b135980c2ab4cdb833a973a4750 commit hello.txt and cambios.txt

3. Etiquetar el commit de la version 2.0 como etiqueta con anotaciones:

       $ git tag -a v2.0 4f73fa5f -m "Version 2.0"

       $ git log --pretty=oneline
       3c6a550790a4d09403c0603ccedf6c3e34c6749a (HEAD -> master, origin/master) Merge branch 'master' of https://github.com/lissettegar/formacion-git
       0e61dccb03040da58a8dc6680b11fe4d9f9d057d Create from github
       8b9e417db52fdc711a3463a472abb177c70c9581 Create file from-local.md
       b986b73fc0e81eb765d96057b46309efa8abe0c4 (tag: v2.0) version 2.0
       aaae799b5936836415e13d7e41117afe02cb83e9 (tag: v1.0) version 1.0
       3a1666d9149d5b135980c2ab4cdb833a973a4750 commit hello.txt and cambios.txt

4. Confirmar que no tenemos ninguna modificación pendiente:

       $ git status
       On branch master
       nothing to commit, working tree clean

5. Modificar el fichero de versiones para simular una nueva version y registrar los cambios en el repositorio local:

       $ echo 'version: 3.0' > version

       $ git status
       On branch master
       Changes not staged for commit:
         (use "git add <file>..." to update what will be committed)
         (use "git checkout -- <file>..." to discard changes in working directory)

       	modified:   version

       no changes added to commit (use "git add" and/or "git commit -a")

       $ git add .

       $ git commit -m "version 3.0"
       [master a07178f] version 3.0
        1 file changed, 1 insertion(+), 1 deletion(-)

       $ git status
       On branch master
       nothing to commit, working tree clean

       $ git log --pretty=oneline
       a07178fa70319546a6e4c72b4893f9fa2d8b53a9 (HEAD -> master) version 3.0
       3c6a550790a4d09403c0603ccedf6c3e34c6749a (origin/master) Merge branch 'master' of https://github.com/lissettegar/formacion-git
       0e61dccb03040da58a8dc6680b11fe4d9f9d057d Create from github
       8b9e417db52fdc711a3463a472abb177c70c9581 Create file from-local.md
       b986b73fc0e81eb765d96057b46309efa8abe0c4 (tag: v2.0) version 2.0
       aaae799b5936836415e13d7e41117afe02cb83e9 (tag: v1.0) version 1.0
       3a1666d9149d5b135980c2ab4cdb833a973a4750 commit hello.txt and cambios.txt

6. Etiquetar el commit en el que estamos como la version 3.0:

       $ git tag -a v3.0 -m "version 3.0"

       $ git log --pretty=oneline
       a07178fa70319546a6e4c72b4893f9fa2d8b53a9 (HEAD -> master, tag: v3.0) version 3.0
       3c6a550790a4d09403c0603ccedf6c3e34c6749a (origin/master) Merge branch 'master' of https://github.com/lissettegar/formacion-git
       0e61dccb03040da58a8dc6680b11fe4d9f9d057d Create from github
       8b9e417db52fdc711a3463a472abb177c70c9581 Create file from-local.md
       b986b73fc0e81eb765d96057b46309efa8abe0c4 (tag: v2.0) version 2.0
       aaae799b5936836415e13d7e41117afe02cb83e9 (tag: v1.0) version 1.0
       3a1666d9149d5b135980c2ab4cdb833a973a4750 commit hello.txt and cambios.txt

7. Subir el commit y las etiquetas al repositorio remoto:

       $ git push origin master --tags
       Username for 'https://github.com': lissettegar
       Password for 'https://lissettegar@github.com':
       Counting objects: 5, done.
       Delta compression using up to 8 threads.
       Compressing objects: 100% (4/4), done.
       Writing objects: 100% (5/5), 567 bytes | 567.00 KiB/s, done.
       Total 5 (delta 1), reused 0 (delta 0)
       remote: Resolving deltas: 100% (1/1), completed with 1 local object.
       To https://github.com/lissettegar/formacion-git.git
          3c6a550..a07178f  master -> master
        * [new tag]         v1.0 -> v1.0
        * [new tag]         v2.0 -> v2.0
        * [new tag]         v3.0 -> v3.0

        $ git log --oneline --graph
        * a07178f (HEAD -> master, tag: v3.0, origin/master) version 3.0
        *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
        |\  
        | * 0e61dcc Create from github
        * | 8b9e417 Create file from-local.md
        |/  
        * b986b73 (tag: v2.0) version 2.0
        * aaae799 (tag: v1.0) version 1.0
        * 3a1666d commit hello.txt and cambios.txt

8. Listar las etiquetas:

       $ git tag
       v1.0
       v2.0
       v3.0


9. Comprobar que los cambios y las etiquetas ya están subidas en github:

En la opción **<>Code**

![alt tag-1][tag-1]

[tag-1]: ../imagenes/tag-1.png

En **Tags**

![alt tag-2][tag-2]

[tag-2]: ../imagenes/tag-2.png

10. Movernos a otro commit usando la etiqueta:

        $ git checkout v1.0
        Note: checking out 'v1.0'.

        You are in 'detached HEAD' state. You can look around, make experimental
        changes and commit them, and you can discard any commits you make in this
        state without impacting any branches by performing another checkout.

        If you want to create a new branch to retain commits you create, you may
        do so (now or later) by using -b with the checkout command again. Example:

          git checkout -b <new-branch-name>

        HEAD is now at aaae799 version 1.0

11. Comprobar que los ficheros que tenemos en nuestro directorio de trabajo se corresponden con los que teniamos en el commit de la version 1.0 y el historico de commits tambien se corresponde:

         $ ll
         total 24
         drwxr-xr-x  3 lgarciap lgarciap 4096 Nov 19 12:15 ./
         drwxr-xr-x 28 lgarciap lgarciap 4096 Nov 18 13:13 ../
         -rw-r--r--  1 lgarciap lgarciap   14 Nov 19 12:15 cambios.txt
         drwxr-xr-x  8 lgarciap lgarciap 4096 Nov 19 12:15 .git/
         -rw-r--r--  1 lgarciap lgarciap   18 Nov 18 09:22 hello.txt
         -rw-r--r--  1 lgarciap lgarciap   13 Nov 19 12:15 version

         $ cat version
         version: 1.0

         $ git log --oneline --graph
         * aaae799 (HEAD, tag: v1.0) version 1.0
         * 3a1666d commit hello.txt and cambios.txt

12. Para volver a mover el HEAD al último commit de nuestra rama master podemos por ejemplo ejecutar:

        $ git checkout master
        Previous HEAD position was aaae799 version 1.0
        Switched to branch 'master'
        Your branch is up to date with 'origin/master'.

        $ git log --pretty=oneline
        a07178fa70319546a6e4c72b4893f9fa2d8b53a9 (HEAD -> master, tag: v3.0, origin/master) version 3.0
        3c6a550790a4d09403c0603ccedf6c3e34c6749a Merge branch 'master' of https://github.com/lissettegar/formacion-git
        0e61dccb03040da58a8dc6680b11fe4d9f9d057d Create from github
        8b9e417db52fdc711a3463a472abb177c70c9581 Create file from-local.md
        b986b73fc0e81eb765d96057b46309efa8abe0c4 (tag: v2.0) version 2.0
        aaae799b5936836415e13d7e41117afe02cb83e9 (tag: v1.0) version 1.0
        3a1666d9149d5b135980c2ab4cdb833a973a4750 commit hello.txt and cambios.txt

        $ cat version
        version: 3.0
