### Instrucciones Laboratorio 4

### Historial de confirmaciones

1. En el mismo repositorio de los labs anteriores, añadir un fichero al repositorio:

       $ echo 'version: 1.0' > version

       $ cat version
       version: 1.0
       $ git status
       On branch master
       Untracked files:
         (use "git add <file>..." to include in what will be committed)

       	version

       nothing added to commit but untracked files present (use "git add" to track)

       $ git add .
       $ git commit -m "verion 1.0"
       [master eec021c] verion 1.0
        1 file changed, 1 insertion(+)
        create mode 100644 version

2. Añadir los cambios correspondientes a una nueva version:

       $ echo 'segunda linea' >> cambios.txt
       $ git status
       On branch master
       Changes not staged for commit:
         (use "git add <file>..." to update what will be committed)
         (use "git checkout -- <file>..." to discard changes in working directory)

       	modified:   cambios.txt

       no changes added to commit (use "git add" and/or "git commit -a")

       $ echo 'version: 2.0' > version

       $ cat version
       version: 2.0

       $ git status
       On branch master
       Changes not staged for commit:
         (use "git add <file>..." to update what will be committed)
         (use "git checkout -- <file>..." to discard changes in working directory)

       	modified:   cambios.txt
       	modified:   version

       no changes added to commit (use "git add" and/or "git commit -a")

       $ git diff
       diff --git a/cambios.txt b/cambios.txt
       index 6e90583..c1e5451 100644
       --- a/cambios.txt
       +++ b/cambios.txt
       @@ -1 +1,2 @@
        primera linea
       +segunda linea
       diff --git a/version b/version
       index a5ef50f..6ec12fe 100644
       --- a/version
       +++ b/version
       @@ -1 +1 @@
       -version: 1.0
       +version: 2.0

       $ git add .
       $ git commit -m "version 2.0"
       [master 4f73fa5] version 2.0
       2 files changed, 2 insertions(+), 1 deletion(-)

3. Consultar el historial de confirmaciones:

       $ git log
       commit 4f73fa5f9d9350f5bf8b3747967f28b173f18f0b (HEAD -> master)
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Nov 18 10:39:20 2020 +0100

           version 2.0

       commit eec021cc70adec40e44c2cdead96d3efa2100606
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Nov 18 10:36:34 2020 +0100

           verion 1.0

       commit a4522872d7c80eef8e17b3d12baa81ba53b81008
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Nov 18 09:23:31 2020 +0100

           commit hello.txt and cambios.txt

4. Consultar el historial de confirmaciones mostrando los cambios de cada una:

       $ git log -p
       commit 4f73fa5f9d9350f5bf8b3747967f28b173f18f0b (HEAD -> master)
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Nov 18 10:39:20 2020 +0100

           version 2.0

       diff --git a/cambios.txt b/cambios.txt
       index 6e90583..c1e5451 100644
       --- a/cambios.txt
       +++ b/cambios.txt
       @@ -1 +1,2 @@
        primera linea
       +segunda linea
       diff --git a/version b/version
       index a5ef50f..6ec12fe 100644
       --- a/version
       +++ b/version
       @@ -1 +1 @@
       -version: 1.0
       +version: 2.0

       commit eec021cc70adec40e44c2cdead96d3efa2100606
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Nov 18 10:36:34 2020 +0100

           verion 1.0

       diff --git a/version b/version
       new file mode 100644
       index 0000000..a5ef50f
       --- /dev/null
       +++ b/version
       @@ -0,0 +1 @@
       +version: 1.0

       commit a4522872d7c80eef8e17b3d12baa81ba53b81008
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Nov 18 09:23:31 2020 +0100

           commit hello.txt and cambios.txt

       diff --git a/cambios.txt b/cambios.txt
       new file mode 100644
       index 0000000..6e90583
       --- /dev/null
       +++ b/cambios.txt
       @@ -0,0 +1 @@
       +primera linea
       diff --git a/hello.txt b/hello.txt
       new file mode 100644
       index 0000000..784aab9
       --- /dev/null
       +++ b/hello.txt
       @@ -0,0 +1,2 @@
       +hello git
       +bye git

5. Consultar unicamente los últimos 2 commits:

       $ git log -p -2
       commit 4f73fa5f9d9350f5bf8b3747967f28b173f18f0b (HEAD -> master)
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Nov 18 10:39:20 2020 +0100

           version 2.0

       diff --git a/cambios.txt b/cambios.txt
       index 6e90583..c1e5451 100644
       --- a/cambios.txt
       +++ b/cambios.txt
       @@ -1 +1,2 @@
        primera linea
       +segunda linea
       diff --git a/version b/version
       index a5ef50f..6ec12fe 100644
       --- a/version
       +++ b/version
       @@ -1 +1 @@
       -version: 1.0
       +version: 2.0

       commit eec021cc70adec40e44c2cdead96d3efa2100606
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Nov 18 10:36:34 2020 +0100

           verion 1.0

       diff --git a/version b/version
       new file mode 100644
       index 0000000..a5ef50f
       --- /dev/null
       +++ b/version
       @@ -0,0 +1 @@
       +version: 1.0

6. Mostrando estadisticas:

       $ git log --stat
       commit 4f73fa5f9d9350f5bf8b3747967f28b173f18f0b (HEAD -> master)
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Nov 18 10:39:20 2020 +0100

           version 2.0

        cambios.txt | 1 +
        version     | 2 +-
        2 files changed, 2 insertions(+), 1 deletion(-)

       commit eec021cc70adec40e44c2cdead96d3efa2100606
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Nov 18 10:36:34 2020 +0100

           verion 1.0

        version | 1 +
        1 file changed, 1 insertion(+)

       commit a4522872d7c80eef8e17b3d12baa81ba53b81008
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Nov 18 09:23:31 2020 +0100

           commit hello.txt and cambios.txt

       cambios.txt | 1 +
       hello.txt   | 2 ++
       2 files changed, 3 insertions(+)

7. Mostrando resumen de estadisticas:

       $ git log --shortstat
       commit 4f73fa5f9d9350f5bf8b3747967f28b173f18f0b (HEAD -> master)
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Nov 18 10:39:20 2020 +0100

           version 2.0

        2 files changed, 2 insertions(+), 1 deletion(-)

       commit eec021cc70adec40e44c2cdead96d3efa2100606
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Nov 18 10:36:34 2020 +0100

           verion 1.0

        1 file changed, 1 insertion(+)

       commit a4522872d7c80eef8e17b3d12baa81ba53b81008
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Nov 18 09:23:31 2020 +0100

           commit hello.txt and cambios.txt

        2 files changed, 3 insertions(+)


 8. Mostrando sólo los nombres de los ficheros:

        $ git log --name-only
        commit 4f73fa5f9d9350f5bf8b3747967f28b173f18f0b (HEAD -> master)
        Author: Lissette García <lissette.garcia@es.logicalis.com>
        Date:   Wed Nov 18 10:39:20 2020 +0100

            version 2.0

        cambios.txt
        version

        commit eec021cc70adec40e44c2cdead96d3efa2100606
        Author: Lissette García <lissette.garcia@es.logicalis.com>
        Date:   Wed Nov 18 10:36:34 2020 +0100

            verion 1.0

        version

        commit a4522872d7c80eef8e17b3d12baa81ba53b81008
        Author: Lissette García <lissette.garcia@es.logicalis.com>
        Date:   Wed Nov 18 09:23:31 2020 +0100

            commit hello.txt and cambios.txt

        cambios.txt
        hello.txt

9. Mostrando los ficheros y si fueron añadidos, modificados, etc:

       $ git log --name-status
       commit 4f73fa5f9d9350f5bf8b3747967f28b173f18f0b (HEAD -> master)
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Nov 18 10:39:20 2020 +0100

           version 2.0

       M       cambios.txt
       M       version

       commit eec021cc70adec40e44c2cdead96d3efa2100606
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Nov 18 10:36:34 2020 +0100

           verion 1.0

       A       version

       commit a4522872d7c80eef8e17b3d12baa81ba53b81008
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Nov 18 09:23:31 2020 +0100

           commit hello.txt and cambios.txt

       A       cambios.txt
       A       hello.txt

10. Mostrando el hash del commit de forma abreviada:

        $ git log --abbrev-commit
        commit 4f73fa5 (HEAD -> master)
        Author: Lissette García <lissette.garcia@es.logicalis.com>
        Date:   Wed Nov 18 10:39:20 2020 +0100

            version 2.0

        commit eec021c
        Author: Lissette García <lissette.garcia@es.logicalis.com>
        Date:   Wed Nov 18 10:36:34 2020 +0100

            verion 1.0

        commit a452287
        Author: Lissette García <lissette.garcia@es.logicalis.com>
        Date:   Wed Nov 18 09:23:31 2020 +0100

            commit hello.txt and cambios.txt

11. Mostrandolos con fechas relativas:

        $ git log --relative-date
        commit 4f73fa5f9d9350f5bf8b3747967f28b173f18f0b (HEAD -> master)
        Author: Lissette García <lissette.garcia@es.logicalis.com>
        Date:   22 minutes ago

            version 2.0

        commit eec021cc70adec40e44c2cdead96d3efa2100606
        Author: Lissette García <lissette.garcia@es.logicalis.com>
        Date:   25 minutes ago

            verion 1.0

        commit a4522872d7c80eef8e17b3d12baa81ba53b81008
        Author: Lissette García <lissette.garcia@es.logicalis.com>
        Date:   2 hours ago

            commit hello.txt and cambios.txt

12. Mostrandolos con fechas relativas y commit abreviado:

        $ git log --relative-date --abbrev-commit
        commit 4f73fa5 (HEAD -> master)
        Author: Lissette García <lissette.garcia@es.logicalis.com>
        Date:   23 minutes ago

            version 2.0

        commit eec021c
        Author: Lissette García <lissette.garcia@es.logicalis.com>
        Date:   25 minutes ago

            verion 1.0

        commit a452287
        Author: Lissette García <lissette.garcia@es.logicalis.com>
        Date:   2 hours ago

            commit hello.txt and cambios.txt

13. Mostrando grafico ASCII:

        $ git log --graph
        * commit 4f73fa5f9d9350f5bf8b3747967f28b173f18f0b (HEAD -> master)
        | Author: Lissette García <lissette.garcia@es.logicalis.com>
        | Date:   Wed Nov 18 10:39:20 2020 +0100
        |
        |     version 2.0
        |
        * commit eec021cc70adec40e44c2cdead96d3efa2100606
        | Author: Lissette García <lissette.garcia@es.logicalis.com>
        | Date:   Wed Nov 18 10:36:34 2020 +0100
        |
        |     verion 1.0
        |
        * commit a4522872d7c80eef8e17b3d12baa81ba53b81008
          Author: Lissette García <lissette.garcia@es.logicalis.com>
          Date:   Wed Nov 18 09:23:31 2020 +0100

              commit hello.txt and cambios.txt

14. Mostrandolos en formato alternativo:

        $ git log --pretty=fuller
        commit 4f73fa5f9d9350f5bf8b3747967f28b173f18f0b (HEAD -> master)
        Author:     Lissette García <lissette.garcia@es.logicalis.com>
        AuthorDate: Wed Nov 18 10:39:20 2020 +0100
        Commit:     Lissette García <lissette.garcia@es.logicalis.com>
        CommitDate: Wed Nov 18 10:39:20 2020 +0100

            version 2.0

        commit eec021cc70adec40e44c2cdead96d3efa2100606
        Author:     Lissette García <lissette.garcia@es.logicalis.com>
        AuthorDate: Wed Nov 18 10:36:34 2020 +0100
        Commit:     Lissette García <lissette.garcia@es.logicalis.com>
        CommitDate: Wed Nov 18 10:36:34 2020 +0100

            verion 1.0

        commit a4522872d7c80eef8e17b3d12baa81ba53b81008
        Author:     Lissette García <lissette.garcia@es.logicalis.com>
        AuthorDate: Wed Nov 18 09:23:31 2020 +0100
        Commit:     Lissette García <lissette.garcia@es.logicalis.com>
        CommitDate: Wed Nov 18 09:30:59 2020 +0100

            commit hello.txt and cambios.txt

        $ git log --pretty=oneline
        4f73fa5f9d9350f5bf8b3747967f28b173f18f0b (HEAD -> master) version 2.0
        eec021cc70adec40e44c2cdead96d3efa2100606 verion 1.0
        a4522872d7c80eef8e17b3d12baa81ba53b81008 commit hello.txt and cambios.txt

        $ git log --pretty=short
        commit 4f73fa5f9d9350f5bf8b3747967f28b173f18f0b (HEAD -> master)
        Author: Lissette García <lissette.garcia@es.logicalis.com>

            version 2.0

        commit eec021cc70adec40e44c2cdead96d3efa2100606
        Author: Lissette García <lissette.garcia@es.logicalis.com>

            verion 1.0

        commit a4522872d7c80eef8e17b3d12baa81ba53b81008
        Author: Lissette García <lissette.garcia@es.logicalis.com>

            commit hello.txt and cambios.txt

15. Mostrandolos en formatos personalizados:

        $ git log --pretty=format:"%h %s" --graph
        * 4f73fa5 version 2.0
        * eec021c verion 1.0
        * a452287 commit hello.txt and cambios.txt

        $ git log --pretty=format:"%h - %an, %ar : %s"
        4f73fa5 - Lissette García, 29 minutes ago : version 2.0
        eec021c - Lissette García, 32 minutes ago : verion 1.0
        a452287 - Lissette García, 2 hours ago : commit hello.txt and cambios.txt

        $ git log -Sversion
        commit eec021cc70adec40e44c2cdead96d3efa2100606
        Author: Lissette García <lissette.garcia@es.logicalis.com>
        Date:   Wed Nov 18 10:36:34 2020 +0100

            verion 1.0
