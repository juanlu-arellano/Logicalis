### Instrucciones Laboratorio 15

### gitk y git gui

En este Laboratorio vamos a crear un repositorio para practicar con las interfaces gráficas `gitk` y `git gui`.

1. Vamos a crear un nuevo repositorio que tendrá 2 ramas:

       $ mkdir gitkdemo
       $ cd gitkdemo
       $ git init
       Initialized empty Git repository in /home/lgarciap/Documents/MAYORAL/FORMACION/gitkdemo/.git/
       $ echo "hello world" > index.txt
       $ git add index.txt
       $ git commit -m "added index.txt with hello world content"
       [master (root-commit) 0d097dc] added index.txt with hello world content
       1 file changed, 1 insertion(+)
       create mode 100644 index.txt

2. A continuación ejecutaremos el `gitk` que nos abrirá una ventana como esta:

       $ gitk&

 ![alt gitk-1][gitk-1]

 [gitk-1]: ../imagenes/gitk-1.png

3. Vamos a añadir un nuevo commit y ver como aparece en el gitk como un punto rojo, que significa que es un cambio que aun no se ha añadido al area de stage:

       $ echo "added content to index" >> index.txt
       $ git status
       On branch master
       Changes not staged for commit:
         (use "git add <file>..." to update what will be committed)
         (use "git checkout -- <file>..." to discard changes in working directory)

       	modified:   index.txt

       no changes added to commit (use "git add" and/or "git commit -a")

 ![alt gitk-2][gitk-2]

 [gitk-2]: ../imagenes/gitk-2.png

4. Si a continuación haremos un commit para este cambio pero esta vez usando la herramienta `git gui`. Lo arrancamos desde linea de comandos desde el directorio del repositorio:

       $ git gui&

 Nos aparecerá una ventana como esta:

  ![alt gitk-3][gitk-3]

  [gitk-3]: ../imagenes/gitk-3.png

5. Para hacer el `commit` primero tenemos que mover el fichero al area de `Stage` (parte inferior izquierda). Para ello haremos click en el icono del fichero `index.txt` y esto hará que el fichero se mueva de area, sería el equivalente a un `git add index.txt`, quedando así:

 ![alt gitk-4][gitk-4]

 [gitk-4]: ../imagenes/gitk-4.png

6. Ahora para hacer el `commit` añadiremos un `Commit Message` y pulsaremos el botón `Commit`:

 ![alt gitk-5][gitk-5]

 [gitk-5]: ../imagenes/gitk-5.png

7. Si volvemos al `gitk` veremos que la rama `master` y el `HEAD` se han movido al nuevo `commit`:

 ![alt gitk-6][gitk-6]

 [gitk-6]: ../imagenes/gitk-6.png

8.  Ahora vamos a comparar ambos commits. Para ello vamos a seleccionar uno de ellos en el gitk, por ejemplo el segundo y con el boton derecho del raton desplegamos un menu de contexto y seleccionamos la opción `Mark this commit`. A continuación pulsando el boton derecho del ratón sobre el otro commit (el primero), seleccionamos una de las opciones de `Diff` que aparecen. En la parte inferior derecha saldrá algo como esto:

 ![alt gitk-7][gitk-7]

 [gitk-7]: ../imagenes/gitk-7.png

9. En este punto vamos a crear una nueva rama desde el `git gui`. Para ello usaremos la opción `Branch -> Create` o `Ctrl+N`:

 ![alt gitk-8][gitk-8]

 [gitk-8]: ../imagenes/gitk-8.png

10. Comprobar como se ve la nueva rama en `gitk`:

 ![alt gitk-9][gitk-9]

 [gitk-9]: ../imagenes/gitk-9.png

11. A continuación vamos a añadir un nuevo fichero y creamos dos nuevos commit:

        $ echo "new branch content" > new_branch_file.txt
        $ git add new_branch_file.txt
        $ git commit -m "new branch commit with new file and added content"
        [new-branch 94a0c2f] new branch commit with new file and added content
        1 file changed, 1 insertion(+)
        create mode 100644 new_branch_file.txt

        $ echo "new branch index update" >> index.txt
        $ git commit -am "new branch commit to index.txt with new content"
        [new-branch 2f920f3] new branch commit to index.txt with new content
        1 file changed, 1 insertion(+)

12. Refrescamos el `gitk` con `F5` y vemos como se ven los nuevos commits de la nueva rama:

 ![alt gitk-10][gitk-10]

 [gitk-10]: ../imagenes/gitk-10.png

13. Si quisieramos ver unicamente las commits que están en una rama y no estan en otra podriamos arrancar gitk con la siguiente opción:

        $ gitk master..new-branch&

  ![alt gitk-11][gitk-11]

  [gitk-11]: ../imagenes/gitk-11.png     

14. Repetir los comandos del **Laboratorio 13 - punto 8**, pero en lugar de usar `git log` usar `gitk`. Usando el repo **formacion-git**.

        $ cd formacion-git

   Ejecutar los comandos desde aquí. Quitar la opción --oneline a la hora de ejecutar los comandos, por ejemplo donde pone:

        $ git log master...dev --oneline

   debeis ejecutar:

        $ gitk master...dev

15. A continuación, vamos a añadir un nuevo fichero al repo **formacion-git**. Nos aseguramos de que estamos en la rama `dev` y si no estais cambiaros a ella:

        $ git branch
        * dev
        fix-02
        master
        new-feature

 Creamos un nuevo fichero en el repo:

        $ echo "Testing push from git gui" >> test-gitgui.txt

 Lanzamos el `git gui`:

        $ git gui&

 Abrirá con el siguiente contenido:

  ![alt gitk-12][gitk-12]

  [gitk-12]: ../imagenes/gitk-12.png  

16. Pulsando en el icono del fichero `test-gitgui.txt` haremos lo equivalente a un `git add` y el fichero pasará al area de `stage`:

 ![alt gitk-13][gitk-13]

 [gitk-13]: ../imagenes/gitk-13.png  

17. En este punto crearemos un nuevo `commit` para este cambio, escribiendo un `Commit Message` y pulsando el botón `Commit`. Esto sería lo equi
  al comando `git commit -m "mensaje"`:

 ![alt gitk-14][gitk-14]

 [gitk-14]: ../imagenes/gitk-14.png  

18. Una vez hecho el commit, abriremos el `gitk` para comprobar que aparece este nuevo cambio en el histórico:

        $ gitk&

 ![alt gitk-15][gitk-15]

 [gitk-15]: ../imagenes/gitk-15.png         

19. Ahora desde el `git gui` haremos un `Push`. Para ello pulsaremos en el botón `Push`, y en la ventana que aparece seleccionamos la rama `dev` y el repositorio remoto `origin` y pulsamos en `Push`:

 ![alt gitk-16][gitk-16]

 [gitk-16]: ../imagenes/gitk-16.png     

20. Nos pedirá las credenciales y una vez terminado el push aparece una ventana como la siguiente:

 ![alt gitk-17][gitk-17]

 [gitk-17]: ../imagenes/gitk-17.png

21. Por último volvemos al `gitk` y comprobamos que la `rama remota dev` está actualizada al último `commit`:

 ![alt gitk-18][gitk-18]

 [gitk-18]: ../imagenes/gitk-18.png
