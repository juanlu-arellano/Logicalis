### Usando el plugin Gitlab Branch Source Plugin

1. Vamos a crear ahora otro Multibranch Pipeline pero esta vez vamos a usar como `Source Code` un Proyecto de Gitlab. Para ello lo primero es instalar el plugin `Gitlab Branch Source Plugin`.

2. Una vez instalado el Plugin hay que configurar un `Gitlab Server`. Ir a `Manage Jenkins -> Configure System` a la sección `GitLab Server`. Configurar nuestro GitLab server, `nombre: gitlab.com`, URL: https://gitlab.com. Añadir una credencial de tipo `API Token` con un token que obtenemos de nuestra cuenta de gitalb.com. Testear la conexión y guardar.

3. Ir a gitlab.com y hacer un fork del repositorio https://gitlab.com/lissettegar/some-code. Desvincular vuestro repositorio del mio en `Settings->General->Advanced->Remove fork relationship`.

4. Nuestro Jenkinsfile en el primer stage hace un Checkout del repositorio `some-code`. Desde la rama develop editar el Jenkinsfile para que apunte a vuestro repositorio en lugar de al mio.

5. Hacer un commit del cambio, moverse a la rama master y hacer un merge de la rama develop.

6. Ahora desde Jenkins crear otro `Multibranch Pipeline` llamado por ejemplo `gitlab-branched` pero esta vez usaremos como Source Code `Gitlab Project`.

7. Configurar como server el `GitLab Server` que creamos en el punto 2 `gitlab.com`.

8. Como es un repositorio público no es necesario definir credenciales.

9. Configurar como `Owner` vuestro usuario y apareceran todos los proyectos que tengáis incluido el proyecto `some-code` en la lista de los proyectos a elegir. Seleccionarlo.

10. Dejar todo los valores por defecto, guardar el job y comprobar que se lanza el `Scan Multibranch Pipeline` y que se ejecutan los builds para ambas ramas.

11. Desde gitlab.com en la rama `develop` añadir algun cambio en el archivo `fichero` y crear un commit.

12. Hacer un nuevo `Scan Multibranch Pipeline` y comprobar que solamente se ejecuta el build de la rama `develop`
