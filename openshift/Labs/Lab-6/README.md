# Instrucciones Laboratorio 6

## Visualizando Logs

1. Desplegar la aplicacion printenv del lab-2. Seguir los pasos del Lab-2 hasta el punto 1.4.
2. Comprobar los logs de la aplicacion por linea de comandos:

        $ oc logs printenv-1-8px6q
        git version 1.8.3.1
        Environment:
        DEV_MODE=false
        NODE_ENV=production
        DEBUG_PORT=5858
        Running as user uid=1002800000(1002800000) gid=0(root) groups=0(root),1002800000
        Launching via npm...
        npm info it worked if it ends with ok
        npm info using npm@6.9.0
        npm info using node@v10.16.3
        npm info lifecycle @~prestart: @
        npm info lifecycle @~start: @

        > @ start /opt/app-root/src
        > node app.js

        Node Backend is listening at 8080

3. Comprobar los eventos del proyecto:

        $ oc get events
        LAST SEEN   TYPE     REASON              OBJECT                             MESSAGE
        12m         Normal   Scheduled           pod/printenv-1-8px6q               Successfully assigned lgp-env/printenv-1-8px6q to worker2dev.ocpdevmad01.tic1.intranet
        12m         Normal   Pulling             pod/printenv-1-8px6q               Pulling image "image-registry.openshift-image-registry.svc:5000/lgp-env/printenv@sha256:ea4ece6fd6a8573674512c8251883bd9dddc8bbd4a23366b6e3e14671d57161b"
        12m         Normal   Pulled              pod/printenv-1-8px6q               Successfully pulled image "image-registry.openshift-image-registry.svc:5000/lgp-env/printenv@sha256:ea4ece6fd6a8573674512c8251883bd9dddc8bbd4a23366b6e3e14671d57161b"
        12m         Normal   Created             pod/printenv-1-8px6q               Created container printenv
        12m         Normal   Started             pod/printenv-1-8px6q               Started container printenv
        13m         Normal   Scheduled           pod/printenv-1-build               Successfully assigned lgp-env/printenv-1-build to worker2dev.ocpdevmad01.tic1.intranet
        13m         Normal   Pulled              pod/printenv-1-build               Container image "quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:a16c3892ffd8ebc28677fec63db9311adfeb99693b99e10fef16573e861f6bbb" already present on machine
        13m         Normal   Created             pod/printenv-1-build               Created container git-clone
        13m         Normal   Started             pod/printenv-1-build               Started container git-clone
        13m         Normal   Pulled              pod/printenv-1-build               Container image "quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:a16c3892ffd8ebc28677fec63db9311adfeb99693b99e10fef16573e861f6bbb" already present on machine
        13m         Normal   Created             pod/printenv-1-build               Created container manage-dockerfile
        13m         Normal   Started             pod/printenv-1-build               Started container manage-dockerfile
        13m         Normal   Pulled              pod/printenv-1-build               Container image "quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:a16c3892ffd8ebc28677fec63db9311adfeb99693b99e10fef16573e861f6bbb" already present on machine
        13m         Normal   Created             pod/printenv-1-build               Created container sti-build
        13m         Normal   Started             pod/printenv-1-build               Started container sti-build
        12m         Normal   Scheduled           pod/printenv-1-deploy              Successfully assigned lgp-env/printenv-1-deploy to worker3dev.ocpdevmad01.tic1.intranet
        12m         Normal   Pulled              pod/printenv-1-deploy              Container image "quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:9ac5a8dceed67e3c3e1c018dc581bf5f03d77a20a2f1ca1bf00c32b5e75b19f6" already present on machine
        12m         Normal   Created             pod/printenv-1-deploy              Created container deployment
        12m         Normal   Started             pod/printenv-1-deploy              Started container deployment
        13m         Normal   BuildStarted        build/printenv-1                   Build lgp-env/printenv-1 is now running
        12m         Normal   BuildCompleted      build/printenv-1                   Build lgp-env/printenv-1 completed successfully
        12m         Normal   SuccessfulCreate    replicationcontroller/printenv-1   Created pod: printenv-1-8px6q
        12m         Normal   DeploymentCreated   deploymentconfig/printenv          Created new replication controller "printenv-1" for version 1

3. Crear un filtro en Kibana para ver los logs de la aplicacion printenv:

![alt Kibana][imagen9]

[imagen9]: images/kibana.png

4. Comprobar que seleccionando ese filtro unicamente aparecen los logs relacionados con el namespace por el que se esta filtrando.

5. Hacer una busqueda por algun mensaje que salga en los logs del pod, por ejemplo "DEBUG_PORT=5858" o cualquier otro.

## Monitorizacion con Prometheus

1. Acceder a Prometheus. En la consola de OpenShift opcion **Monitoring** -> **Metrics** -> **Prometheus UI**

![alt Prometheus][imagen1]

[imagen1]: images/lab-prometheus1.png

2. Una vez en la consola de Prometheus seleccionar **Status** -> **Targets**

3. Confirmar que todos los Targets estan en estado **UP**

![alt Prometheus][imagen2]

[imagen2]: images/lab-prometheus2.png

4. Hacer clic en **Unhealthy** para comprobar que la pagina esta vacia. Esto indica que todo esta bien en el cluster.

5. Seleccionar la Opcion **Graph** para probar varias queries de Prometheus.

6. En la entrada **Expresion** escribir la siguiente lo siguiente y pulsar enter:

        kubelet_running_pod_count

7. Espere ver el numero de pods corriendo en cada nodo:

![alt Prometheus][imagen3]

[imagen3]: images/lab-prometheus3.png

8. Probar mas queries:

* node_memory_MemTotal_bytes / 1024 /1024
* node_memory_MemFree_bytes /1024 /1024
* round(node_memory_MemFree_bytes/1024/1024)

9. Borrar la Expresion y empezar a escribir **node**. Ver la lista de expresiones que se despliega y experimente con otras queries de nodo.

10. Explore con otras queries viendo la salida en modo grafico, en la pestaña **Graph** en lugar de **Condole**:

* namespace_pod_container:container_cpu_usage_seconds_total:sum_rate

![alt Prometheus][imagen4]

[imagen4]: images/lab-prometheus4.png

11. Todas las queries se pueden hacer tambien desde la consola de OpenShift **Monitoring** -> **Metrics**. Explorar por ejemplo **node_filesystem_size_bytes**:

![alt Prometheus][imagen8]

[imagen8]: images/lab-prometheus5.png

## Dashboards de Grafana

1. Acceder a Grafana. En la consola de OpenShift opcion **Monitoring** -> **Dashboards**

![alt Grafana][imagen5]

[imagen5]: images/lab-grafana1.png

2. Buscar la grafica **Kubernetes / Compute Resources / Cluster**. Esta grafica muestra un overview del cluster incluida la utilización de cpu, memoria:

![alt Grafana][imagen6]

[imagen6]: images/lab-grafana2.png

3. Explore los gráficos haciendo lo siguiente:

* Encuentra diferentes proyectos.
* Ver los gráficos para un solo proyecto.
* Cambiando los rangos de tiempo (parte superior derecha de la ventana)

4. Explorar los graficos de utilización de los nodos **USE Method / Node**:

![alt Grafana][imagen7]

[imagen7]: images/lab-grafana3.png
