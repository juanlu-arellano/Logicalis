### Instrucciones Laboratorio 3

### Ignorar ficheros

1. Crear un nuevo repositorio:

       $ mkdir repo-ignore
       $ cd repo-ignore
       $ git init
       $ mkdir results
       $ touch a.dat b.dat c.dat results/a.out results/b.out codigo.txt

2. Comprobar el estado de git:

       $ git status

       On branch master

       No commits yet

       Untracked files:
         (use "git add <file>..." to include in what will be committed)

       	a.dat
       	b.dat
       	c.dat
       	codigo.txt
       	results/

       nothing added to commit but untracked files present (use "git add" to track)

3. Vamos a indicar a Git que ignore determinados ficheros creando un archivo en el directorio raíz de nuestro proyecto llamado .gitignore:

       $ vi .gitignore

       *.dat
       results/

 Estos patrones le dicen a Git que ignore cualquier archivo cuyo nombre termine en .dat y todo lo que haya en el directorio results. (Si alguno de estos archivos ya estaba siendo rastreado, Git seguirá rastreándolos.)

4. Una vez que hemos creado este archivo, la salida de git status es mucho más limpia, ya no aparecen los ficheros que hemos indicado que se ignoren y aparece el fichero .gitignore que acabamos de crear:

       $ git status
       On branch master

       No commits yet

       Untracked files:
         (use "git add <file>..." to include in what will be committed)

       	.gitignore
       	codigo.txt

       nothing added to commit but untracked files present (use "git add" to track)

5. Podriamos no querer rastrear el fichero .gitignore, pero todos aquellos con los que compartimos nuestro repositorio probablemente desearán ignorar las mismas cosas que nosotros. Vamos a añadir y hacer “commit” de ambos ficheros:

       $ git add .
       $ git status
       On branch master

       No commits yet

       Changes to be committed:
         (use "git rm --cached <file>..." to unstage)

       	new file:   .gitignore
       	new file:   codigo.txt

       $ git commit -m "Add .gitignore file"
       $ git status

       # On branch master
       nothing to commit, working directory clean

6. Como ventaja, usar .gitignore nos ayuda a evitar añadir accidentalmente al repositorio los archivos que no queremos rastrear:

       $ git add a.dat

       The following paths are ignored by one of your .gitignore files:
       a.dat
       Use -f if you really want to add them.

7. Si realmente queremos anular la configuración de ignorar, podemos usar git add -f para obligar a Git a añadir algo:

       $ git add -f a.dat
       $ git status
       On branch master
       Changes to be committed:
         (use "git reset HEAD <file>..." to unstage)

       	new file:   a.dat


8. Para ver el estado de los archivos ignorados:

       $ git status --ignored
       On branch master
       Changes to be committed:
         (use "git reset HEAD <file>..." to unstage)

       	new file:   a.dat

       Ignored files:
         (use "git add -f <file>..." to include in what will be committed)

       	b.dat
       	c.dat
       	results/
