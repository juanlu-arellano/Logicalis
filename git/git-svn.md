### Uso del Git Bridge SVN

* Requisitos: Tener un repositorio SVN. En este ejemplo el repositorio de SVN es http://10.0.2.15:8090/svn/project/

1. Crear un directorio donde clonarnos el repositorio de SVN. El comando `git svn clone` ejecuta el equivalente de dos comandos: `git svn init` y `git svn fetch` en la URL que proporciones. Para un proyecto con cientos o miles de confirmaciones, esto puede demorar literalmente horas o incluso días. En este ejemplo estamos ignorando tags y branches (no existen):

       $ mkdir test-gitsvn
       $ cd test-gitsvn
       $ git svn clone http://10.0.2.15:8090/svn/project

2. Movernos al directorio donde se ha clonado el repositorio y comprobar el contenido:

       $ cd project
       $ ll

3. A partir de aqui se puede ejecutar comandos normales de git, por ejemplo:

       $ git branch
       * master

       $ git log --graph --pretty --oneline
       * e97c51a (HEAD -> master, git-svn) commit hola desde git
       * aa56f68 commit hola.txt
       * 5b3849c fichero creado desde git
       * 2d32072 subiendo otra vez new-file.txt
       * 8a80299 subiendo new-file.txt
       * 7ebd87d subiendo fichero.txt

       $ git svn log
       ------------------------------------------------------------------------
       r6 | lgarciap | 2021-01-16 17:32:53 +0100 (s<E1>b, 16 ene 2021) | 2 lines

       commit hola desde git

       ------------------------------------------------------------------------
       r5 | lgarciap | 2021-01-16 17:32:52 +0100 (s<E1>b, 16 ene 2021) | 2 lines

       commit hola.txt

       ------------------------------------------------------------------------
       r4 | lgarciap | 2021-01-16 17:26:35 +0100 (s<E1>b, 16 ene 2021) | 2 lines

       fichero creado desde git

       ------------------------------------------------------------------------
       r3 | lgarciap | 2021-01-16 16:25:43 +0100 (s<E1>b, 16 ene 2021) | 2 lines

       subiendo otra vez new-file.txt

       ------------------------------------------------------------------------
       r2 | lgarciap | 2021-01-16 14:57:28 +0100 (s<E1>b, 16 ene 2021) | 2 lines

       subiendo new-file.txt

       ------------------------------------------------------------------------
       r1 | lgarciap | 2021-01-16 14:53:42 +0100 (s<E1>b, 16 ene 2021) | 2 lines

       subiendo fichero.txt

       ------------------------------------------------------------------------


4. Para subir cambios al repositorio de SVN:

       $ echo "segunda linea" >>fichero.txt
       $ git add .
       $ git commit -m "segunda linea fichero.txt"
       $ git svn dcommit
       Committing to http://10.0.2.15:8090/svn/project ...
       	M	fichero.txt
       Committed r7
       	M	fichero.txt
       r7 = b342ba5fb1708357be98857c7cbca33ffd74d04e (refs/remotes/git-svn)
       No changes between f9e5c24dbe24864d6758bac8cb4df7a743f26afb and refs/remotes/git-svn
       Resetting to the latest refs/remotes/git-svn

5. Comprobar los logs:

       $ git log --graph --pretty --oneline
       * b342ba5 (HEAD -> master, git-svn) segunda linea fichero.txt
       * e97c51a commit hola desde git
       * aa56f68 commit hola.txt
       * 5b3849c fichero creado desde git
       * 2d32072 subiendo otra vez new-file.txt
       * 8a80299 subiendo new-file.txt
       * 7ebd87d subiendo fichero.txt

       $ git svn log
       ------------------------------------------------------------------------
       r7 | lgarciap | 2021-01-24 14:54:25 +0100 (dom, 24 ene 2021) | 2 lines

       segunda linea fichero.txt

       ------------------------------------------------------------------------
       r6 | lgarciap | 2021-01-16 17:32:53 +0100 (s<E1>b, 16 ene 2021) | 2 lines

       commit hola desde git

       ------------------------------------------------------------------------
       r5 | lgarciap | 2021-01-16 17:32:52 +0100 (s<E1>b, 16 ene 2021) | 2 lines

       commit hola.txt

       ------------------------------------------------------------------------
       r4 | lgarciap | 2021-01-16 17:26:35 +0100 (s<E1>b, 16 ene 2021) | 2 lines

       fichero creado desde git

       ------------------------------------------------------------------------
       r3 | lgarciap | 2021-01-16 16:25:43 +0100 (s<E1>b, 16 ene 2021) | 2 lines

       subiendo otra vez new-file.txt

       ------------------------------------------------------------------------
       r2 | lgarciap | 2021-01-16 14:57:28 +0100 (s<E1>b, 16 ene 2021) | 2 lines

       subiendo new-file.txt

       ------------------------------------------------------------------------
       r1 | lgarciap | 2021-01-16 14:53:42 +0100 (s<E1>b, 16 ene 2021) | 2 lines

       subiendo fichero.txt

       ------------------------------------------------------------------------
