# Instrucciones Laboratorio 1

## Crear proyecos, pods, services, routes, etc

Nota: Cambiar el nombre del namespace en el fichero yaml por el que se este usando en cada caso

### 1. Logarse en el cluster
Obtener el comando de logging desde la consola de OCP -> Copy Logging Command -> Display Token (ejemplo):

    $ oc login --token=FmJXS2XQoseeC3bBZvhfmZOCtk1kD3AWaQzQE_01Ldg --server=https://api.ocp.hx.logicalis.com:6443

### 2. Crear un proyecto

```shell
$ export GUID=<iniciales>
$ oc new-project ${GUID}-formacion --description="Proyecto de formacion de ${GUID}" --display-name="${GUID}-formacion"
Now using project "lgp-formacion" on server "https://api.ocp.hx.logicalis.com:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node

$ oc describe project ${GUID}-formacion
```

### 3. Crear pod de nginx

```shell
$ cd <path repo>/Lab-1
```

*Nota: editar el fichero nginx.yaml para poner el GUID definido en cada caso*

```shell
$ oc create -f nginx.yaml
pod/nginx created
```

### 4. Comprobar que el pod esta corriendo

```shell
$ oc get pods -n $GUID-formacion
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          1m

$ oc get pods -owide
NAME    READY   STATUS    RESTARTS   AGE    IP             NODE                            NOMINATED NODE   READINESS GATES
nginx   1/1     Running   0          111m   10.128.2.245   worker-0.ocp.hx.logicalis.com   <none>           <none>
```

### 5. Ver informacion del pod

```shell
$ oc describe pod nginx
Name:               nginx
Namespace:          lgp-formacion
Priority:           0
PriorityClassName:  <none>
Node:               worker-0.ocp.hx.logicalis.com/10.20.97.113
Start Time:         Thu, 12 Dec 2019 11:54:40 +0100
Labels:             app=nginx
Annotations:        k8s.v1.cni.cncf.io/networks-status:
                      [{
                          "name": "openshift-sdn",
                          "interface": "eth0",
                          "ips": [
                              "10.128.2.245"
                          ],
                          "default": true,
                          "dns": {}
                      }]
                    openshift.io/scc: anyuid
Status:             Running
IP:                 10.128.2.245
Containers:
  nginx:
    Container ID:   cri-o://7ec8547f6b2fa766d72c92cfffbe548f4f042f11e1d4b3966ad403b809c5dc43
    Image:          nginx
    Image ID:       docker.io/library/nginx@sha256:189cce606b29fb2a33ebc2fcecfa8e33b0b99740da4737133cdbcee92f3aba0a
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Thu, 12 Dec 2019 11:55:07 +0100
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-p7b25 (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  default-token-p7b25:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-p7b25
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age   From                                    Message
  ----    ------     ----  ----                                    -------
  Normal  Scheduled  62s   default-scheduler                       Successfully assigned lgp-formacion/nginx to worker-0.ocp.hx.logicalis.com
  Normal  Pulling    54s   kubelet, worker-0.ocp.hx.logicalis.com  Pulling image "nginx"
  Normal  Pulled     36s   kubelet, worker-0.ocp.hx.logicalis.com  Successfully pulled image "nginx"
  Normal  Created    35s   kubelet, worker-0.ocp.hx.logicalis.com  Created container nginx
  Normal  Started    35s   kubelet, worker-0.ocp.hx.logicalis.com  Started container nginx
```

### 6. Acceder al pod con curl

6.1. Acceder a uno de los masters del cluster por ssh (ejemplo):

```shell
$ ssh core@master1dev.ocpdevmad01.tic1.intranet
```

6.2. Comprbar el acceso con el curl (La IP a la que accede el curl es la IP del pod punto 4 ):

```shell
$ curl http://10.128.2.245
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

### 7. Comprobar en la consola de OCP como se ve el pod de nginx creado:

![alt Pods][imagen1]

[imagen1]: images/pod-nginx-1.png

### 8. Crear un servicio para el pods

```shell
$ oc expose pod nginx
service/nginx exposed

$ oc get svc
NAME    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
nginx   ClusterIP   172.30.15.125   <none>        80/TCP    7s
```

### 9. Crear un route para el servicio

```shell
$ oc expose svc nginx
route.route.openshift.io/nginx exposed

$ oc get route
NAME    HOST/PORT                                       PATH   SERVICES   PORT   TERMINATION   WILDCARD
nginx   nginx-lgp-formacion.apps.ocp.hx.logicalis.com          nginx      80                   None
```

### 10. Comprobar que se han creado tanto el servicio como el route en la consola de OCP

![alt Route][imagen2]

[imagen2]: images/routes2.png

### 11. Comprobar el acceso al nginx a traves del route

![alt Route][imagen3]

[imagen3]: images/routes3.png
