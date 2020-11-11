# Añadir nodo worker RHCOs al cluster


https://access.redhat.com/solutions/4799921


#### Ejecutar desde el directorio donde estan los ficheros ignitio con que se instaló el cluster


1. Exportar variable con el api-int del cluster:

        $ export MCS=api-int.openshift42.logicalis.com:22623

2. A continuación ejecutar el siguiente comando para obtener el nuevo certificado:

       $ echo "q" | openssl s_client -connect $MCS  -showcerts | awk '/-----BEGIN CERTIFICATE-----/,/-----END CERTIFICATE-----/' | base64 --wrap=0 | tee ./api-int.base64 && \
       sed --regexp-extended --in-place=.backup "s%base64,[^,]+%base64,$(cat ./api-int.base64)\"%" ./worker.ign

3. Check if the file changed, if yes, then upload the updated worker.ign file to the HTTP server in use for Red Hat OpenShift Container Platform 4.x cluster deployment and start the worker machine.

4. Codificar el nuevo fichero ignitio en base64:

       $ base64 -w0 worker.ign >worker.64

5. Crear la VM del nodo a añadir (clonando el template que se usó para la instalación. Configurar las siguientes variables en la VM:

       guestinfo.ignition.config.data.encoding -> base64
       disk.EnableUUID -> TRUE
       guestinfo.ignition.config.data -> nuevo worker.64

6. Una vez el nodo este arrancado aprobar los certificados para que de esta forma entre en el cluster (comprobar y aprobar los certificados que iran saliendo a medida que se vayan aprobando):

       $ oc get csr
       $ oc get csr -o name | xargs oc adm certificate approve
       certificatesigningrequest.certificates.k8s.io/csr-hlp79 approved
       certificatesigningrequest.certificates.k8s.io/csr-tmfz2 approved

7. En este punto ya deberia aparecer el nuevo nodo dentro del cluster:

       $ oc get nodes
       NAME                                STATUS   ROLES    AGE     VERSION
       master1dev.ocpdevmad01.tic1.intranet   Ready    master   4d17h   v1.14.6+6ac6aa4b0
       master2dev.ocpdevmad01.tic1.intranet   Ready    master   4d17h   v1.14.6+6ac6aa4b0
       master3dev.ocpdevmad01.tic1.intranet   Ready    master   4d17h   v1.14.6+6ac6aa4b0
       mgmt1dev.ocpdevmad01.tic1.intranet     Ready    worker   4d17h   v1.14.6+6ac6aa4b0
       worker1dev.ocpdevmad01.tic1.intranet   Ready    worker   4d17h   v1.14.6+6ac6aa4b0
       worker2dev.ocpdevmad01.tic1.intranet   Ready    worker   4d17h   v1.14.6+6ac6aa4b0
       worker3dev.ocpdevmad01.tic1.intranet   Ready    worker   4d17h   v1.14.6+6ac6aa4b0
       worker4dev.ocpdevmad01.tic1.intranet   Ready    worker   4d17h   v1.14.6+6ac6aa4b0
