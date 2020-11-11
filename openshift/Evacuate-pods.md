# Evacuar pods de un nodo

Evacuar los pods permite migrar los pods seleccionados o todos los pods de un nodo o nodos determinados.

Solo se puede evacuar pods desplegados por un Controller. Los Controller crearan nuevos pods en otros nodos y eliminaran los pods existentes de los nodos especificados.

Los pods sin replicas, es decir, aquellos que no están desplegados por un Controller, no se evacuan.

Puede evacuar un subconjunto de pods especificando un selector de pod. Los selectores de pod se basan en etiquetas, por lo que todos los pods con la etiqueta especificada serán evacuados.

## Procedimiento

1. Marcar los nodos como unschedulable

        $ oc adm cordon <node1>
        NAME        STATUS                        ROLES     AGE       VERSION
        <node1>     NotReady,SchedulingDisabled   worker   1d        v1.14.6+c4799753c

2. Para evacuar los pods (ejemplo):

        $ oc adm drain <node1> <node2> [--pod-selector=<pod_selector>]

3. Para forzar ademas el borrado de los pods sin replicas:

        $ oc adm drain <node1> <node2> --force=true

4. Para dar un periodo de gracia:

        $ oc adm drain <node1> <node2> --grace-period=-1

5. Para simular la evacuacion de pods sin que se llegue a realizar:

        $ oc adm drain <node1> <node2>  --dry-run=true

6. Marcar los nodos como schedulable:

        $ oc adm uncordon <node1>
