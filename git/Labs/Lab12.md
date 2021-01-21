### Instrucciones Laboratorio 12

### Hooks

En este laboratorio vamos a poner en práctica el uso de Hooks

1. Crear un nuevo repositorio

       $ mkdir repo-myhooks
       $ cd repo-myhooks
       $ git init

2. Comprobar que se han creado las plantillas de **hooks** por defecto:

       tree .git/hooks/
       .git/hooks/
       ├── applypatch-msg.sample
       ├── commit-msg.sample
       ├── fsmonitor-watchman.sample
       ├── post-update.sample
       ├── pre-applypatch.sample
       ├── pre-commit.sample
       ├── prepare-commit-msg.sample
       ├── pre-push.sample
       ├── pre-rebase.sample
       ├── pre-receive.sample
       └── update.sample

3. A continuación crearemo un hook **prepare-commit-msg** en el que definiremos un mensaje que queremos que se añada a todos nuestros commits, en este caso será el nombre del usuario que hace el commit. Para ello crearemos el fichero `prepare-commit-msg` en el directorio `.git/hooks` con el siguiente contenido y le damos permisos de ejecución:

       $ vi .git/hooks/prepare-commit-msg

       #!/bin/sh

       SOB=$(git config user.name)
       grep -qs "^$SOB" "$1" || echo "- Creado por @$SOB" >> "$1"

       $ chmod +x .git/hooks/prepare-commit-msg

4. Ahora probaremos el hook creando un commit:

       $ echo "primer cambio en el repo" >>file
       $ cat file
       primer cambio en el repo

       $ git add .
       $ git commit -m "primer commit"
       [master (root-commit) 403db73] primer commit - Creado por @Lissette García
        1 file changed, 1 insertion(+)
        create mode 100644 file

       $ git log --graph --pretty --oneline
       * 403db73 (HEAD -> master) primer commit - Creado por @Lissette García

5. Vamos ahora a crear un hook **commit-msg** para que nuestros mensajes de commits cumplan con unos estandares. En este caso deben incluir algunas palabras claves. Para ello crearemos un fichero `commit-msg` en el directorio `.git/hooks` con el siguiente contenido y le damos permisos de ejecución:

       $ vi .git/hooks/commit-msg

       #!/bin/bash

       Color_Off='\033[0m'
       BRed="\033[1;31m"         # Red
       BGreen="\033[1;32m"       # Green
       BYellow="\033[1;33m"      # Yellow
       BBlue="\033[1;34m"        # Blue

       MSG_FILE=$1
       FILE_CONTENT="$(cat $MSG_FILE)"
       # Initialize constants here
       export REGEX='(Add: |Created: |Fix: |Update: |Rework: )'
       export ERROR_MSG="Commit message format must match regex \"${REGEX}\""
       if [[ $FILE_CONTENT =~ $REGEX ]]; then
        printf "${BGreen}Good commit!${Color_Off}"
       else
         printf "${BRed}Bad commit ${BBlue}\"$FILE_CONTENT\"\n"
        printf "${BYellow}$ERROR_MSG\n"
        printf "commit-msg hook failed (add --no-verify to bypass)\n"

        exit 1
       fi
       exit 0

       $ chmod +x .git/hooks/commit-msg

6. Vamos a probar el hook con un nuevo commit:

       $ echo "segundo cambio en el repo" >>file
       $ git add .
       $ git commit -m "segundo commit"
       Bad commit "segundo commit
       - Creado por @Lissette García"
       Commit message format must match regex "(Add: |Created: |Fix: |Update: |Rework: )"
       commit-msg hook failed (add --no-verify to bypass)

    El hook ha dado error porque no se cumple el formato definido para los commits. Si repetimos el commit, esta vez cumpliendo con el formato del mensaje el commit se realizará con exito:

       $ git commit -m "Update: segundo commit"
       Good commit![master c978946] Update: segundo commit - Creado por @Lissette García
        1 file changed, 1 insertion(+)
       $ git log --graph --pretty --oneline
       * c978946 (HEAD -> master) Update: segundo commit - Creado por @Lissette García
       * 403db73 primer commit - Creado por @Lissette García

7. En este punto vamos a crear un hook **pre-commit**, en el que vamos a revisar el código. Vamos a buscar si existen determinadas palabras (TODO y PENDING) que no queremos que existan en nuestro código a la hora de hacer el commit. Para ello crearemos un fichero `pre-commit` en el directorio `.git/hooks` con el siguiente contenido y le damos permisos de ejecución:

       $ vi .git/hooks/pre-commit

       #!/bin/bash

       # Git Shell Coloring
       RESTORE='\033[0m'    # Text Reset means no color change
       RED='\033[00;31m'	 # Red color code
       YELLOW='\033[00;33m' # yellow color code
       BLUE='\033[00;34m'   # blue color code

       FORBIDDEN=( 'TODO:' 'PENDING:' )
       FOUND=''

       for j in "${FORBIDDEN[@]}"
       do
         for i in `git diff --cached --name-only`
         do

           # the trick is here...use `git show :file` to output what is staged
           # test it against each of the FORBIDDEN strings ($j)

           if echo `git show :$i` | grep -q "$j"; then

             FOUND+="${BLUE}$i ${RED}contains ${RESTORE}\"$j\"${RESTORE}\n"

           fi
         done
       done

       # if FOUND is not empty, REJECT the COMMIT
       # PRINT the results (colorful-like)

       if [[ ! -z $FOUND ]]; then
         printf "${YELLOW}COMMIT REJECTED\n"
         printf "$FOUND"
         exit 1
       fi

       # nothing found? let the commit happen
       exit 0

       $ chmod +x .git/hooks/pre-commit

8. A continuación probaremos el hook creando un nuevo commit:

       $ echo "TODO: tercer cambio en el repo" >>file
       $ cat file
       primer cambio en el repo
       segundo cambio en el repo
       TODO: tercer cambio en el repo
       $ git add .
       $ git commit -m "Update: tercer commit"
       COMMIT REJECTED
       file contains "TODO:"

   El commit falla porque en nuestro fichero existe la palabra prohibida **TODO**. Para solucionarlo, editaremos el fichero, borraremos la palabra **TODO** y haremos nuevamente el commit:

       $ vi file   (borrar la palabra TODO)

       $ git status
       On branch master
       Changes to be committed:
         (use "git reset HEAD <file>..." to unstage)

       	modified:   file

       Changes not staged for commit:
         (use "git add <file>..." to update what will be committed)
         (use "git checkout -- <file>..." to discard changes in working directory)

       	modified:   file

       $ git add .
       $ git status
       On branch master
       Changes to be committed:
         (use "git reset HEAD <file>..." to unstage)

       	modified:   file

       $ git commit -m "Update: tercer commit"
       Good commit![master f8eb811] Update: tercer commit - Creado por @Lissette García
        1 file changed, 1 insertion(+)
       $ git log --graph --pretty --oneline
       * f8eb811 (HEAD -> master) Update: tercer commit - Creado por @Lissette García
       * c978946 Update: segundo commit - Creado por @Lissette García
       * 403db73 primer commit - Creado por @Lissette García

9. Ahora vamos a añidir un repositorio remoto a nuestro repo. Para ello crearos un nuevo repo en vuestra cuenta en github y añadirlo como repositorio remoto, por ejemplo:

       $ git remote add origin https://github.com/lissettegar/repo-myhooks.git
       $ git remote -v
       origin	https://github.com/lissettegar/repo-myhooks.git (fetch)
       origin	https://github.com/lissettegar/repo-myhooks.git (push)

10. En este punto nos vamos a crear un hook **pre-push**. Este hook evitará que se pueda hacer git push a la rama `master` y que además a la rama `develop` solo pueda hacer push de lunes a jueves. Para ello crearemos un fichero `pre-push` en el directorio `.git/hooks` con el siguiente contenido y le damos permisos de ejecución:

        $ vi .git/hooks/pre-push

        #!/bin/sh

        echo "Skip pre-push hooks with --no-verify (not recommended).\n"

        BRANCH=`git rev-parse --abbrev-ref HEAD`
        if [ "$BRANCH" = "master" ];
        then
          echo "You are on branch $BRANCH. You must not push to master\n"
          exit 1
        fi

        if [ `date +%w` -ge 5 ] && [ "$BRANCH" = "develop" ];
        then
          echo "Please, do not push code to develop before the weekend!\n"
          exit 1
        fi

        $ chmod +x .git/hooks/pre-push

11. Probemos el push de la rama master y comprobemos que falla tal y como hemos indicado en el hook pre-push:

        $ git push origin master
        Username for 'https://github.com': lissettegar
        Password for 'https://lissettegar@github.com':
        Skip pre-push hooks with --no-verify (not recommended).

        You are on branch master. You must not push to master

        error: failed to push some refs to 'https://github.com/lissettegar/repo-myhooks.git'

12. A continuación crearemos la rama **develop** y haremos push de esa rama. No nos dará error porque no es la rama master y no es fin de semana:

        $ git checkout -b develop
        Switched to a new branch 'develop'

        $ git branch
        * develop
          master

        $ git push origin develop
        Username for 'https://github.com': lissettegar
        Password for 'https://lissettegar@github.com':
        Skip pre-push hooks with --no-verify (not recommended).

        Counting objects: 9, done.
        Delta compression using up to 8 threads.
        Compressing objects: 100% (5/5), done.
        Writing objects: 100% (9/9), 737 bytes | 737.00 KiB/s, done.
        Total 9 (delta 2), reused 0 (delta 0)
        remote: Resolving deltas: 100% (2/2), done.
        To https://github.com/lissettegar/repo-myhooks.git
         * [new branch]      develop -> develop

         $ git branch -a
         * develop
           master
           remotes/origin/develop
