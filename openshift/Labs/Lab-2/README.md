# Instrucciones Laboratorio 2

## Desplegar aplicaciones desde linea de comando y uso de configmaps, variables de entorno, secrets y almacenamiento persistente estático

Requisitos:

* Logarse en el cluster con el comando "oc login ..."

* Definir la variable GUID:

      $ export GUID=<iniciales>

### 1. Uso de Variables de entorno

  1.1. Crear el proyecto y desplegar una aplicacion desde un repositorio de Git:

    $ oc new-project $GUID-env
    $ oc new-app https://github.com/redhat-gpte-devopsautomation/PrintEnv --as-deployment-config=true

  1.2. Ver los logs del BuildConfig:

    $ oc logs -f bc/printenv

  1.3. Crear un route para la app:

    $ oc expose svc printenv

  1.4 Ver los recursos del projecto. Comprobar como se han creado los objetos deployment config, pods, secrets, etc:

    $ oc get all

  1.5. Comprobar que variables de entorno estan definidas en el pod en ese momento:

    $ oc exec <printenv-X-XXX> printenv |grep APP

  1.6. Configurar dos variables de entorno a la aplicacion:

    $ oc set env dc/printenv APP_VAR_1=Value1 APP_VAR_2=Value2

  1.7. Esperar a que la aplicacion redespliegue:

    $ watch oc get pod

  1.8. Cuando el pod este corriendo pulsar Ctrl+C para salir del watch y continuar.

  1.9. Comprobar nuevamenete las variables de entorno definidas dentro del pod:

    $ oc exec <printenv-X-XXX> printenv |grep APP
    APP_VAR_1=Value1
    APP_VAR_2=Value2
    APP_ROOT=/opt/app-root

  1.10. Borrar la segunda variable de entorno y volver a comprobarlas con el "oc exec":

    $ oc set env dc/printenv APP_VAR_2-

  1.9. Comprobar que la variable ya no esta definida:

    $ oc exec <printenv-X-XXX> printenv |grep APP
    APP_VAR_1=Value1
    APP_ROOT=/opt/app-root


### 2. Uso de ConfigMaps

  2.1. Crear un ConfigMap con 2 variables de entorno:

    $ oc create configmap printenv-config \
     --from-literal=APP_VAR_3=Value3 \
     --from-literal=APP_VAR_4=Value4

  2.2. Actualizar el deployment de la aplicacion para añadir las variables definidas en el configmap:

    $ oc edit dc printenv

      spec:
        containers:
        - env:
          - name: APP_VAR_1
            value: Value1
          - name: APP_VAR_3
            valueFrom:
              configMapKeyRef:
                name: printenv-config
                key: APP_VAR_3
          - name: APP_VAR_4
            valueFrom:
              configMapKeyRef:
                name: printenv-config
                key: APP_VAR_4

  2.3. Para salir del edit hay que guardar la configuracion ":wq!"

  2.4. Esperar a que la aplicacion redespliegue:

    $ watch oc get pod

  2.5. Cuando el pod este corriendo pulsar Ctrl+C para salir del watch y continuar.

  2.6. Comprobar nuevamenete las variables de entorno definidas dentro del pod:

    $ oc exec <printenv-X-XXX> printenv |grep APP
    APP_VAR_4=Value4
    APP_VAR_1=Value1
    APP_VAR_3=Value3
    APP_ROOT=/opt/app-root

  2.7. Definir una variable de entorno que lee el contenido de un fichero:

    $ oc set env dc/printenv READ_FROM_FILE=/data/configfile.txt

  2.8. Crear el fichero de configuracion:

    $ echo "This is a very important Config File" > configfile.txt

  2.9. Crear un ConfigMapp:

    $ oc create configmap printenv-config-file --from-file=configfile.txt

  2.10. Añadir el ConfigMap a la configuracion del deployment:

    $ oc set volume dc/printenv --add --overwrite --name=config-volume -m /data/ -t configmap --configmap-name=printenv-config-file

  2.11. Encontrar el pod que este Running:

    $ oc get pods
    NAME                READY   STATUS      RESTARTS   AGE
    printenv-1-build    0/1     Completed   0          41m
    printenv-1-deploy   0/1     Completed   0          38m
    printenv-2-deploy   0/1     Completed   0          7m31s
    printenv-3-deploy   0/1     Completed   0          4m44s
    printenv-4-deploy   0/1     Completed   0          97s
    printenv-6-deploy   0/1     Completed   0          20s
    printenv-6-n8zj6    1/1     Running     0          9s

  2.12. Abrir una shell en el pod (en este ejemplo es el printenv-6-n8zj6) y comprobar el contenido del fichero:

    $ oc rsh printenv-6-n8zj6

    sh-4.2$ ls /data/configfile.txt
    /data/configfile.txt

    sh-4.2$ cat /data/configfile.txt
    This is a very important Config File

## 3. Uso de secrets para configurar variables de entorno

  3.1. Crear un secret desde un fichero:

    $ echo 'r3dh4t1!' > ./password.txt
    $ echo 'admin' > ./user.txt
    $ oc create secret generic printenv-secret --from-file=app_user=user.txt --from-file=app_password=password.txt
    $ oc describe secrets printenv-secret

  3.2. Comprobar el secret:

    $ oc get secret printenv-secret -o yaml

    apiVersion: v1
    data:
      app_password: cjNkaDR0MSEK
      app_user: YWRtaW4K
    kind: Secret
    [...]

  3.3. Decodificar el UserID y el pass usando *base64 --decode*:

    $ echo "cjNkaDR0MSEK" | base64 --decode
    $ echo "YWRtaW4K" | base64 --decode  

  3.4. Añadir el secret a la app *printenv*:

    $ oc set env dc/printenv --from=secret/printenv-secret

  3.5. Verificar que el valor de las variables es el correcto:

    $ oc set env dc/printenv --list

    # deploymentconfigs/printenv, container printenv
    APP_VAR_1=Value1
    # APP_VAR_3 from configmap printenv-config, key APP_VAR_3
    # APP_VAR_4 from configmap printenv-config, key APP_VAR_4
    READ_FROM_FILE=/data/configfile.txt
    # APP_USER from secret printenv-secret, key app_user
    # APP_PASSWORD from secret printenv-secret, key app_password      

  3.6. Configurar las mismas variables de entorno para el MySQL añadiendo el prefijo *MYSQL_*:

    $ oc set env dc/printenv --from=secret/printenv-secret --prefix=MYSQL_
    $ oc set env dc/printenv --list

    # deploymentconfigs/printenv, container printenv
    APP_VAR_1=Value1
    # APP_VAR_3 from configmap printenv-config, key APP_VAR_3
    # APP_VAR_4 from configmap printenv-config, key APP_VAR_4
    READ_FROM_FILE=/data/configfile.txt
    # APP_USER from secret printenv-secret, key app_user
    # APP_PASSWORD from secret printenv-secret, key app_password
    # MYSQL_APP_PASSWORD from secret printenv-secret, key app_password
    # MYSQL_APP_USER from secret printenv-secret, key app_user

## 4. Uso de almacenamiento persistente estático

### Este paso lo habremos hecho con los prerequisitos:

  4.1. Exportar un FS por NFS. A continuación están los pasos para crear el servidor NFS y como exportar el FS. En nuestro caso ya teneis creado un FS con vuestro nombre en /home/nfs en el nodo bastión que contiene un fichero index.yaml y ese el FS que teneis que usar:

    yum install -y nfs-utils rpcbind
    systemctl enable rpcbind
    systemctl enable nfs-lock
    systemctl enable nfs-idmap
    systemctl enable nfs-server

    systemctl start rpcbind
    systemctl start nfs-server
    systemctl start nfs-lock
    systemctl start nfs-idmap

    mkdir -p /nfs-dir
    chmod 777 -R /nfs-dir

    echo '/nfs-dir *(rw,sync,no_wdelay,no_root_squash,insecure)' >> /etc/exports
    exportfs -r

  4.2. Crear un PV que haga uso del FS compartido por NFS(Sustituir el $GUID por vuestro nombre o GUID):

    $ vi pv-volume.yaml
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: $GUID-pv-volume
    spec:
      storageClassName: ""
      capacity:
        storage: 1Gi
      accessModes:
      - ReadWriteOnce
      nfs:
        path: <vuestro FS exportado por NFS>
        server: <IP-sevidor-NFS>
      persistentVolumeReclaimPolicy: Retain

  4.3. Comprobar que se ha creado el PV

      $ oc get pv

  4.4. Crear un nuevo proyecto y añadir privilegios de anyuid a la service account **default**:

      $ oc new-project $GUID-persistent
      $ oc adm policy add-scc-to-user anyuid system:serviceaccount:$GUID-env:default

  4.5. Crear el PVC (Cambiar el GUID y poner vuestro nombre de proyecto):

       apiVersion: v1
       kind: PersistentVolumeClaim
       metadata:
         name: <$GUID-pvc>
         namespace: <$GUID-persistent>
       spec:
         storageClassName: ""
         volumeName: <vuestro nombre de pv>
         accessModes:
         - ReadWriteMany
         resources:
           requests:
             storage: 1Gi  


  4.6. Comprobar que se ha creado el PVC

       $ oc get pvc -n $GUID-persistent

  4.7. ¿Cual es el estado del PVC? ¿Si hay algún problema como podemos corregirlo?

  4.8. Una vez corregido el problema y el pvc este en status Bound, desplegar un pod nginx y configurarle como volumen el pvc que hemos creado y que lo monte en /usr/share/nginx/html.

       $ oc create deploy nginx --image=nginx
       $ oc set volume deployment/nginx --add --name=myvol --mount-path=/usr/share/nginx/html -t pvc --claim-name=<nombre-pvc>

  4.9. Comprobar que el pod de nginx arranca correctamente y que tiene montado el PVC

       $ oc get pod
       $ oc describe pod nginx-xxx

  4.10. Crear un servicio para el pod y un route para acceder desde el exterior

       $ oc expose deploy nginx --port=80
       $ oc expose svc nginx

  4.11. Comprobar que se tiene acceso a la aplicación

       $ curl <route>

  4.12. Eliminar el volumen del deployment de nginx que añadimos en el punto 4.8, borrar tambien el pvc y el pv:

       $ oc set volume deployment/nginx --remove --name=myvol
       $ oc delete pvc <nombre-pvc>
       $ oc delete pv <nombre-pv>

  4.13. Crear un nuevo PVC dinamicamente (modificar el $GUID de la definición del yaml):

       $ vi pvc-dinamic.yaml
       kind: PersistentVolumeClaim
       apiVersion: v1
       metadata:
         name: test-claim
         namespace: $GUID-persistent
         annotations:
           volume.beta.kubernetes.io/storage-class: "managed-nfs-storage"
       spec:
         accessModes:
           - ReadWriteMany
         resources:
           requests:
             storage: 1Mi
       
       $ oc create -f pvc-dinamic.yaml

  4.14. Comprobar que ocurre una vez que se crea el nuevo PVC:

       $ oc get pvc
       $ oc get pv

  4.15. ¿Que ocurre al crear el PVC a partir de una StorageClass?

  4.16. Añadir el nuevo PVC al deployment de nginx.


