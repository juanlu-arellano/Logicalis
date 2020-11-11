# Eliminar un nodo

Cuando se elimina un nodo del cluster, el objeto del nodo se elimina en Kubernetes, pero los pods que existen en el nodo en sí no se eliminan. Cualquier pod que no se desplegara con un Controller quedara inaccesible para OpenShift, los pods desplegados por un Controller se redesplegaran en otros nodos disponibles, y el resto de pods tendrían que eliminarse manualmente.

## Para eliminar un nodo del clúster de OpenShift:

1. [Evacuar los pods del nodo a eliminar](Evacuate-pods)
2. Borrar el nodo:

        $ oc delete node <node>

3. Comprobar que el nodo se ha borrado de la lista de nodos:

        $ oc get nodes
