### Instrucciones Laboratorio 1

### Añadir cambios

En este laboratorio pondremos en práctica comandos básicos de git

1. Instalar git y tree:

 En linux:

       $ sudo apt-get install git git-gui gitk tree

 En windows:

  * Descargar el software de git desde https://gitforwindows.org/. Esto descargará un programa `git bash` para usar como consola desde la que ejecutar los comandos de git.

  * Descargar el binario del comando `tree` desde http://gnuwin32.sourceforge.net/packages/tree.htm. Descomprimir el fichero y copiarlo en la carpeta que elijais. Por último añadir la ruta donde este el `tree` en la variable de entorno `PATH`.

2. Configurar git:

       $ git config --global user.name "Your Name"
       $ git config --global user.email "Your email address"
       $ git config --global core.editor vi
       $ git config --list      

3. Crear un repositorio nuevo vacio,
- El subcomando init creará un directorio .git en el repositorio utilizado para los metadatos de Git
(consulte el curso de Git Deep Dive para obtener más información).:

       $ mkdir formacion-git
       $ cd formacion-git
       $ git init

4. Comprobar que se crea el directorio oculto `.git`:

       $ ls -la
       $ tree .git
       .git
       ├── branches
       ├── config
       ├── description
       ├── HEAD
       ├── hooks
       │   ├── applypatch-msg.sample
       │   ├── commit-msg.sample
       │   ├── fsmonitor-watchman.sample
       │   ├── post-update.sample
       │   ├── pre-applypatch.sample
       │   ├── pre-commit.sample
       │   ├── prepare-commit-msg.sample
       │   ├── pre-push.sample
       │   ├── pre-rebase.sample
       │   ├── pre-receive.sample
       │   └── update.sample
       ├── info
       │   └── exclude
       ├── objects
       │   ├── info
       │   └── pack
       └── refs
           ├── heads
           └── tags

       9 directories, 15 files

5. Comprobar a donde apunta el `HEAD`:

       $ cat .git/HEAD
       ref: refs/heads/master

6. Comprobar el estado del repositorio:

       $ git status
       On branch master

       No commits yet

       nothing to commit (create/copy files and use "git add" to track)

7. Crear un fichero dentro del directorio del repo y comprobar con git status que el fichero aparece como `untracked`:

       $ echo 'hello git' >> hello.txt

       $ git status
       On branch master

       No commits yet

       Untracked files:
         (use "git add <file>..." to include in what will be committed)

       	hello.txt

       nothing added to commit but untracked files present (use "git add" to track)

8. En este punto aun no hay nada agregado o modificado en la carpeta `.git`:

       $ tree .git
       .git
       ├── branches
       ├── config
       ├── description
       ├── HEAD
       ├── hooks
       ...
       ├── info
       │   └── exclude
       ├── objects
       │   ├── info
       │   └── pack
       └── refs
           ├── heads
           └── tags

9. Ahora ejecutamos `git add.` para añadir el archivo. El archivo agregado aparecerá como pendiente de confirmación, "Changes to be committed":

       $ git add .

       $ git status
       On branch master

       No commits yet

       Changes to be committed:
         (use "git rm --cached <file>..." to unstage)

       	new file:   hello.txt

10. Si miramos ahora el contenido de `.git`, vemos que se ha creado una entrada en la carpeta de objeto para el fichero que hemos añadido:

        $ tree .git
        .git
        ├── branches
        ├── config
        ├── description
        ├── HEAD
        ├── hooks
        ...
        ├── index
        ├── info
        │   └── exclude
        ├── objects
        │   ├── 8d
        │   │   └── 0e41234f24b6da002d962a26c2495ea16a425f
        │   ├── info
        │   └── pack
        └── refs
            ├── heads
            └── tags

11. Los ficheros de snapshot se guardan en la carpeta `objetcts` y cada fichero tiene su propia identificación calculada por git. Las primeras 2 letras de este ID sera el nombre de carpeta y el resto el nombre del archivo.
Vamos a ver el contenido del snapshot directamente en el objeto de git. Primero hay que verificar el tipo de archivo, pasando como parámetro una combinación del nombre de la carpeta (8d) y el nombre del archivo (0e412 ..):

        $ git cat-file -t 8d0e41234f24b6da002d962a26c2495ea16a425f
        blob

        $ git cat-file blob 8d0e41234f24b6da002d962a26c2495ea16a425f
        hello git

12. A continuación antes de hacer el commit del fichero, vamos a modificarlo:

        $ echo 'bye git' >> hello.txt

        $ git status
        On branch master

        No commits yet

        Changes to be committed:
          (use "git rm --cached <file>..." to unstage)

        	new file:   hello.txt

        Changes not staged for commit:
          (use "git add <file>..." to update what will be committed)
          (use "git checkout -- <file>..." to discard changes in working directory)

        	modified:   hello.txt

13. Con el siguiente comando podemos ver las diferencias de la modificación que hemos hecho:

        $ git diff hello.txt
        diff --git a/hello.txt b/hello.txt
        index 8d0e412..784aab9 100644
        --- a/hello.txt
        +++ b/hello.txt
        @@ -1 +1,2 @@
         hello git
        +bye git

  En este punto sólo tenemos un snapshot y git se da cuenta de que el fichero que contiene el snapshot ha sido modificado.


 14. Creamos otro snapshot que contenga el cambio y comprobamos el estado:

         $ git add .
         $ git status
         On branch master

         No commits yet

         Changes to be committed:
           (use "git rm --cached <file>..." to unstage)
                 new file:   hello.txt`

15. En este caso si se crea un nuevo objeto para el snapshot que acabamos de crear:

        $ tree .git
        .git
        ├── branches
        ├── config
        ├── description
        ├── HEAD
        ├── hooks
        ...
        ├── index
        ├── info
        │   └── exclude
        ├── objects
        │   ├── 78
        │   │   └── 4aab98519e195b37ab749d3f76d7cedca6858d
        │   ├── 8d
        │   │   └── 0e41234f24b6da002d962a26c2495ea16a425f
        │   ├── info
        │   └── pack
        └── refs
            ├── heads
            └── tags

16. Si comprobamos el nuevo objeto:

        $ git cat-file -t 784aab98519e195b37ab749d3f76d7cedca6858d
        blob

        $ git cat-file blob 784aab98519e195b37ab749d3f76d7cedca6858d
        hello git
        bye git

17. En este punto haremos un commit:

        $ git commit -m "commit hello.txt"
        [master (root-commit) 9fb6511] commit hello.txt
        1 file changed, 2 insertions(+)
        create mode 100644 hello.txt

        $ git status
        On branch master
        nothing to commit, working tree clean

19. Después del commit, podemos ver como se crean varios ficheros en la carpeta `.git`:

         $ tree .git
         .git
         ├── branches
         ├── COMMIT_EDITMSG
         ├── config
         ├── description
         ├── HEAD
         ├── hooks
         ...
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
         │   ├── 78
         │   │   └── 4aab98519e195b37ab749d3f76d7cedca6858d
         │   ├── 8d
         │   │   └── 0e41234f24b6da002d962a26c2495ea16a425f
         │   ├── 9f
         │   │   └── b6511a8cceff613bd047d9ed05c0a4495b3b49
         │   ├── info
         │   └── pack
         └── refs
             ├── heads
             │   └── master
             └── tags



   * La tabla de índices fue actualizado.
   * Se ha creado el fichero COMMIT_EDITMSG.
   * Se han creado nuevas carpetas en `objects`.
   * La carpeta master fue creada en`refs/heads`.

20. Ahora vamos a examinar el fichero del `commit`. Hay dos nuevos archivos creados, así que uno de ellos es del `commit`. Para identificar cual es hay que ejecutar `git cat-file -t <id>` (recordad que el id se forma juntando el nombre de la carpeta y el nombre del fichero), por ejemplo:

        $ git cat-file -t 9fb6511a8cceff613bd047d9ed05c0a4495b3b49
        commit
        $ git cat-file commit 9fb6511a8cceff613bd047d9ed05c0a4495b3b49
        tree 35d4b75f539702e8c15fc1c985e61cca603a2a3b
        author Lissette García <lissette.garcia@es.logicalis.com> 1608714591 +0100
        committer Lissette García <lissette.garcia@es.logicalis.com> 1608714591 +0100

        commit hello.txt

21. Examinemos el resto de ficheros creados:

   Contiene el commit id correspondiente al HEAD:
        $ cat .git/refs/heads/master
        9fb6511a8cceff613bd047d9ed05c0a4495b3b49

   Contienen la misma información, "el commitid correspondiente al HEAD":
        $ cat .git/logs/HEAD
        0000000000000000000000000000000000000000 9fb6511a8cceff613bd047d9ed05c0a4495b3b49 Lissette García <lissette.garcia@es.logicalis.com> 1608714591 +0100	commit (initial): commit hello.txt

        $ cat .git/logs/refs/heads/master
        0000000000000000000000000000000000000000 9fb6511a8cceff613bd047d9ed05c0a4495b3b49 Lissette García <lissette.garcia@es.logicalis.com> 1608714591 +0100	commit (initial): commit hello.txt

    Contienen el mensaje del commit:
        $ cat .git/COMMIT_EDITMSG
        commit hello.txt
