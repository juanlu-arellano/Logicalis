### Migración de SVN a GitLab

Si actualmente está utilizando un repositorio SVN, puede migrar el repositorio a Git y GitLab. La recomendación es que una vez que se migre el repositorio, los desarrolladores dejen de usar el repositor de SVN. De lo contrario, es difícil mantener la sincronizados en ambas direcciones.

1. Instalar los paquetes necesarios para la migración:

       $ sudo apt-get install git-core git-svn ruby
       $ sudo gem install svn2git

2. Preparar la lista con los autores del proyecto que se va a migrar para que los commits queden con los datos de los creadores:

       $ svn log --quiet | grep -E "r[0-9]+ \| .+ \|" | cut -d'|' -f2 | sed 's/ //g' | sort | uniq > users.txt

       o

       $ svn log http://10.0.2.15:8090/svn/project --xml --quiet | grep author | sort -u | perl -pe 's/.*>(.*?)<.*/$1 = /'> users.txt

3. Completar el fichero generado para que quede como en este ejemplo con los datos de todos los autores del proyecto:

       lgarciap = Lissette Garcia Porro <lissette@example.com>


4. Si el repositorio SVN está en el formato estándar (trunk, branches, tags, no agrupados) la conversión es simple. Para un repositorio no estándar, consulte la documentación de svn2git https://github.com/nirvdrum/svn2git. El siguiente comando se traerá el repositorio y realizará la conversión en el directorio de trabajo actual. Asegúrese de crear un nuevo directorio para cada repositorio antes de ejecutar el comando svn2git. El proceso de conversión llevará tiempo. Si el repositorio requiere usuario y password use las opciones `--username <username>` y `--password <password>` en el comando.

       $ svn2git https://svn.example.com/path/to/repo --authors /path/to/users.txt
       $ svn2git http://10.0.2.15:8090/svn/project --notrunk --nobranches --notags --authors ../users.txt
       o

       $ git svn clone http://10.0.2.15:8090/svn/project --no-metadata --authors-file=./users.txt

5. Crear un proyecto en gitlab y añadirlo como repositorio remoto del repo local creado. Finalmente hacer un push de todas las ramas y todos los tags:

       $ git remote add origin git@gitlab.com:<group>/<project>.git
       $ git push --all origin
       $ git push --tags origin
