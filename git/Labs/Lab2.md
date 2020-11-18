### Instrucciones Laboratorio 2

### Deshacer cambios

1. Comprobar que no hay ningún cambio pendiente del lab anterior:

       $ git status
       On branch master
       nothing to commit, working tree clean

2. Crear nuevo fichero y añadirlo al repositorio:

       $ echo 'primera linea' >> cambios.txt
       $ git status
       On branch master
       Untracked files:
         (use "git add <file>..." to include in what will be committed)

       	cambios.txt

       nothing added to commit but untracked files present (use "git add" to track)

3. Este fichero que acabamos de crear no fue incluido en el commit anterior por error, así que vamos a incluirlo ahora usando el comando git commit --amend:

       $ git add cambios.txt
       lgarciap@lgarciap-ThinkPad-T480:~/Documents/REPOS/prueba$ git status
       On branch master
       Changes to be committed:
         (use "git reset HEAD <file>..." to unstage)

       	new file:   cambios.txt

4. Podemos comprobar que se ha creado una entrada para el nuevo fichero:

        ├── index
        ├── info
        │   └── exclude
        ├── logs
        │   ├── HEAD
        │   └── refs
        │       └── heads
        │           └── master
        ├── objects
        │   ├── 35
        │   │   └── d4b75f539702e8c15fc1c985e61cca603a2a3b
        │   ├── 6e
        │   │   └── 9058368966b7b6797b108919f5517d58e98437
        │   ├── 78
        │   │   └── 4aab98519e195b37ab749d3f76d7cedca6858d
        │   ├── 8d
        │   │   └── 0e41234f24b6da002d962a26c2495ea16a425f
        │   ├── f5
        │   │   └── 4869598dea474ea9fc8a41bd7cdd016d3e7de9
        │   ├── info
        │   └── pack
        └── refs
            ├── heads
            │   └── master
            └── tags

5. Hacemos el commit:

       $ git commit --amend
       commit hello.txt and cambios.txt

       # Please enter the commit message for your changes. Lines starting
       # with '#' will be ignored, and an empty message aborts the commit.
       #
       # Date:      Tue Nov 17 16:06:50 2020 +0100
       #
       # On branch master
       #
       # Initial commit
       #
       # Changes to be committed:
       #       new file:   cambios.txt
       #       new file:   hello.txt
       #

       $ git commit --amend
       [master a452287] commit hello.txt and cambios.txt
        Date: Wed Nov 18 09:23:31 2020 +0100
        2 files changed, 3 insertions(+)
        create mode 100644 cambios.txt
        create mode 100644 hello.txt

        $ git status
        On branch master
        nothing to commit, working tree clean

6. Ahora podemos ver que se ha registrado el nuevo commit:

       ├── index
       ├── info
       │   └── exclude
       ├── logs
       │   ├── HEAD
       │   └── refs
       │       └── heads
       │           └── master
       ├── objects
       │   ├── 07
       │   │   └── 5f71514a6b3bce04c7ab07930b3d017bbe67e6
       │   ├── 35
       │   │   └── d4b75f539702e8c15fc1c985e61cca603a2a3b
       │   ├── 6e
       │   │   └── 9058368966b7b6797b108919f5517d58e98437
       │   ├── 78
       │   │   └── 4aab98519e195b37ab749d3f76d7cedca6858d
       │   ├── 8d
       │   │   └── 0e41234f24b6da002d962a26c2495ea16a425f
       │   ├── a4
       │   │   └── 522872d7c80eef8e17b3d12baa81ba53b81008
       │   ├── f5
       │   │   └── 4869598dea474ea9fc8a41bd7cdd016d3e7de9
       │   ├── info
       │   └── pack
       └── refs
           ├── heads
           │   └── master
           └── tags

7. Si lo examinamos:

       $ git cat-file -t a4522872d7c80eef8e17b3d12baa81ba53b81008
       commit
       lgarciap@lgarciap-ThinkPad-T480:~/Documents/REPOS/prueba$ git cat-file commit a4522872d7c80eef8e17b3d12baa81ba53b81008
       tree 075f71514a6b3bce04c7ab07930b3d017bbe67e6
       author Lissette García <lissette.garcia@es.logicalis.com> 1605687811 +0100
       committer Lissette García <lissette.garcia@es.logicalis.com> 1605688259 +0100

       commit hello.txt and cambios.txt

8. Tambien podemos confirmar que el actual HEAD es el nuevo commit:

       $ cat .git/refs/heads/master
       a4522872d7c80eef8e17b3d12baa81ba53b81008

9. Ahora modificamos el fichero cambios.txt:

       $ echo 'segunda linea' >> cambios.txt

10. En este punto git detecta el fichero como modificado:

        $ git status
        On branch master
        Changes not staged for commit:
          (use "git add <file>..." to update what will be committed)
          (use "git checkout -- <file>..." to discard changes in working directory)

         modified:   cambios.txt

        no changes added to commit (use "git add" and/or "git commit -a")

11. Hacemos el git add para moverlo al area de stage o preparación:

        $ git add cambios.txt
        $ git status
        On branch master
        Changes to be committed:
          (use "git reset HEAD <file>..." to unstage)

        	modified:   cambios.txt

12. En este punto nos damos cuenta de que no queremos que este cambio se incluya aún en un commit, así que vamos a sacar al fichero del area de preparación:

        $ git reset HEAD
        Unstaged changes after reset:
        M	cambios.txt
        $ git status
        On branch master
        Changes not staged for commit:
          (use "git add <file>..." to update what will be committed)
          (use "git checkout -- <file>..." to discard changes in working directory)

        	modified:   cambios.txt

        no changes added to commit (use "git add" and/or "git commit -a")
        $ cat cambios.txt
        primera linea
        segunda linea


13. Si en este caso quisieramos recuperar la versión del fichero cambios.txt que tenemos en el commit podriamos hacerlo con un git checkout:

         $ git checkout -- cambios.txt
         $ git status
         On branch master
         nothing to commit, working tree clean
         $ cat cambios.txt 
         primera linea
