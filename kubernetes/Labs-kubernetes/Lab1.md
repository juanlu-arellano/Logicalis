### Instrucciones Laboratorio 1 - Kubernetes - Pods, namespaces, variables de entorno

En este laboratorio practicaremos como crear y usar los recursos descritos.

1. Crear un namespace llamado 'mynamespace' y un pod con la imagen nginx llamado `nginx` en dicho namespace:

       $ kubectl create namespace mynamespace
       $ kubectl run nginx --image=nginx --restart=Never -n mynamespace

2. Crear otro pod de nginx pero de forma declarativa. Para generar el yaml usaremos la opción `--dry-run` y redirigiremos la salida a un fichero yaml:

       $ kubectl run nginx-1 --image=nginx --restart=Never --dry-run -o yaml > pod.yaml

       $ cat pod.yaml

       apiVersion: v1
       kind: Pod
       metadata:
         creationTimestamp: null
         labels:
           run: nginx
         name: nginx
       spec:
         containers:
         - image: nginx
           imagePullPolicy: IfNotPresent
           name: nginx
           resources: {}
         dnsPolicy: ClusterFirst
         restartPolicy: Never
       status: {}

       $ kubectl create -f pod.yaml -n mynamespace


3. Crear un pod busybox (usando el comando kubectl) que ejecute el comando `env` y observe la salida. Lo haremos de 2 maneras, la primera con la opcion `--it` para que nos muestre la consola del pod y veamos la ejecución del comando `env`, y la segunda sin la opción `--it`, en este caso para ver la ejecución del comando necesitamos ver los logs del pod:

       $ kubectl run busybox --image=busybox --command --restart=Never -n mynamespace -it -- env

       $ kubectl run busybox-1 --image=busybox --command --restart=Never -n mynamespace -- env

  Para ver los logs del pod:

       $ kubectl logs busybox-1 -n mynamespace

4. Obtener el YAML de un nuevo namespace llamado 'myns' y crearlo:

       $ kubectl create namespace myns -o yaml --dry-run > myns.yaml
       $ kubectl create -f myns.yaml

5. Ver los pods de todos los namespaces:

       $ kubectl get po --all-namespaces

6. Crear un pod de la imagen nginx llamado `nginx` y permitir tráfico en el puerto 80:

       $ kubectl run nginx --image=nginx --restart=Never --port=80 -n myns
       $ kubectl get pod -n myns
       NAME    READY   STATUS    RESTARTS   AGE
       nginx   1/1     Running   0          11s

7. Ejecutar los siguientes comandos para ver la configuracíon del pod `nginx`:

       $ kubectl get pod nginx -n myns -oyaml
       $ kubectl describe pod nginx -n myns

8. Al no indicar el tag de la imagen de nginx a partir de la cual queremos crear el pod, automaticamente se usa la imagen `latest`. Ahora vamos a cambiar la imagen del pod nginx a la nginx:1.7.1. Observe que el pod que estaba corriendo muere y arranca uno nuevo inmediatamente:

        $ kubectl get po nginx -o jsonpath='{.spec.containers[].image}{"\n"}' -n myns
        nginx
        $ kubectl set image pod/nginx nginx=nginx:1.7.1 -n myns
        pod/nginx image updated
        $ kubectl get po nginx -w -n myns
        NAME    READY   STATUS    RESTARTS   AGE
        nginx   1/1     Running   0          10m
        nginx   1/1     Running   1          11m
        $ kubectl describe po nginx -n myns
        $ kubectl get po nginx -o jsonpath='{.spec.containers[].image}{"\n"}' -n myns
        nginx:1.7.1

9. Probar las distintas formas de obtener un yaml de un pod que esta corriendo:

       $ kubectl get po nginx -n myns -o yaml
       $ kubectl get po nginx -n myns -oyaml
       $ kubectl get po nginx -n myns --output yaml
       $ kubectl get po nginx -n myns --output=yaml

10. Obtener información de un pod incluido detalles de eventos y problemas, utiles por ejemplo si un no arranca:

        $ kubectl describe po nginx -n myns

11. Obtener los logs de un pod:

        $ kubectl logs nginx -n myns

12. Ejecutar una shell en un pod:

        $ kubectl exec -it nginx -n myns -- /bin/sh

13. Crear un pod busybox que haga un echo 'hello world' y termine, se puede hacer de cualquiera de las 2 formas:

        $ kubectl run busybox --image=busybox -it --restart=Never -n myns -- echo 'hello world'
        $ kubectl run busybox-1 --image=busybox -it --restart=Never -n myns -- /bin/sh -c 'echo hello world'

14. Crear un pod nginx llamado `my-nginx` y configure una variable de entorno como 'var1=val1'. Comprobar que la variable esta definida correctamente dentro del pod:

 Crear el pod:

        $ kubectl run my-nginx --image=nginx --restart=Never --env=var1=val1 -n myns

 Comprobar la creación de la variable de entorno de las siguientes formas:

        $ kubectl exec -it my-nginx -n myns -- env
        $ kubectl exec -it my-nginx -n myns -- sh -c 'echo $var1'
        $ kubectl describe po my-nginx -n myns | grep val1

15. Borrar los proyectos creados, esto borrará todos los recursos que se crearan en el namespace:

        $ kubectl delete ns myns
        $ kubectl delete ns mynamespace
