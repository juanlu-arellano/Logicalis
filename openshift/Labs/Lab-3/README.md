# Instrucciones Laboratorio 3

## Desplegar aplicaciones desde la Consola de OCP

### Desplegar una aplicación en OpenShift desde la consola, utilizando un código fuente de un repo en GitHub

1. Entrar en la consola de OCP, vista Developer. En la opcion **+Add**, seleccionar la opcion **From Git** para ver el formulario **Import from git**.

![alt Crar App][imagen1]

[imagen1]: images/create-app1.png

2. En la seccion **Git** escribir la URL del repositorio Git donde esta el codigo fuente a partir del cual se desea crear una aplicación. En este ejemplo usaremos la URL de una aplicación nodejs:
https://github.com/sclorg/nodejs-ex.

3. En la sección **Builder**, seleccionar el Builder Image que se va a usar, en este caso **Node.js**, lo selecciona por defecto. En este caso usaremos la version **latest**.

4. En la sección **General** elegir un nombre para la aplicacion. Se puede usar el que viene por defecto.

5. En la sección **Recursos** podemos elegir si queremos desplegar un deployment o un deploymentconfig. Dejar la opción por defecto.

6. En la sección **Advanced Options**, la opción **Create a route to the application** está seleccionada de forma predeterminada para que se cree el router y poder acceder a la aplicacion.

7. Clic en **Crear** para crear la aplicación y ver su estado de compilación en la vista **Topología**.

![alt Crar App][imagen2]

[imagen2]: images/create-app2.png

8. Comprobar en la opcion **Build** del menú de la izquierda, que se ha creado un BuildConfig para la aplicacion y que seleccionando el **Buildconfig** podemos ver los Buids desplegados (en este caso solo estara uno)

![alt Crar App][imagen3]

[imagen3]: images/create-app3.png

![alt Crar App][imagen4]

[imagen4]: images/create-app4.png

9. Volvemos a la opción **Topología**, y accedemos a la aplicacion pulsando en la flechita que aparece en el borde superior derecho del despliegue.

![alt Crar App][imagen8]

[imagen8]: images/create-app8.png

## Desplegar una aplicación en OpenShift desde la consola usando el catalogo

1. En la opcion **+Add**, seleccionar la opcion **From Catalog** para ver el formulario **Developer Catalog** y filtrar por **Jenkins**.

![alt Crar App][imagen5]

[imagen5]: images/create-app5.png

2. Seleccionar el **Jenkins Ephemeral** y hacer clic en **Instantiate Template**

3. Dejar todos los valores por defecto y observar que indica todos los recursos que se crearan.

![alt Crar App][imagen6]

[imagen6]: images/create-app6.png

4. Clic en **Create** e ir a la vista **Topologia** para ver la app desplegada

![alt Crar App][imagen7]

[imagen7]: images/create-app7.png

5. Desde la vista **Topologia**, acceder a la aplicacion

![alt Crar App][imagen9]

[imagen9]: images/create-app9.png

6. Limpiar el entorno

```shell
$ oc delete project $GUID-formacion
```


