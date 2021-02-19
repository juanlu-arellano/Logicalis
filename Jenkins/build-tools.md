### Build Tools

En este laboratorio vamos a practicar el uso de Build Tools.

1. Ir a `Manage Jenkis -> Global Configuration` y ver que Tools ya aparecen disponibles. En este ejemplo vamos a usar **Ant**.

2. Buscar la Tool `Ant` y hacer click en `Add`. Crear un instalador que se llame `ant-default` y guardar.

3. Crear un job `freestyle` que se llame `ant-build`. En la sección `Source Code Management` seleccionar `Git` y configurar el siguiente repositorio:

       https://github.com/learn-devops-fast/rps-ant.git

3. En la sección `Build`, añadiremos un Step de tipo `Invoke Ant` usando la version de ant que creamos antes `ant-default`.

4. Añadir como `Target` las tareas que queremos ejecutar:

       clean compile test package war

5. Ejecutar un build y comprobar que termina correctamente. Ver tambien la salida de la consola.

6. A continuación configuraremos que se muestren los resultados de los Test de `JUnit`. Para ello añadimos un `Post-build action` de tipo, `Publish JUnit results report`.

7. El path donde se guardan los test es relativo al WS, asi que tenemos que configurar `Test report XMLs` como:

       target/test-reports/*.xml

8. Guardar y ejecutar un nuevo build. Comprobar que a diferencia de la primera ejecución en esta aparecen publicados los `Test Results` y que ahora en la ventana del Job a la derecha aparece un gráfico que se llama `Test Result Trend`
