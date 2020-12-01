# Crear NFS StorageClass

Este procedimiento es para crear una `StorageClass` usando un `NFS-provisioner`. En este ejemplo exportamos un FS desde una maquina.

## Crear y exportar el FS

1. Instalar y arrancar el nfs-server en caso de que no este hecho:

        yum install -y nfs-utils
        systemctl enable rpcbind
        systemctl enable nfs-server
        systemctl start rpcbind
        systemctl start nfs-server

        firewall-cmd --permanent --zone=public --add-service=nfs
        firewall-cmd --permanent --zone=public --add-service=rpcbind
        firewall-cmd --reload

2. Crear el FS que vamos a exportar y darle los permisos necesarios:

        $ mkdir /home/nfs-openshift
        $ chmod -R 777 /home/nfs-openshift
        $ chown -R nfsnobody:nfsnobody /home/nfs-openshift

3. Editar el fichero `/etc/exportfs` y añadir la siguiente linea para nuestro FS a exportar:

        /home/nfs-openshift *(rw,sync,no_root_squash)

4. Reiniciar el servicio nfs-server:

        $ systemctl restart nfs-server

## Modificar los ficheros del despliegue

1. Copiar los ficheros yaml necesarios estan en el repo directorio `nfs-storageclass`

2. Modificar el fichero deployment.yaml para indicar la IP del servidor NFS y el path que se esta exportando. En este ejemplo es:

        env:
          - name: PROVISIONER_NAME
            value: nfs-dinamic-storage
          - name: NFS_SERVER
            value: 10.20.90.1
          - name: NFS_PATH
            value: /home/nfs-openshift
        volumes:
        - name: nfs-client-root
        nfs:
          server: 10.20.90.1
          path: /home/nfs-openshift

## Desplegar

1. Crear el proyecto donde va a correr el nfs-provider:

        $ oc new-project nfs-provisioner

2. Crear el serviceaccount y los roles y rolebinding necesarios:

        $ oc create -f rbac.yaml

3. Dar los permisos necesarios al serviceaccount para que pueda hacer montajes:

        $ oc adm policy add-scc-to-user hostmount-anyuid system:serviceaccount:nfs-provisioner:nfs-client-provisioner

4. Crear el deployment y la storageclass:

        $ oc create -f deployment.yaml -f class.yaml

5. Probar que funciona creando un pvc y un pod que lo usa:

        $ oc create -f test-claim.yaml
        $ oc create -f test-pod.yaml

6. El resultado debe ser que en el FS `/home/nfs-openshift` se crea un directorio con el nonmbre del pvc y dentro hay un fichero que se llama SUCCESS:

        $ ll /home/nfs-openshift/
        total 0
        drwxrwxrwx. 2 root root 20 Feb 13 12:20 nfs-provisioner-test-claim-pvc-b3233145-4e52-11ea-b682-005056948d6e
        $ cd nfs-provisioner-test-claim-pvc-b3233145-4e52-11ea-b682-005056948d6e/
        $ ll
        total 0
        -rw-r--r--. 1 root root 0 Feb 13 12:20 SUCCESS


Referencias:

https://medium.com/faun/openshift-dynamic-nfs-persistent-volume-using-nfs-client-provisioner-fcbb8c9344e
