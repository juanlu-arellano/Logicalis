### Instrucciones Laboratorio 2 - Kubernetes - Labels y anotaciones

En este laboratorio practicaremos como crear y usar etiquetas y anotaciones.

1. Crear un namespace llamado `my-nginx` y 3 pods con los nombres `nginx1,nginx2,nginx3`. Todos ellos con la etiqueta `label app=v1`

       $ kubectl create ns my-nginx
       $ kubectl run nginx1 --image=nginx --restart=Never --labels=app=v1 -nmy-nginx
       $ kubectl run nginx2 --image=nginx --restart=Never --labels=app=v1 -nmy-nginx
       $ kubectl run nginx3 --image=nginx --restart=Never --labels=app=v1 -nmy-nginx

2. Mostrar todas las etiquetas de todos los pods:

       $ kubectl get po --show-labels -nmy-nginx
       NAME     READY   STATUS    RESTARTS   AGE   LABELS
       nginx1   1/1     Running   0          42s   app=v1
       nginx2   1/1     Running   0          36s   app=v1
       nginx3   1/1     Running   0          32s   app=v1

3. Cambiar el label del pod `nginx2` a `app=v2`

       $ kubectl label po nginx2 app=v2 --overwrite -nmy-nginx

4. Mostrar las etiquetas `app` de todos los pods:

       $ kubectl get po -L app -nmy-nginx
       NAME     READY   STATUS    RESTARTS   AGE     APP
       nginx1   1/1     Running   0          3m52s   v1
       nginx2   1/1     Running   0          3m46s   v2
       nginx3   1/1     Running   0          3m42s   v1

5. Mostrar solo los pods que tengan la etiqueta `app=v2` (debe mostrarse solo el pod `nginx2`):

       $ kubectl get po -l app=v2 -nmy-nginx
       NAME     READY   STATUS    RESTARTS   AGE
       nginx2   1/1     Running   0          5m20s

6. Borrar la etiqueta `app` de todos los pods de nginx y comprobar que se borran:

       $ kubectl label po nginx1 nginx2 nginx3 -nmy-nginx app-
       pod/nginx1 labeled
       pod/nginx2 labeled
       pod/nginx3 labeled

       $ kubectl get po --label-columns=app -nmy-nginx
       NAME     READY   STATUS    RESTARTS   AGE     APP
       nginx1   1/1     Running   0          8m      
       nginx2   1/1     Running   0          7m54s   
       nginx3   1/1     Running   0          7m50s  

7. Añadir anotaciones a los pods `nginx1, nginx2, nginx3` con el valor `description='my description'`:

       $ kubectl annotate po nginx1 nginx2 nginx3 description='my description' -nmy-nginx
       pod/nginx1 annotated
       pod/nginx2 annotated
       pod/nginx3 annotated

8. Comprobar las anotaciones de uno de los pods nginx:

       $ kubectl describe po nginx1 -nmy-nginx | grep -i 'annotations'

9. Borrar las anotaciones de los 3 pods y comprobar que se han borrado:

       $ kubectl annotate po nginx{1..3} description- -nmy-nginx
       pod/nginx1 annotated
       pod/nginx2 annotated
       pod/nginx3 annotated
       lgarciap@lgarciap-ubuntu:~$ kubectl describe po nginx1 -nmy-nginx | grep -i 'annotations'
       Annotations:  <none>

10. Borrar los 3 pods y el namespace:

        $ kubectl delete po nginx{1..3}
        $ kubectl delete ns my-nginx
