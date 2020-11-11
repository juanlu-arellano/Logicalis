### Desplegar aplicación de ejemplo

1. Acceder a la consola de openshift:

![alt dashboard][dashboard]

[dashboard]: ../imagenes/dashboard.png

2. Crear un proyecto con el nombre que se desee indicando un `GUID` para identificarlo:

![alt create-project][create-project]

[create-project]: ../imagenes/create-project.png

3. Cambiar a la vista Developer `Administrator -> Developer`:

![alt change-to-developer][change-to-developer]

[change-to-developer]: ../imagenes/change-to-developer.png

4. En la opción `Topology` pulsar en `From Calatog`:

![alt developer-catalog][developer-catalog]

[developer-catalog]: ../imagenes/developer-catalog.png

5. Buscar la imagen `Node.js` y seleccionarla:

![alt developer-nodejs][developer-nodejs]

[developer-nodejs]: ../imagenes/developer-nodejs.png

6. Click en `Create Aplication` y rellenar el formulario que aparece:

Git Repo URL: https://github.com/IBM/node-s2i-openshift.

Desplegar las opciones avanzadas y configurar `/site` para el `Context Dir`.

Poner a la aplicación el nombre que se desee en `Aplication Name`:

![alt nodejs-context][nodejs-context]

[nodejs-context]: ../imagenes/nodejs-context.png

7. Hacer Click en el boton `Create` en el final de la ventana para desplegar la Aplicación.

8. Cuando el despliegue haya terminado, encuentre el Routes y haga click en el enlace para abrir la aplicación:

![alt nodejs-success][nodejs-success]

[nodejs-success]: ../imagenes/nodejs-success.png

9. Puede acceder a la aplicación con las credenciales que desee, por ejemplo test/test:

![alt nodejs-login][nodejs-login]

[nodejs-login]: ../imagenes/nodejs-login.png

10. Habilitar limites de recursos a la aplicación. Para ello editaremos el Deployment que se ha creado añadiendo los recuros como se indican en la imagen:

![alt ocp-limits-yaml][ocp-limits-yaml]

[ocp-limits-yaml]: ../imagenes/ocp-limits-yaml.png

Cambiar:

    resources: {}
Por:

    resources:
      limits:
        cpu: 30m
        memory: 100Mi
      requests:
        cpu: 3m
        memory: 40Mi

Salvar y recargar.

11. Habilitar el autoscaler. Opción `Workloads > Horizontal Pod Autoscalers > Create Horizontal Pod Autoscaler`. Sustituir el template que aparece por el siguiente, sustituyendo el texto que aparece entre <>:

        apiVersion: autoscaling/v2beta1
        kind: HorizontalPodAutoscaler
        metadata:
          name: <NOMBRE>
          namespace: <PROYECTO>
        spec:
          scaleTargetRef:
            apiVersion: apps/v1
            kind: Deployment
            name: <NOMBRE DEPLOYMENT>
          minReplicas: 1
          maxReplicas: 5
          metrics:
            - type: Resource
              resource:
                name: cpu
                targetAverageUtilization: 1       

      Otra forma de crear el `hpa` es por linea de comandos, por ejemplo:

        oc project lgp-hpa
        oc autoscale deployment/lgp-patient --max=5 --cpu-percent=1
        horizontalpodautoscaler.autoscaling/lgp-patient autoscaled
        oc get hpa
        NAME          REFERENCE                TARGETS        MINPODS   MAXPODS   REPLICAS   AGE
        lgp-patient   Deployment/lgp-patient   <unknown>/1%   1         5         0          3s

12. Probar el autoscalado

 12.1. Simular la carga de la aplicación ejecutando el siguiente comando:

        while sleep 1; do curl -s <your_app_route>/info; done

 12.2. Observar como a los pocos segundos va aumentando la carga de CPU del pod donde corre la aplicación `Workload > Pod > Overview`.

 12.3. Comprobar también como al pasar unos 5 min aproximadamente aumentan el numero de pods que tiene el deployment y pasa de 1 a 5:

 ![alt ocp-hpa-before][ocp-hpa-before]

 [ocp-hpa-before]: ../imagenes/ocp-hpa-before.png

 12.4. Una vez que termine el autoscalado, parar la simulación de carga y comprobar nuevamente como disminuye progresivamente la carga de CPU de los Pods `Workload > Pod > Overview`.

 12.5. A los 5 minutos de haberse reducido la carga por debajo del límite indicado en el `hpa`, el número de pods decrecerá y volverá a ser lo que esté definido en el deployment.

13. Eliminar el hpa, por linea de comando o por la consola de OCP:

        oc delete hpa <NOMBRE>
