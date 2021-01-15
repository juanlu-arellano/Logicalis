### Instrucciones Laboratorio 13

* Prerequisito: Haber terminado los labs anteriores.

### Herramientas de Git

En este laboratorio vamos a prácticar el uso de referencias de git, como hacer busquedas de cadenas de texto y como hacer debug.

1. Lo primero que haremos será comprobar que estamos en la rama `dev` y ver cual es el commit ID de nuestro `HEAD` y comprobaremos que nuestra rama `dev` y el `HEAD` apuntan al mismo commit:

       $ git branch
       * dev
         fix-02
         master
         new-feature

       $ git show HEAD
       commit 23b1cbb542b4d67187511fb4026d0a09d31f0688 (HEAD -> dev, origin/dev)
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Fri Jan 8 10:48:54 2021 +0100

           new feature

       diff --git a/new-feature.txt b/new-feature.txt
       new file mode 100644
       index 0000000..e4d8265
       --- /dev/null
       +++ b/new-feature.txt
       @@ -0,0 +1 @@
       +nueva feature

       $ git show dev
       commit 23b1cbb542b4d67187511fb4026d0a09d31f0688 (HEAD -> dev, origin/dev)
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Fri Jan 8 10:48:54 2021 +0100

           new feature

       diff --git a/new-feature.txt b/new-feature.txt
       new file mode 100644
       index 0000000..e4d8265
       --- /dev/null
       +++ b/new-feature.txt
       @@ -0,0 +1 @@
       +nueva feature

2. Para obtener los reflogs del HEAD:

       $ git reflog
       23b1cbb (HEAD -> dev, origin/dev) HEAD@{0}: cherry-pick: new feature
       3080ead HEAD@{1}: commit: nuevos cambios en la rama dev
       b74700d (tag: v4.0, origin/master, master) HEAD@{2}: reset: moving to HEAD~
       3330f4a HEAD@{3}: checkout: moving from new-feature to dev
       bade9da (new-feature) HEAD@{4}: checkout: moving from dev to new-feature
       3330f4a HEAD@{5}: cherry-pick: new feature
       b74700d (tag: v4.0, origin/master, master) HEAD@{6}: checkout: moving from new-feature to dev
       bade9da (new-feature) HEAD@{7}: commit: new feature add
       7dd4ce7 HEAD@{8}: commit: new feature
       b74700d (tag: v4.0, origin/master, master) HEAD@{9}: checkout: moving from dev to new-feature
       b74700d (tag: v4.0, origin/master, master) HEAD@{10}: checkout: moving from master to dev
       b74700d (tag: v4.0, origin/master, master) HEAD@{11}: checkout: moving from 0e163421dc15802ebc9d70fc3f70da60d5ba7dbe to master
       0e16342 HEAD@{12}: checkout: moving from 187e4dd8cee90395d3f8cf7cea6c4b1ae07360e3 to 0e163421dc15802ebc9d70fc3f70da60d5ba7dbe
       187e4dd HEAD@{13}: checkout: moving from 059c91225e945a85b2c434cd8c5b401a269a9967 to 187e4dd8cee90395d3f8cf7cea6c4b1ae07360e3
       059c912 HEAD@{14}: checkout: moving from 91f9d8782952ec1f8aee3b2a12a2a22100a36bf9 to 059c91225e945a85b2c434cd8c5b401a269a9967
       91f9d87 HEAD@{15}: checkout: moving from master to 91f9d8782952ec1f8aee3b2a12a2a22100a36bf9
       b74700d (tag: v4.0, origin/master, master) HEAD@{16}: merge dev: Fast-forward
       059c912 HEAD@{17}: checkout: moving from dev to master
       b74700d (tag: v4.0, origin/master, master) HEAD@{18}: commit: version 4.0
       .
       .
       .

3. A continuación vamos a obtener los reflogs de la rama `master`:

       $ git reflog master
       b74700d (tag: v4.0, origin/master, master) master@{0}: merge dev: Fast-forward
       059c912 master@{1}: commit (merge): Merge branch dev in master
       91f9d87 master@{2}: commit: Hola desde rama master
       b27cdea master@{3}: reset: moving to b27cdea
       e06561a master@{4}: merge dev: Merge made by the 'recursive' strategy.
       b27cdea master@{5}: merge fix-01: Fast-forward
       1e12a67 master@{6}: commit: nuevo commit rama master
       a07178f (tag: v3.0) master@{7}: commit: version 3.0
       3c6a550 master@{8}: pull origin master: Merge made by the 'recursive' strategy.
       8b9e417 master@{9}: commit: Create file from-local.md
       b986b73 (tag: v2.0) master@{10}: commit: version 2.0
       aaae799 (tag: v1.0) master@{11}: commit: version 1.0
       3a1666d master@{12}: commit (amend): commit hello.txt and cambios.txt
       9fb6511 master@{13}: commit (initial): commit hello.txt

4. Si queremos obtener mas información de un commit en concreto podemos por ejemplo usar el reflog:

       $ git show master@{1}
       commit 059c91225e945a85b2c434cd8c5b401a269a9967
       Merge: 91f9d87 187e4dd
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Dec 23 13:38:40 2020 +0100

           Merge branch dev in master

       diff --cc hello.txt
       index 8690765,90a792b..9a5b603
       --- a/hello.txt
       +++ b/hello.txt
       @@@ -1,1 -1,1 +1,2 @@@
        +Hola rama master
       + Hola rama dev

       $ git show HEAD@{1}
       commit 3080ead7d6124d024c753064714cffd67296603f
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Fri Jan 8 12:14:34 2021 +0100

           nuevos cambios en la rama dev

       diff --git a/news.txt b/news.txt
       index 0a02199..3668580 100644
       --- a/news.txt
       +++ b/news.txt
       @@ -1 +1,2 @@
        nueva info en rama dev
       +nuevos cambios en la rama dev

5. Tambien podemos obtener información del estado de una rama en un momento del tiempo:

       $ git show dev@{2.hour.ago}
       commit 3330f4a3b13e0015047c7fcffa9155f1e5c676cf
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Fri Jan 8 10:48:54 2021 +0100

           new feature

       diff --git a/new-feature.txt b/new-feature.txt
       new file mode 100644
       index 0000000..e4d8265
       --- /dev/null
       +++ b/new-feature.txt
       @@ -0,0 +1 @@
       +nueva feature

       $ git show dev@{3.hour.ago}
       commit b74700d021363d9688c95f1aac3ed2c8815a6534 (tag: v4.0, origin/master, master)
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Thu Dec 24 12:03:29 2020 +0100

           version 4.0

       diff --git a/version b/version
       index a3ea086..8d1e0a0 100644
       --- a/version
       +++ b/version
       @@ -1 +1,2 @@
        version: 3.0
       +version 4.0

6. Otra forma de referenciar commits es usando los caracteres `^` y `~`. Veamos varios ejemplos de como usar el `^`. Para ellos vamos a localizar en nuestro repo un commit en concreto. En este caso queremos el commit ID del commit que tiene como mensaje **Merge branch dev in master**:

       $ git log --graph --pretty --oneline --grep="Merge branch dev in master"
       *   059c912 Merge branch dev in master

 En mi caso es el **059c912**. Hemos elegido este commit porque tiene 2 padres al ser un commit creado con un merge. Ejecutad los siguientes comandos para vuestro commit:

       $ git show 059c912
       commit 059c91225e945a85b2c434cd8c5b401a269a9967
       Merge: 91f9d87 187e4dd
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Dec 23 13:38:40 2020 +0100

           Merge branch dev in master

       diff --cc hello.txt
       index 8690765,90a792b..9a5b603
       --- a/hello.txt
       +++ b/hello.txt
       @@@ -1,1 -1,1 +1,2 @@@
        +Hola rama master
       + Hola rama dev

 Muestra info del primer padre del commit **059c912**:

       $ git show 059c912^		 
       commit 91f9d8782952ec1f8aee3b2a12a2a22100a36bf9
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Dec 23 13:33:16 2020 +0100

           Hola desde rama master

       diff --git a/hello.txt b/hello.txt
       index 784aab9..8690765 100644
       --- a/hello.txt
       +++ b/hello.txt
       @@ -1,2 +1 @@
       -hello git
       -bye git
       +Hola rama master

 Muestra info del primer padre del primer padre del commit **059c912**:

       $ git show 059c912^^		
       commit b27cdea08c670fc107e1d8bf26c6cc1db1a52888
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Dec 23 13:08:30 2020 +0100

           fix hecho en la rama fix-01

       diff --git a/new-commit.txt b/new-commit.txt
       index 0462e1a..9925690 100644
       --- a/new-commit.txt
       +++ b/new-commit.txt
       @@ -1 +1,2 @@
        nuevo commit rama master
       +fix hecho en la rama fix-01

 Muestra info del segundo padre del commit **059c912**:

       $ git show 059c912^2		
       commit 187e4dd8cee90395d3f8cf7cea6c4b1ae07360e3
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Dec 23 13:20:10 2020 +0100

           Hola desde rama dev

       diff --git a/hello.txt b/hello.txt
       index 784aab9..90a792b 100644
       --- a/hello.txt
       +++ b/hello.txt
       @@ -1,2 +1 @@
       -hello git
       -bye git
       +Hola rama dev

7. Ahora vamos a usar el caracter `~` y lo combinaremos con el `^`. Vamos a buscar otro commit, por ejemplo el que tiene el mensaje "tercera linea en cambios.txt":

       $ git log --graph --pretty --oneline |grep "tercera linea en cambios.txt"
       * d14c280 tercera linea en cambios.txt

 En mi caso el commit a usar es el **d14c280**. Ejecutad los siguientes comandos para vuestro commit:

 Muestra info del primer padre del commit **d14c280**:

       $ git show d14c280~
       commit 3a4c264f64447122fbf4fd7b4c2d75871154b65a
       Merge: c040330 059c912
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Thu Dec 24 11:22:29 2020 +0100

           Merge branch 'master' into dev

 Muestra info del primer padre del commit **d14c280**:

       $ git show d14c280~~
       commit c040330e6ef621d56d4fc263176e0ee675b3a637
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Wed Dec 23 14:54:53 2020 +0100

           New brach experimental

       diff --git a/exp.txt b/exp.txt
       new file mode 100644
       index 0000000..68149a4
       --- /dev/null
       +++ b/exp.txt
       @@ -0,0 +1 @@
       +creando nueva rama experimental

  Muestra info del segundo padre del primer padre del commit **d14c280** (la salida de ambos comandos es la misma):

       $ git show d14c280~^2
       $ git show d14c280^^2

  Muestra info del primer padre del segundo padre del primer padre del commit **d14c280**:

       $ git show d14c280~^2~

  Muestra info del segundo padre del segundo padre del primer padre del commit **d14c280**:

       $ git show d14c280~^2^2

8. A continuación vamos a usar las referencias para ver que diferencias hay entre ramas.

 Usando los `2 puntos`, para ver que commits tiene la `dev` que no están aun en la rama `master`:

       $ git log master..dev
       commit 23b1cbb542b4d67187511fb4026d0a09d31f0688 (HEAD -> dev, origin/dev)
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Fri Jan 8 10:48:54 2021 +0100

           new feature

       commit 3080ead7d6124d024c753064714cffd67296603f
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Fri Jan 8 12:14:34 2021 +0100

           nuevos cambios en la rama dev

 Usando `^` y `--not` para ver que commits tiene la `dev` que no están aun en la rama `master` (la salida de ambos comandos es igual):

       $ git log ^master dev
       $ git log dev --not master
       commit 23b1cbb542b4d67187511fb4026d0a09d31f0688 (HEAD -> dev, origin/dev)
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Fri Jan 8 10:48:54 2021 +0100

           new feature

       commit 3080ead7d6124d024c753064714cffd67296603f
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Fri Jan 8 12:14:34 2021 +0100

           nuevos cambios en la rama dev

 Para ver que commits tiene 2 ramas `dev` y `new-feature` que no tiene una tercera `master` (la salida de ambos comandos es igual):

       $ git log dev new-feature ^master
       $ git log dev new-feature --not master
       commit 23b1cbb542b4d67187511fb4026d0a09d31f0688 (HEAD -> dev, origin/dev)
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Fri Jan 8 10:48:54 2021 +0100

           new feature

       commit 3080ead7d6124d024c753064714cffd67296603f
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Fri Jan 8 12:14:34 2021 +0100

           nuevos cambios en la rama dev

       commit bade9da233aa9cc58bf7a902758b5aeefef14e67 (new-feature)
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Fri Jan 8 11:00:37 2021 +0100

           new feature add

       commit 7dd4ce75eefc86d521bcd58aa975d2a0940a6b72
       Author: Lissette García <lissette.garcia@es.logicalis.com>
       Date:   Fri Jan 8 10:48:54 2021 +0100

           new feature

 Usando tres puntos `...`, permite ver todos los commits que son alcanzables por alguna de dos referencias, pero no por las dos al mismo tiempo:

       $ git log master...dev --oneline
       23b1cbb (HEAD -> dev, origin/dev) new feature
       3080ead nuevos cambios en la rama dev

       $ git log --left-right new-feature...dev --oneline
       > 23b1cbb (HEAD -> dev, origin/dev) new feature
       > 3080ead nuevos cambios en la rama dev
       < bade9da (new-feature) new feature add
       < 7dd4ce7 new feature

9. Para buscar cadenas o expresiones dentro de los ficheros de nuestro repo podemos usar el comando `gir grep`:

 Buscar la cantidad de veces que aparece la cadena `hola`:

        $ git grep --count -i hola
        from-github.md:1
        from-local.md:1
        hello.txt:2

 Buscar todas las apariciones de la cadena `hola`:

       $ git grep -in hola
       from-github.md:1:## hola desde github
       from-local.md:1:## hola desde local
       hello.txt:1:Hola rama master
       hello.txt:2:Hola rama dev

 Buscar todas las apariciones de la cadena `hola` en distintas versiones:

       $ git grep -in hola v3.0
       v3.0:from-github.md:1:## hola desde github
       v3.0:from-local.md:1:## hola desde local

       $ git grep -in hola v4.0
       v4.0:from-github.md:1:## hola desde github
       v4.0:from-local.md:1:## hola desde local
       v4.0:hello.txt:1:Hola rama master
       v4.0:hello.txt:2:Hola rama dev

10. Para buscar cadenas o nombres de funciones en el histórico usaremos las opciones `-L` y `-S` del comando `git log`:

 Para buscar la cadena `version` en el fichero version:

        $ git log -L /version/:version
        commit b74700d021363d9688c95f1aac3ed2c8815a6534 (tag: v4.0, origin/master, master)
        Author: Lissette García <lissette.garcia@es.logicalis.com>
        Date:   Thu Dec 24 12:03:29 2020 +0100

            version 4.0

        diff --git a/version b/version
        --- a/version
        +++ b/version
        @@ -1,1 +1,2 @@
         version: 3.0
        +version 4.0

        commit a07178fa70319546a6e4c72b4893f9fa2d8b53a9 (tag: v3.0)
        Author: Lissette García <lissette.garcia@es.logicalis.com>
        Date:   Wed Dec 23 11:54:08 2020 +0100

            version 3.0

        diff --git a/version b/version
        --- a/version
        +++ b/version
        @@ -1,1 +1,1 @@
        -version: 2.0
        +version: 3.0

        commit b986b73fc0e81eb765d96057b46309efa8abe0c4 (tag: v2.0)
        Author: Lissette García <lissette.garcia@es.logicalis.com>
        Date:   Wed Dec 23 11:15:37 2020 +0100

            version 2.0

        diff --git a/version b/version
        --- a/version
        +++ b/version
        @@ -1,1 +1,1 @@
        -version: 1.0
        +version: 2.0

        commit aaae799b5936836415e13d7e41117afe02cb83e9 (tag: v1.0)
        Author: Lissette García <lissette.garcia@es.logicalis.com>
        Date:   Wed Dec 23 11:12:38 2020 +0100

            version 1.0

        diff --git a/version b/version
        --- /dev/null
        +++ b/version
        @@ -0,0 +1,1 @@
        +version: 1.0

 Mostrar las confirmaciones que introdujeron un cambio en el código que agregó o eliminó alguna linea que contine la cadena `linea`:

        $ git log -Slinea --oneline
        d14c280 tercera linea en cambios.txt
        b986b73 (tag: v2.0) version 2.0
        3a1666d commit hello.txt and cambios.txt

11. Por último vamos practicar el uso del comando `git bisect`, esta es una herramienta de debug muy útil que se utiliza para encontrar en qué commit fue el primero en el que se introdujo un error o problema. En este ejemplo, como no tenemos código real, lo que vamos a  buscar es el commit donde se introdujo la cadena "tercera linea".

 Lo primero es arrancar el bisect e indicar que el commit en el que nos encontramos está mal, es donde hemos detectado el fallo y lo que queremos es encontrar el primer commit donde aparece el fallo:

        $ git bisect start
        $ git bisect bad

        $ git log --graph --pretty --oneline
        * 23b1cbb (HEAD -> dev, origin/dev) new feature
        * 3080ead nuevos cambios en la rama dev
        * b74700d (tag: v4.0, origin/master, master) version 4.0
        * cb4cb70 (fix-02) fix hecho en la rama fix-02
        * d14c280 tercera linea en cambios.txt
        *   3a4c264 Merge branch 'master' into dev
        |\  
        | *   059c912 Merge branch dev in master
        | |\  
        | * | 91f9d87 Hola desde rama master
        | * | b27cdea fix hecho en la rama fix-01
        | * | 1e12a67 nuevo commit rama master
        * | | c040330 New brach experimental
        | |/  
        |/|   
        * | 187e4dd Hola desde rama dev
        * | 0e16342 Update news
        |/  
        * a07178f (tag: v3.0) version 3.0
        *   3c6a550 Merge branch 'master' of https://github.com/lissettegar/formacion-git
        |\  
        | * 0e61dcc Create from github
        * | 8b9e417 Create file from-local.md
        |/  
        * b986b73 (tag: v2.0) version 2.0
        * aaae799 (tag: v1.0) version 1.0
        * 3a1666d commit hello.txt and cambios.txt

 A continuación indicaremos en que `versión/commit` sabemos que el código era correcto, esto hará que el bisect nos muestre información de un commit en medio del que hemos marcado como `bad` y el que hemos marcado como `good`:

        $ git bisect good v2.0
        Bisecting: 9 revisions left to test after this (roughly 3 steps)
        [91f9d8782952ec1f8aee3b2a12a2a22100a36bf9] Hola desde rama master

 Ahora comprobaremos si el codigo en el commit al que nos ha movido el `git bisect` es correcto o no:

        $ cat cambios.txt
        primera linea
        segunda linea

 En este commit el código es correcto así que lo marcaremos como `good` y el bisect nos moverá a otro commit entre este que hemos marcado como `good` y el que marcamos como `bad`:

        $ git bisect good
        Bisecting: 4 revisions left to test after this (roughly 2 steps)
        [3a4c264f64447122fbf4fd7b4c2d75871154b65a] Merge branch 'master' into dev

 Repetimos el proceso de comprobar el código y de marcarlo como `good` o `bad` segun sea el caso y al marcarlo el bisect nos moverá nuevamente de `commit`:

        $ cat cambios.txt
        primera linea
        segunda linea

        $ git bisect good
        Bisecting: 2 revisions left to test after this (roughly 1 step)
        [cb4cb70a70f7e1e65fc9951ea34fd35aba4de0e9] fix hecho en la rama fix-02

 En esté commit en el que estamos ahora encontramos que el código está mal (aparece la cadena "tercera linea"), así que lo marcaremos como `bad`:

        $ cat cambios.txt
        primera linea
        segunda linea
        tercera linea

        $ git bisect bad
        Bisecting: 0 revisions left to test after this (roughly 0 steps)
        [d14c2808c401909578a8760f4ed8afc4ecca578b] tercera linea en cambios.txt

 Al marcarle como `bad` el commit donde nos hemos encontrado el problema, y como solo queda un commit entre este que está `bad` y el ultimo que le indicamos que era `good`, ya no quedan mas steps que hacer así que el commit que está en medio es donde se introdujo el problema:

 **[d14c2808c401909578a8760f4ed8afc4ecca578b] tercera linea en cambios.txt**

Graficamente estos son los pasos que hemos hecho:

![alt bisect-1][bisect-1]

[bisect-1]: ../imagenes/bisect-1.png
