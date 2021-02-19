### Seguridad

1. Ir a `Manage Jenkins->Configure Global Security`. En la sección `Authorization`, seleccione estrategia `Project-based Matrix Authorization Strategy `.

2. En la columna `Overall` para usuarios autenticados, seleccione el cuadro `Read`. Guardar.

3. Ir a  `Manage Jenkins->Manage Users`. Hacer click en `Create User` y crear los siguientes usuarios:

       Nombre de usuario: tom
       Contraseña: defina una contraseña que recordará fácilmente, ya que tendremos que ingresarla más adelante en el laboratorio
       Nombre completo: tom
       Dirección de correo electrónico: una dirección de correo electrónico falsa (por ejemplo, "tom@co.com")

   Haga clic en Crear usuario.

       Nombre de usuario: lisa
       Contraseña: defina una contraseña que recordará fácilmente, ya que tendremos que ingresarla más adelante en el laboratorio
       Nombre completo: lisa
       Dirección de correo electrónico: una dirección de correo electrónico falsa (por ejemplo, "lisa@co.com")

   Haga clic en Crear usuario.

       Nombre de usuario: diane
       Contraseña: defina una contraseña que recordará fácilmente, ya que tendremos que ingresarla más adelante en el laboratorio
       Nombre completo: diane
       Dirección de correo electrónico: una dirección de correo electrónico falsa (por ejemplo, "diane@co.com")

   Haga clic en Crear usuario.

4. Cree una carpeta llamada `frontend`. En la configuración de la carpeta, sección `Propiedades`, seleccione `Enable project-based security`.

5. Haga clic en `Add user or group`. Añadir al usuario `tom` y una vez añadido otorgarle todos los permisos.

6. Haga clic en `Add user or group`. Añadir al usuario `diane` y una vez añadido otorgarle permisos de `lectura`.

7. Guardar la configuración de la carpeta y crear un Job de tipo `Freestyle` con el nombre que querais. Guardar el Job con los valores predeterminados.

8. Cree una carpeta llamada `backend` En la configuración de la carpeta, sección `Propiedades`, seleccione `Enable project-based security`.

9. Haga clic en `Add user or group`. Añadir al usuario `lisa` y una vez añadido otorgarle todos los permisos.

10. Haga clic en `Add user or group`. Añadir al usuario `diane` y una vez añadido otorgarle permisos de `lectura`.

11. Guardar la configuración de la carpeta y crear un Job de tipo `Freestyle` con el nombre que querais. Guardar el Job con los valores predeterminados.

12. Cerrar sesión en Jenkins.

13. Iniciar sesión como `diane` y verificar que vea las carpetas `frontend` y `backend`.

14. Haz clic en la carpeta de `backend`. Deberíamos ver el trabajo y su estado, pero no tenemos permisos para ejecutarlo.

15. Haz clic en la carpeta `frontend`. Deberíamos ver el trabajo y su estado, pero no tenemos permisos para ejecutarlo.

16. Cerrar sesión como `diane`.

17. Iniciar sesión como `tom` y verificar que solo ve la carpeta `frontend`.

18. Haz clic en la carpeta `frontend`. Veremos que tenemos más permisos, incluido crear items y ejecutar builds.

19. Cerrar sesión como `tom`.

20. Iniciar sesión como `lisa` y verificar que solo ve la carpeta `backend`.

21. Haz clic en la carpeta `backend`. Veremos que tenemos más permisos, incluido crear items y ejecutar builds.
