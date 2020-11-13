
###  Apagando el clúster de forma ordenada

* Prerequisitos

 Hacer un backup del etcd antes de apagar el clúster.

 Tener acceso al clúster como usuario con role cluster-admin.

* Procedimiento

1. Obtener la lista de nodos:

       $ nodes=$(oc get nodes -o name)

2. Apagar los nodos:

       $ for node in ${nodes[@]}
       do
           echo "==== Shut down $node ===="
           ssh core@$node sudo shutdown -h 1
       done
3. En este punto ya se podrían apagar los sistemas externos de los que depende el clúster, por ejemplo storage, ldap, haproxy, etc

### Arrancando el cluster de forma ordenada

* Prerequisitos

 Tener acceso al clúster como usuario con role cluster-admin.

 Este procedimiento asume que el clúster fue apagado de forma ordenada.

* Procedimiento

1. Encender los sistemas externos de los que depende el clúster, por ejemplo storage, ldap, haproxy, etc

2. Arrancar todas las máquinas del clúster. Esperar unos 10 min antes de continuar.

3. Verificar que todos los masters están Ready:

       $ oc get nodes -l node-role.kubernetes.io/master

       NAME                           STATUS   ROLES    AGE   VERSION
       ip-10-0-168-251.ec2.internal   Ready    master   75m   v1.18.3
       ip-10-0-170-223.ec2.internal   Ready    master   75m   v1.18.3
       ip-10-0-211-16.ec2.internal    Ready    master   75m   v1.18.3

4. Si algún master no está Ready comprobar si hay certificados pendientes que deban ser aprovados:

       $ oc get csr
       $ oc adm certificate approve <csr_name>

5. Una vez que todos los masters están Ready, verificar los nodos workers:

       $ oc get nodes -l node-role.kubernetes.io/worker

       NAME                           STATUS   ROLES    AGE   VERSION
       ip-10-0-179-95.ec2.internal    Ready    worker   64m   v1.18.3
       ip-10-0-182-134.ec2.internal   Ready    worker   64m   v1.18.3
       ip-10-0-250-100.ec2.internal   Ready    worker   64m   v1.18.3

6. Si algún worker no está Ready comprobar si hay certificados pendientes que deban ser aprovados:

        $ oc get csr
        $ oc adm certificate approve <csr_name>

7. Verificar que no hay Operators en estado DEGRADED a True

        $ oc get clusteroperators

        NAME                                       VERSION   AVAILABLE   PROGRESSING   DEGRADED   SINCE
        authentication                             4.5.0     True        False         False      59m
        cloud-credential                           4.5.0     True        False         False      85m
        cluster-autoscaler                         4.5.0     True        False         False      73m
        config-operator                            4.5.0     True        False         False      73m
        console                                    4.5.0     True        False         False      62m
        csi-snapshot-controller                    4.5.0     True        False         False      66m
        dns                                        4.5.0     True        False         False      76m
        etcd                                       4.5.0     True        False         False      76m
        ...


 8. Comprobar que todos los nodos están en estado Ready

        $ oc get nodes

        NAME                           STATUS   ROLES    AGE   VERSION
        ip-10-0-168-251.ec2.internal   Ready    master   82m   v1.18.3
        ip-10-0-170-223.ec2.internal   Ready    master   82m   v1.18.3
        ip-10-0-179-95.ec2.internal    Ready    worker   70m   v1.18.3
        ip-10-0-182-134.ec2.internal   Ready    worker   70m   v1.18.3
        ip-10-0-211-16.ec2.internal    Ready    master   82m   v1.18.3
        ip-10-0-250-100.ec2.internal   Ready    worker   69m   v1.18.3


9. Si el clúster no arranca de forma adecuada deberá recuperar de un backup del etcd.
