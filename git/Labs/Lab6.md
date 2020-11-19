### Instrucciones Laboratorio 6

### Etiquetado

1. Mostrar el historial de confirmaciones:

       $ git log --pretty=oneline
       5670211fc758e8d16faab39760479d0417459962 (HEAD -> master, origin/master) Merge branch 'master' of https://github.com/lissettegar/prueba
       9ae72a99243d92f38ee0c7bf34c2381206d990e2 Create file from-local.md
       fd98b4779e39dd7277feecdcd07ec38b6b014797 Create from-github.md
       4f73fa5f9d9350f5bf8b3747967f28b173f18f0b version 2.0
       eec021cc70adec40e44c2cdead96d3efa2100606 version 1.0
       a4522872d7c80eef8e17b3d12baa81ba53b81008 commit hello.txt and cambios.txt

2. Etiquetar el commit de la version 1.0 como etiqueta ligera:

       $ git tag v1.0 eec021c

       $ git log --pretty=oneline
       5670211fc758e8d16faab39760479d0417459962 (HEAD -> master, origin/master) Merge branch 'master' of https://github.com/lissettegar/prueba
       9ae72a99243d92f38ee0c7bf34c2381206d990e2 Create file from-local.md
       fd98b4779e39dd7277feecdcd07ec38b6b014797 Create from-github.md
       4f73fa5f9d9350f5bf8b3747967f28b173f18f0b version 2.0
       eec021cc70adec40e44c2cdead96d3efa2100606 (tag: v1.0) version 1.0
       a4522872d7c80eef8e17b3d12baa81ba53b81008 commit hello.txt and cambios.txt

3. Etiquetar el commit de la version 2.0 como etiqueta con anotaciones:

       $ git tag -a v2.0 4f73fa5f -m "Version 2.0"

       $ git log --pretty=oneline
       5670211fc758e8d16faab39760479d0417459962 (HEAD -> master, origin/master) Merge branch 'master' of https://github.com/lissettegar/prueba
       9ae72a99243d92f38ee0c7bf34c2381206d990e2 Create file from-local.md
       fd98b4779e39dd7277feecdcd07ec38b6b014797 Create from-github.md
       4f73fa5f9d9350f5bf8b3747967f28b173f18f0b (tag: v2.0) version 2.0
       eec021cc70adec40e44c2cdead96d3efa2100606 (tag: v1.0) version 1.0
       a4522872d7c80eef8e17b3d12baa81ba53b81008 commit hello.txt and cambios.txt

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
       [master 7ca0cfa] version 3.0
        1 file changed, 1 insertion(+), 1 deletion(-)

       $ git status
       On branch master
       nothing to commit, working tree clean

       $ git log --pretty=oneline
       7ca0cfa0e74f83eaa67b6ea56d38a63ad13d8d99 (HEAD -> master) version 3.0
       5670211fc758e8d16faab39760479d0417459962 (origin/master) Merge branch 'master' of https://github.com/lissettegar/prueba
       9ae72a99243d92f38ee0c7bf34c2381206d990e2 Create file from-local.md
       fd98b4779e39dd7277feecdcd07ec38b6b014797 Create from-github.md
       4f73fa5f9d9350f5bf8b3747967f28b173f18f0b (tag: v2.0) version 2.0
       eec021cc70adec40e44c2cdead96d3efa2100606 (tag: v1.0) version 1.0
       a4522872d7c80eef8e17b3d12baa81ba53b81008 commit hello.txt and cambios.txt

6. Etiquetar el commit en el que estamos como la version 3.0:

       $ git tag -a v3.0 -m "version 3.0"

       $ git log --pretty=oneline
       7ca0cfa0e74f83eaa67b6ea56d38a63ad13d8d99 (HEAD -> master, tag: v3.0) version 3.0
       5670211fc758e8d16faab39760479d0417459962 (origin/master) Merge branch 'master' of https://github.com/lissettegar/prueba
       9ae72a99243d92f38ee0c7bf34c2381206d990e2 Create file from-local.md
       fd98b4779e39dd7277feecdcd07ec38b6b014797 Create from-github.md
       4f73fa5f9d9350f5bf8b3747967f28b173f18f0b (tag: v2.0) version 2.0
       eec021cc70adec40e44c2cdead96d3efa2100606 (tag: v1.0) version 1.0
       a4522872d7c80eef8e17b3d12baa81ba53b81008 commit hello.txt and cambios.txt

7. Subir el commit y las etiquetas al repositorio remoto:

       $ git push origin master --tags
       Username for 'https://github.com': lissettegar
       Password for 'https://lissettegar@github.com':
       Counting objects: 5, done.
       Delta compression using up to 8 threads.
       Compressing objects: 100% (4/4), done.
       Writing objects: 100% (5/5), 569 bytes | 569.00 KiB/s, done.
       Total 5 (delta 1), reused 0 (delta 0)
       remote: Resolving deltas: 100% (1/1), completed with 1 local object.
       To https://github.com/lissettegar/prueba.git
          5670211..7ca0cfa  master -> master
        * [new tag]         v1.0 -> v1.0
        * [new tag]         v2.0 -> v2.0
        * [new tag]         v3.0 -> v3.0

        $ git log --oneline --graph
        * 7ca0cfa (HEAD -> master, tag: v3.0, origin/master) version 3.0
        *   5670211 Merge branch 'master' of https://github.com/lissettegar/prueba
        |\  
        | * fd98b47 Create from-github.md
        * | 9ae72a9 Create file from-local.md
        |/  
        * 4f73fa5 (tag: v2.0) version 2.0
        * eec021c (tag: v1.0) verion 1.0
        * a452287 commit hello.txt and cambios.txt

8. Listar las etiquetas:

       $ git tag
       v1.0
       v2.0
       v3.0


9. Comprobar que los cambios y las etiquetas ya están subidas en github:

![alt tag-1][tag-1]

[tag-1]: ../imagenes/tag-1.png

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

        HEAD is now at eec021c verion 1.0

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

         $ git log --pretty=oneline
         eec021cc70adec40e44c2cdead96d3efa2100606 (HEAD, tag: v1.0) verion 1.0
         a4522872d7c80eef8e17b3d12baa81ba53b81008 commit hello.txt and cambios.txt

12. Para volver a mover el HEAD al último commit de nuestra rama master podemos por ejemplo ejecutar:

        $ git checkout master
        Previous HEAD position was eec021c verion 1.0
        Switched to branch 'master'

        $ git log --pretty=oneline
        7ca0cfa0e74f83eaa67b6ea56d38a63ad13d8d99 (HEAD -> master, tag: v3.0, origin/master) version 3.0
        5670211fc758e8d16faab39760479d0417459962 Merge branch 'master' of https://github.com/lissettegar/prueba
        9ae72a99243d92f38ee0c7bf34c2381206d990e2 Create file from-local.md
        fd98b4779e39dd7277feecdcd07ec38b6b014797 Create from-github.md
        4f73fa5f9d9350f5bf8b3747967f28b173f18f0b (tag: v2.0) version 2.0
        eec021cc70adec40e44c2cdead96d3efa2100606 (tag: v1.0) verion 1.0
        a4522872d7c80eef8e17b3d12baa81ba53b81008 commit hello.txt and cambios.txt

        $ cat version 
        version: 3.0
