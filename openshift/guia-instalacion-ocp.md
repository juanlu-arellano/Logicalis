## Guia de instalación


### Configuración DNS Server nodo bastión

https://www.itzgeek.com/how-tos/linux/centos-how-tos/configure-dns-bind-server-on-centos-7-rhel-7.html
https://www.unixmen.com/setting-dns-server-centos-7/

Observaciones:

- Si se tiene proxy configurar las siguientes variables de entorno en el nodo bastion:

       export https_proxy=http://<PROXY>
       export http_proxy=http://<PROXY>
       export no_proxy=<IP_BASTION>,api.<CLUSTER_OCP>.<DOMINIO>,api-int.<CLUSTER_OCP>.<DOMINIO>,.<CLUSTER_OCP>.<DOMINIO>,.<DOMINIO>,<VCENTER>
- Enlace para descargar el software para un Trial:

https://www.redhat.com/en/technologies/cloud-computing/openshift/try-it/success


1. Instalar paquetes de DNS:

       yum -y install bind bind-utils

2. Configurar DNS:

       $ vi /etc/named.conf

        Comentar lineas:

       //      listen-on port 53 { 127.0.0.1; };
       //      listen-on-v6 port 53 { ::1; };

	Añadir las IPs desde las que se puede acceder al DNS:

       allow-query     { localhost; 10.20.77.0/24; 10.20.20.0/23;};

	Dentro de "options" añadir:

       forwarders {
               8.8.8.8;
               8.8.4.4;
       };

	Cambiar:

       dnssec-validation yes;

	por

       dnssec-validation no;        

	Añadir las zonas:

       zone "mayor.test" IN {
               type master;
               file "mayor.test";
               allow-update { none; };
       };

       zone "77.20.10.in-addr.arpa" IN {
               type master;
               file "reverse.mayor.test";
               allow-update { none; };
       };

	Configurar ficheros dns y reverse:

       $ vi /var/named/mayor.test

       $TTL 86400
       @   IN  SOA     bastion.mayor.test. root.mayor.test. (
               2011071001  ;Serial
               3600        ;Refresh
               1800        ;Retry
               604800      ;Expire
               86400       ;Minimum TTL
       )
       @       IN  NS          bastion.mayor.test.
       bastion          IN  A   10.20.77.181
       bootstrap        IN  A   10.20.77.182
       master0          IN  A   10.20.77.183
       master1          IN  A   10.20.77.184
       master2          IN  A   10.20.77.185
       worker0          IN  A   10.20.77.186
       worker1          IN  A   10.20.77.187
       worker2          IN  A   10.20.77.188
       api.ocptest      IN  A   10.20.77.181
       api-int.ocptest  IN  A   10.20.77.181
       *.apps.ocptest   IN  A   10.20.77.181


	Reverse:

       $ vi  /var/named/reverse.mayor.test

       $TTL 86400
       @ IN SOA bastion.mayor.test. root.mayor.test. (
       2014112511 ;Serial
       3600 ;Refresh
       1800 ;Retry
       604800 ;Expire
       86400 ;Minimum TTL
       )
       ;Name Server Information
       @ IN NS bastion.mayor.test.
       ;Reverse lookup for Name Server
       181 IN PTR bastion.mayor.test.
       ;PTR Record IP address to HostName
       182 IN PTR bootstrap.mayor.test.
       183 IN PTR master0.mayor.test.
       184 IN PTR master1.mayor.test.
       185 IN PTR master2.mayor.test.
       186 IN PTR worker0.mayor.test.
       187 IN PTR worker1.mayor.test.
       187 IN PTR worker2.mayor.test.

	Para chequear el fichero:

       $ named-checkzone mayor.test /var/named/mayor.test
       zone mayor.test/IN: loaded serial 2011071001
       OK

3. Reiniciar el servicio:

       systemctl stop named
       systemctl start named
       systemctl enable named

4. To avoid NetworkManager to overwrite the file /etc/resolv:

       $ vi /etc/sysconfig/network-scripts/ifcfg-ens192

	Remove line: DNS1="x.x.x.x"

       $ vi /etc/resolv.conf

       nameserver <el que este>
       nameserver 10.20.77.181

    Comprobar que el DNS resuelve correctamente:

       dig bootstrap.mayor.test +short
       dig master0.mayor.test +short
       dig master1.mayor.test +short
       dig master2.mayor.test +short
       dig worker0.mayor.test +short
       dig worker1.mayor.test +short
       dig worker1.mayor.test +short

       dig -x 10.20.77.182 +short
       dig -x 10.20.77.183 +short
       dig -x 10.20.77.184 +short
       dig -x 10.20.77.185 +short
       dig -x 10.20.77.186 +short
       dig -x 10.20.77.187 +short
       dig -x 10.20.77.188 +short

       dig api.ocptest.mayor.test +short
       dig api-int.ocptest.mayor.test +short
       dig *.apps.ocptest.mayor.test +short


### Configurar Load Balancer en el nodo bastión

https://cbonte.github.io/haproxy-dconv/1.7/configuration.html

https://www.howtoforge.com/tutorial/how-to-setup-haproxy-as-load-balancer-for-nginx-on-centos-7/

1. Instalar los paquetes:

       $ yum -y install haproxy
       $ haproxy --version
       HA-Proxy version 1.8.15 2018/12/13

2. Configurar el haproxy:

       $ cd /etc/haproxy/
       $ mv haproxy.cfg haproxy.cfg.orig
       $ vi /etc/haproxy/haproxy.cfg
       global
           log         127.0.0.1 local2 debug
           chroot      /var/lib/haproxy
           pidfile     /var/run/haproxy.pid
           maxconn     4000
           user        haproxy
           group       haproxy
           daemon
            # turn on stats unix socket
            stats socket /var/lib/haproxy/stats

            ssl-default-bind-ciphers PROFILE=SYSTEM
            ssl-default-server-ciphers PROFILE=SYSTEM
       defaults
           mode                    tcp
           log                     global
           option                  tcplog
           option                  dontlognull
           retries                 3
           timeout http-request    10s
           timeout queue           1m
           timeout connect         10s
           timeout client          1m
           timeout server          1m
           timeout http-keep-alive 10s
           timeout check           10s

       ###############################
       # Configuracion OCP 4 #
       ###############################

       #listen stats
       #    bind :9000
       #    mode http
       #    stats enable
       #    stats uri /

       #---------------------------------------------------------------------
       # main frontend for OCP API server
       #---------------------------------------------------------------------
       frontend ocp-api-server
           bind *:6443
           default_backend ocp-api-server
           mode tcp
           option tcplog

       #---------------------------------------------------------------------
       # backend for OCP API servers (master & boostrap nodes)
       #---------------------------------------------------------------------  
       backend ocp-api-server
           balance source
           mode tcp
           server bootstrap bootstrap.mayor.test:6443 check
           server master0 master0.mayor.test:6443 check
           server master1 master1.mayor.test:6443 check
           server master2 master2.mayor.test:6443 check

       #---------------------------------------------------------------------
       # main frontend for OCP config server
       #---------------------------------------------------------------------
       frontend machine-config-server
           bind *:22623
           default_backend machine-config-server
           mode tcp
           option tcplog

       #---------------------------------------------------------------------
       # backend for OCP config servers (master & boostrap nodes)
       #---------------------------------------------------------------------  
       backend machine-config-server
           balance source
           mode tcp
           server bootstrap bootstrap.mayor.test:22623 check
           server master0 master0.mayor.test:22623 check
           server master1 master1.mayor.test:22623 check
           server master2 master2.mayor.test:22623 check

       #---------------------------------------------------------------------
       # main frontend for OCP ingress (router) HTTP server
       #---------------------------------------------------------------------
       frontend ingress-http
           bind *:80
           default_backend ingress-http
           mode tcp
           option tcplog

       #---------------------------------------------------------------------
       # backend for OCP ingress (router) HTTP server
       #---------------------------------------------------------------------  
       backend ingress-http
           balance source
           mode tcp
           server worker0 worker0.mayor.test:80 check
           server worker1 worker1.mayor.test:80 check
           server worker2 worker2.mayor.test:80 check

       #---------------------------------------------------------------------
       # main frontend for OCP ingress (router) HTTPS server
       #---------------------------------------------------------------------
       frontend ingress-https
           bind *:443
           default_backend ingress-https
           mode tcp
           option tcplog

       #---------------------------------------------------------------------
       # backend for OCP ingress (router) HTTPS server
       #---------------------------------------------------------------------
       backend ingress-https
           balance source
           mode tcp
           server worker0 worker0.mayor.test:443 check
           server worker1 worker1.mayor.test:443 check
           server worker2 worker2.mayor.test:443 check

3. Comprobar que el fichero es correcto y reiniciar el servicio:

       $ setsebool -P haproxy_connect_any=1
       $ haproxy -f /etc/haproxy/haproxy.cfg -c
       Configuration file is valid

       $ systemctl stop haproxy
       $ systemctl start haproxy
       $ systemctl enable haproxy


### Configuración DHCP Server en el nodo bastión

1. Configurar el servidor dhcp:

       $ yum install -y dhcpd

       $ vi /etc/dhcp/dhcpd.conf

       #
       # DHCP Server Configuration file.
       #   see /usr/share/doc/dhcp*/dhcpd.conf.example
       #   see dhcpd.conf(5) man page
       #
       option domain-name "mayor.test";
       option domain-name-servers 10.20.77.181;
       max-lease-time 7200;
       default-lease-time 900;
       authoritative;

       subnet 10.20.77.0 netmask 255.255.255.0{

         option subnet-mask 255.255.255.0;
         option domain-search "mayor.test";
         option routers 10.20.77.254;
       }

       host bootstrap {
         hardware ethernet 00:50:56:94:68:7c;
         fixed-address bootstrap.mayor.test;
       }
       host master0 {
         hardware ethernet 00:50:56:94:39:3d;
         fixed-address master0.mayor.test;
       }
       host master1 {
         hardware ethernet 00:50:56:94:8c:f2;
         fixed-address master1.mayor.test;
       }
       host master2 {
         hardware ethernet 00:50:56:94:9d:0e;
         fixed-address master2.mayor.test;
       }
       host worker0 {
         hardware ethernet 00:50:56:94:57:b5;
         fixed-address worker0.mayor.test;
       }
       host worker1 {
         hardware ethernet 00:50:56:94:2e:0b;
         fixed-address worker1.mayor.test;
       }
       host worker1 {
         hardware ethernet 00:50:56:94:2e:0b;
         fixed-address worker1.mayor.test;
       }       
       deny unknown-clients;

2. Reiniciar los servicios:

       $ systemctl stop dhcpd
       $ systemctl start dhcpd
       $ systemctl enable --now dhcpd


### Configuración WEB Server en el nodo bastión

1. Instalar httpd:

       $ yum -y install httpd

2. Change the listen port to 81, the port 80 will be used by the load balancer:

       $ vi /etc/httpd/conf/httpd.conf

       Change Listen 80 with Listen 81
       Listen 81

3. Reiniciar el servicio:

       $ systemctl enable httpd
       $ systemctl start httpd

4. Comprobar que escucha:

       $ ss -tlpn| grep httpd
        LISTEN  0         128                         *:81                     *:*       users:(("httpd",pid=4718,fd=4),("httpd",pid=4717,fd=4),("httpd",pid=4716,fd=4),("httpd",pid=4714,fd=4))

By default the web server serves files from: /var/www/html/

### Descarcar el Sw y prepar la instalación. Desde el nodo bastión

1. Deshabilitar el firewall:

       $ systemctl stop firewalld
       $ systemctl disable firewalld

2. Crear directorio donde descargar el software:

       $ mkdir /opt/ocp4
       $ cd /opt/ocp4

3. Descargar sw de instalación y oc (cambiar por la versión que querramos instalar). En este caso vamos a usar lo que descarguemos del enlace del Trial:

	Comprobar en https://mirror.openshift.com/pub/openshift-v4/clients/ocp/

       $ yum install -y wget
       $ wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/4.5.20/openshift-client-linux-4.5.20.tar.gz
       $ wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/4.5.20/openshift-install-linux-4.5.20.tar.gz

4. Obtener el  pull secret from https://cloud.redhat.com/openshift/install/vsphere/user-provisioned

5. Descomprimir los ficheros de instalación y del oc:

       $ ls
        openshift-client-linux-4.5.20.tar.gz  openshift-install-linux-4.5.20.tar.gz

       $ tar -xvf openshift-install-linux-4.5.20.tar.gz
       $ tar -xvf openshift-client-linux-4.5.20.tar.gz

6. Mover el oc a un directorio que esté en el PATH:

       $ mv kubectl oc /usr/local/bin

7. Crear directorio de instalación:

       $ mkdir install

8. General claves ssh para la instalación:

       $ ssh-keygen -t rsa -b 4096 -N '' -f ~/.ssh/id_rsa
       $ eval "$(ssh-agent -s)"
       $ ssh-add ~/.ssh/id_rsa

       $ cat /root/.ssh/id_rsa.pub
        ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQC/9emXA1nAXxVKVLLjQJ0Kb/gPdLp09vXKA+oJaw9UpmdD2EGP20+99jN48upTLQddHzKmEqRbWmMj4PZxL0OFbTnya7avB4FNrkS8vfdh+y7dwPHvKwmDKKAmrbYoF7Vy9zyVIcYjllhN7kIWfMWwJTMFr2eeMbtFLyuuPDsGsTQ440b8vqNEHw9oLVFX8uxLaJBIcJk26+icLFHOFQwX5V94sJgpN/hE/YOHq8HoWUs5p9yRfU44yCsgXi6HVZAz7Oc1mGUWCutK6IlSHLb4jRMX6M/G9u1EWM6y2pzRZo4baOd3AQ1ZpdRV0LcCt7N1JSaOdpN5Ct8eflWeuIVTmJ7fZdzH3ub7YtKUpGsyXqqtjAoDEPuCb9TKlP2fQZ8ZuslF1id+uR41NLwwg1xTY4RUKQDHv2CZmf4cPq5wbfxAf8uTxvfcuGAwLMWfFOHxoaIPZdYoRBZo9cm9cviAVcmLXhBr8syiuVyKKRDcZIhio9c8kriY4ZdZjZS38NSgmQN5u9dIBbYtU5c2qsvrg4paWZ3lxEnfY5Xxbt8R/hY5mMEmBJiT7PMzTVGSSwPcfrOzOAc8VRFoW4qT9x1JkeFjxs/Ojh1D+gCl+ESg+Xxad0Wo2MIK2IgsrlaUlsk2IEG22ORhSRcKP9JEeHOWjdFWpnWh2WWejWpl2BEqFQ==

9. Crear fichero install-config con los datos reales del cluster (usar la clave ssh publica generada en el paso anterior y el pullSecret):

       $ vi install-config.yaml

       apiVersion: v1
       baseDomain: mayor.test
       compute:
       - hyperthreading: Enabled
         name: worker
         replicas: 3
       controlPlane:
         hyperthreading: Enabled
         name: master
         replicas: 3
       metadata:
         name: ocptest
       networking:
         clusterNetworks:
         - cidr: 10.128.0.0/14
           hostPrefix: 23
         networkType: OpenShiftSDN
         serviceNetwork:
         - 172.30.0.0/16
       platform:
         vsphere:
           vcenter: vcenter.poc.com
           username: administrator@vsphere.local
           password: <password>
           datacenter: Datacenter1
           defaultDatastore: datastore1
       pullSecret: ''
       sshKey: ''         

	En el caso de existir un proxy añadir tambien las siguientes lineas:

       proxy:
         httpProxy: http://<username>:<pswd>@<ip>:<port>
         httpsProxy: http://<username>:<pswd>@<ip>:<port>
         noProxy: .mayor.test,.ocptest.mayor.test,192.168.1.0/24,ip vcenter

       noProxy: nodes real domains, node alias domain, vcenter ip, nodes IP ranges

10. Copiar el fichero install-config.yaml al directorio de instalación y crear los manifiestos:

         $ cp install-config.yaml install
         $ ./openshift-install create manifests --dir=install

!OJO A partir de este punto se dispone de 24 horas para terminar la instalación, pasado ese tiempo hay que ejecutar nuevamente el script.

11. Borrar los manifiestos de los machinesets:

        $ rm -f install/manifests/openshift/99_openshift-cluster-api_master-machines-*.yaml install/manifests/openshift/99_openshift-cluster-api_worker-machineset-*.yaml

12. Modificar el siguiente manifiesto para evitar que los pods se ejecuten en los masters:

        $ sed -i 's/mastersSchedulable: true/mastersSchedulable: false/' install/manifests/cluster-scheduler-02-config.yml

13. Obtener los ficheros `ignition`:

        $ ./openshift-install create ignition-configs --dir=install

14. Crear el fichero append-bootstrap.ign:

        $ vi append-bootstrap.ign
        {
          "ignition": {
            "config": {
              "append": [
                {
                  "source": "10.20.77.181:81/bootstrap.ign",
                  "verification": {}
                }
              ]
            },
            "timeouts": {},
            "version": "2.2.0"
          },
          "networkd": {},
          "passwd": {},
          "storage": {},
          "systemd": {}
        }

15. Copiar el fichero bootstrap.ign al http server:

        $ cp install/bootstrap.ign /var/www/html

16. Comprobar que se puede descargar el fichero:

        $ wget http://10.20.77.181:81/bootstrap.ign

17. Convertir los ficheros ignition a `base64`:

        $ base64 -w0 install/master.ign > install/master.64
        $ base64 -w0 install/worker.ign > install/worker.64
        $ base64 -w0 install/append-bootstrap.ign > install/append-bootstrap.64


### In the vSphere client:

1. Descargar el OVA de RHCOS:

      https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/4.5/4.5.6/rhcos-4.5.6-x86_64-vmware.x86_64.ova

2. In the vSphere Client, create a folder in your datacenter to store your VMs.

 Create a template for the OVA image.

- From the Hosts and Clusters tab, right-click your cluster’s name and click Deploy OVF Template.

- On the Select an OVF tab, specify the name of the RHCOS OVA file that you downloaded.

- On the Select a name and folder tab, set a Virtual machine name, such as RHCOS, click the name of your vSphere cluster, and select the folder you created in the previous step.

- On the Select a compute resource tab, click the name of your vSphere cluster.

- On the Select storage tab, configure the storage options for your VM.

  - Select Thin Provision or Thick Provision, based on your storage preferences.

  - Select the datastore that you specified in your install-config.yaml file.

- On the Select network tab, specify the network that you configured for the cluster, if available.

- If you plan to use the same template for all cluster machine types, do not specify values on the Customize template tab.

 After the template deploys, deploy a VM for a machine in the cluster.

- Right-click the template’s name and click Clone → Clone to Virtual Machine.

- On the Select a name and folder tab, specify a name for the VM. You might include the machine type in the name, such as control-plane-0 or compute-1.

- On the Select a name and folder tab, select the name of the folder that you created for the cluster.

- On the Select a compute resource tab, select the name of a host in your datacenter.

- Optional: On the Select storage tab, customize the storage options.

- On the Select clone options, select Customize this virtual machine’s hardware.

- On the Customize hardware tab, click VM Options → Advanced.

  - Optional: In the event of cluster performance issues, from the Latency Sensitivity list, select High.

  - Click Edit Configuration, and on the Configuration Parameters window, click Add Configuration Params. Define the following parameter names and values:

    - guestinfo.ignition.config.data: Paste the contents of the base64-encoded Ignition config file for this machine type.

    - guestinfo.ignition.config.data.encoding: Specify base64.

    - disk.EnableUUID: Specify TRUE.

 - Alternatively, prior to powering on the virtual machine add via vApp properties:

   - Navigate to a virtual machine from the vCenter Server inventory.

   - On the Configure tab, expand Settings and select vApp options.

   - Scroll down and under Properties apply the configurations from above.

- In the Virtual Hardware panel of the Customize hardware tab, modify the specified values as required. Ensure that the amount of RAM, CPU, and disk storage meets the minimum requirements for the machine type.

   	bootstrap y master -> 4 vCPU, 16 GB RAM y 120 GB HD
   	infra ->  8 vCPU, 32 GB RAM y 120 GB HD + [300GB Log, 200GB metrics, 50GB alerts]
   	worker -> 8 vCPU, 16 GB RAM y 120 GB HD

3. Crear el resto de VM del cluster.

4. Obtener las MACs de las VM y editar el fichero dhcpd.conf. Restart dhcpd:

       $ systemctl restart dhcpd

5. Arrancar la VM del bootstrap y comprobar que arranca correctamente y que la instalación progresa:

       $ ssh core@bootstrap.mayor.test

6. Una vez confirmado que el bootstrap ha arrancado bien, arrancar los masters uno a uno comprobando que cogen su ignition y después los workers.

7. Ejecutar en el nodo bastion el siguiente comando:

       ./openshift-install --dir=install wait-for bootstrap-complete --log-level=debug

8. En este punto ya se puede apagar el nodo bootstrap.

       ./openshift-install --dir=install wait-for install-complete --log-level=debug

9. Para logarse en el cluster:

       $ export KUBECONFIG=/opt/ocp4/install/auth/kubeconfig
       $ oc get nodes

10. If you list nodes with oc get nodes there will be only three nodes, the master nodes.

	Check the CSR status:

        $ oc get csr
        NAME                                                 AGE     REQUESTOR                                                                   CONDITION
        csr-p22zc                                            3m19s   system:serviceaccount:openshift-machine-config-operator:node-bootstrapper   Pending
        system:etcd-server:etcd-0.ocp42cluster1.jordax.com   2m24s   system:serviceaccount:openshift-machine-config-operator:node-bootstrapper   Pending

	(here there could be more CSRs in pending status)

	Accept them:

        $ oc get csr | grep -v NAME | grep Pending | awk '{print $1}' | xargs oc adm certificate approve

 Este paso hay que repetirlo varias veces hasta que están todos los certificados aprobados y todos los nodos Ready

11. Comprobar que todos los operators estan Available:

        $ watch -n5 oc get clusteroperators

12. Monitorizar que la instalación termina correctamente:


13. Comprobar que todos los pods estan OK:

        $ oc get pods -A

### Configurar el registry

1. Configurar el NFS Server en el nodo donde se va a exportar:

       yum install -y nfs-utils rpcbind
       systemctl enable rpcbind
       systemctl enable nfs-lock
       systemctl enable nfs-idmap
       systemctl enable nfs-server

       systemctl start rpcbind
       systemctl start nfs-server
       systemctl start nfs-lock
       systemctl start nfs-idmap

       mkdir -p /nfs-imageregistry
       chmod 777 -R /nfs-imageregistry

       echo '/nfs-imageregistry *(rw,sync,no_wdelay,no_root_squash,insecure,fsid=0)' >> /etc/exports
       exportfs -r

2. Crear PV y PVC:

       mkdir /opt/OCP/registry
       cd /opt/OCP/registry
       vi openshift-registry.yaml (fichero openshift-registry.yaml)

       oc create -f openshift-registry.yaml
       oc get pv
       oc get pvc -n openshift-image-registry

	Cambiar de true a false el que sea la SC por defecto

       oc edit sc thin

	Configurar el storage del registry

       oc edit configs.imageregistry.operator.openshift.io cluster

	Sustituir:

       storage: {}
       managementState: Removed

	Por:

       storage:
         pvc:
           claim: image-registry-storage
       managementState: Managed


3. Comprobar que arranca correctamente:

   	oc get pods -n openshift-image-registry
   	NAME                                              READY   STATUS    RESTARTS   AGE
   	cluster-image-registry-operator-5c4dc465b-69dgg   2/2     Running   0          3d
   	image-registry-d4df5b76d-zszn5                    1/1     Running   0          39s
   	node-ca-fhcwd                                     1/1     Running   0          13m
   	node-ca-hgfjr                                     1/1     Running   0          13m
   	node-ca-hx2pp                                     1/1     Running   0          13m
   	node-ca-snvz4                                     1/1     Running   0          13m
   	node-ca-vhc9v                                     1/1     Running   0          13m
   	node-ca-xhk2m                                     1/1     Running   0          13m


### Configurar nodos Infra:

1. Etiquetar los nodos de "infra":

       $ oc label node worker2.mayor.test node-role.kubernetes.io/infra=""

2. Comprobar que muestra el role correctamente:

       $ oc get nodes
       $ oc get node --show-labels

3. First, create an infra-mcp.yaml file with the following content:

       $ mkdir /opt/ocp4/infra
       vi infra-mcp.yaml
       apiVersion: machineconfiguration.openshift.io/v1
       kind: MachineConfigPool
       metadata:
         name: infra
       spec:
         machineConfigSelector:
           matchExpressions:
             - {key: machineconfiguration.openshift.io/role, operator: In, values: [worker,infra]}
         nodeSelector:
           matchLabels:
             node-role.kubernetes.io/infra: ""

       $ oc create -f infra-mcp.yaml

4. To verify the creation check for a rendered-infra config by running the following to view all Machine Configs:

       oc get mc
       oc get mcp

5. When it’s completed, the status for the infra MCP will show: True for UPDATED, False for UPDATING and False for DEGRADED.

    Note: Since the nodes are rebooted in order to apply this new machine config, this takes several minutes to complete.

6. Añadir taint a los nodos de infra para que no se ejecuten pods del cliente:

       oc adm taint nodes -l node-role.kubernetes.io/infra infra=reserved:NoSchedule infra=reserved:NoExecute


### Configurar el stack de monitorizacion

1. Comprobar si ya existe el configmap:

       $ oc -n openshift-monitoring get configmap cluster-monitoring-config

2. Si no existe crearlo:

       $ oc -n openshift-monitoring create configmap cluster-monitoring-config

3. Editarlo y crear la seccion `data` si no existe:

        $ oc -n openshift-monitoring edit configmap cluster-monitoring-config

        apiVersion: v1
        kind: ConfigMap
        metadata:
          name: cluster-monitoring-config
          namespace: openshift-monitoring
        data:
          config.yaml: |

4. Especificar el `nodeSelector` en el configmap "cluster-monitoring-config" para que los pods de monitoring se ejecuten en los nodos infra:

       $ oc -n openshift-monitoring edit configmap cluster-monitoring-config
       data:
         config.yaml: |
           prometheusK8s:
             nodeSelector:
               node-role.kubernetes.io/infra: ''
             tolerations:
             - key: infra
               value: reserved
               effect: NoSchedule
             - key: infra
               value: reserved
               effect: NoExecute
           alertmanagerMain:
             nodeSelector:
               node-role.kubernetes.io/infra: ''
             tolerations:
             - key: infra
               value: reserved
               effect: NoSchedule
             - key: infra
               value: reserved
               effect: NoExecute
           prometheusOperator:
             nodeSelector:
               node-role.kubernetes.io/infra: ''
             tolerations:
             - key: infra
               value: reserved
               effect: NoSchedule
             - key: infra
               value: reserved
               effect: NoExecute
           grafana:
             nodeSelector:
               node-role.kubernetes.io/infra: ''
             tolerations:
             - key: infra
               value: reserved
               effect: NoSchedule
             - key: infra
               value: reserved
               effect: NoExecute
           k8sPrometheusAdapter:
             nodeSelector:
               node-role.kubernetes.io/infra: ''
             tolerations:
             - key: infra
               value: reserved
               effect: NoSchedule
             - key: infra
               value: reserved
               effect: NoExecute
           kubeStateMetrics:
             nodeSelector:
               node-role.kubernetes.io/infra: ''
             tolerations:
             - key: infra
               value: reserved
               effect: NoSchedule
             - key: infra
               value: reserved
               effect: NoExecute
           telemeterClient:            <<< Quitar este componente si no se quiere telemetria
             nodeSelector:
               node-role.kubernetes.io/infra: ''
             tolerations:
             - key: infra
               value: reserved
               effect: NoSchedule
             - key: infra
               value: reserved
               effect: NoExecute
           openshiftStateMetrics:
             nodeSelector:
               node-role.kubernetes.io/infra: ""
             tolerations:
             - key: infra
               value: reserved
               effect: NoSchedule
             - key: infra
               value: reserved
               effect: NoExecute

5. Los pods se reiniciaran y arrancaran en los nodos de Infra, comprobar con el comando:

       $ oc get pod -n openshift-monitoring -owide

### Configurar el almacenamiento persistente para la monitorizacion:

1. Crear el proyecto "local-storage:

       oc new-project local-storage

2. Desde la consola de OCP en **Opeator Hub**, instalar el operator "local storage" en el proyecto "local-storage"

3. Una vez instalado, aparecera en la lista de "Installed Operators". Verificar que esta instalado correctamente:

       $ oc -n local-storage get pods
       NAME                                      READY   STATUS    RESTARTS   AGE
       local-storage-operator-746bf599c9-vlt5t   1/1     Running   0          19m
       $ oc get csvs -n local-storage
       NAME                                         DISPLAY         VERSION               REPLACES   PHASE
       local-storage-operator.4.2.26-202003230335   Local Storage   4.2.26-202003230335              Succeeded

4. Configurar los local-volumen para los pods de prometheus y alertmanager:

       $ mkdir /opt/ocp4/monitoring
       $ cd /opt/ocp4/monitoring
       $ vi monitoring-local-disk.yaml

       apiVersion: "local.storage.openshift.io/v1"
       kind: "LocalVolume"
       metadata:
         name: "prometheus-local-disks-0"
         namespace: "local-storage"
       spec:
         tolerations:
           - operator: Exists
         nodeSelector:
           nodeSelectorTerms:
           - matchExpressions:
               - key: kubernetes.io/hostname
                 operator: In
                 values:
                 - worker2
         storageClassDevices:
           - storageClassName: "prometheus-local-sc"
             devicePaths:
               - /dev/sdc

       ---

       apiVersion: "local.storage.openshift.io/v1"
       kind: "LocalVolume"
       metadata:
         name: "alertmanager-local-disks-0"
         namespace: "local-storage"
       spec:
         tolerations:
           - operator: Exists
         nodeSelector:
           nodeSelectorTerms:
           - matchExpressions:
               - key: kubernetes.io/hostname
                 operator: In
                 values:
                 - worker2
         storageClassDevices:
           - storageClassName: "alertmanager-local-sc"
             devicePaths:
               - /dev/sdd

       $ oc create -f monitoring-local-disk.yaml

5. NO SE HACE SI SE USAN TAINTS Y TOLERATIONS Permitir la creación de local storage en los nodos de infra (esto no es necesario si tenemos los tolerations):

       oc patch ds <ds diskmaker> -n local-storage -p '{"spec": {"template": {"spec": {"tolerations":[{"operator": "Exists"}]}}}}'
       oc patch ds <ds provisioner> -n local-storage -p '{"spec": {"template": {"spec": {"tolerations":[{"operator": "Exists"}]}}}}'

6. Comprobar que se crean correctamente los pods de local-disk, las storageclass y los pv:

       $ oc get all -n local-storage
       $ oc get sc
       $ oc get pv

7. Modificar el configmap del cluster-monitoring para definir el storage del alertmanager y el prometheus:

       $ oc -n openshift-monitoring edit configmap cluster-monitoring-config

       data:
       config.yaml: |
         prometheusK8s:
           nodeSelector:
             node-role.kubernetes.io/infra: ''
           tolerations:
           - key: infra
             value: reserved
             effect: NoSchedule
           - key: infra
             value: reserved
             effect: NoExecute
           volumeClaimTemplate:
             metadata:
               name: localpvc
             spec:
               storageClassName: prometheus-local-sc
               accessModes:
                 - ReadWriteOnce
               resources:
                 requests:
                   storage: 200Gi
         alertmanagerMain:
           nodeSelector:
             node-role.kubernetes.io/infra: ''
           tolerations:
           - key: infra
             value: reserved
             effect: NoSchedule
           - key: infra
             value: reserved
             effect: NoExecute
           volumeClaimTemplate:
             metadata:
               name: localpvc
             spec:
               storageClassName: alertmanager-local-sc
               accessModes:
                 - ReadWriteOnce
               resources:
                 requests:
                   storage: 50Gi

8. Una vez se guarden los cambios los pods se reniniciaran y haran el bound de los pv. Comprobar:

       oc get pods -n openshift-monitoring
       oc get pvc -n openshift-monitoring

9. Comprobar con un oc describe de los pods que se ha montado correctamente el pvc, por ejemplo:

       oc describe pod prometheus-k8s-0 -n openshift-monitoring
       oc describe pod alertmanager-main-0 -n openshift-monitoring


### Configurar el persistent storage para el Elastic:

1. Crear los LocalVolume:

       $ mkdir /opt/ocp4/logging
       $ cd /opt/ocp4/logging
       $ vi logging-local-disk.yaml

       apiVersion: "local.storage.openshift.io/v1"
       kind: "LocalVolume"
       metadata:
         name: "elastic-local-disks-0"
         namespace: "local-storage"
       spec:
         tolerations:
           - operator: Exists
         nodeSelector:
           nodeSelectorTerms:
           - matchExpressions:
               - key: kubernetes.io/hostname
                 operator: In
                 values:
                 - worker2
        storageClassDevices:
           - storageClassName: "elastic-localstorage-sc"
             devicePaths:
               - /dev/sdb

       $ oc create -f logging-local-disk.yaml

2. NO SE HACE SI SE USA TAINS Y TOLERATIONS. Permitir la creación de local storage en los nodos de infra:

       oc patch ds <ds diskmaker> -n local-storage -p '{"spec": {"template": {"spec": {"tolerations":[{"operator": "Exists"}]}}}}'
       oc patch ds <ds provisioner> -n local-storage -p '{"spec": {"template": {"spec": {"tolerations":[{"operator": "Exists"}]}}}}'

3. Comprobar que se crean correctamente los pods de local-disk, las storageclass y los pv:

       $ oc get all -n local-storage
       $ oc get sc
       $ oc get pv


### Configurar el stack de logging

1. Crear el namespace openshift-operators-redhat donde se instalará el "Elastic operator":

       $ vi eo-namespace.yaml
       apiVersion: v1
       kind: Namespace
       metadata:
         name: openshift-operators-redhat
         annotations:
           openshift.io/node-selector: ""
         labels:
           openshift.io/cluster-monitoring: "true"

       $ oc create -f eo-namespace.yaml

2. Crear el Operator group:

       $ vi eo-og.yaml
       apiVersion: operators.coreos.com/v1
       kind: OperatorGroup
       metadata:
         name: openshift-operators-redhat
         namespace: openshift-operators-redhat
       spec: {}

       $ oc create -f eo-og.yaml

3. Crear el objeto para la suscripción:

       $ vi eo-sub.yaml
       apiVersion: operators.coreos.com/v1alpha1
       kind: Subscription
       metadata:
         name: "elasticsearch-operator"
         namespace: "openshift-operators-redhat"
       spec:
         channel: "4.3"
         installPlanApproval: "Automatic"
         source: "redhat-operators"
         sourceNamespace: "openshift-marketplace"
         name: "elasticsearch-operator"

       $ oc create -f eo-sub.yaml

4. Cambiarse al proyecto "openshift-operators-redhat" y crear el rbac que da acceso a prometheus a dicho proyecto:

       $ oc project openshift-operators-redhat

       $ vi eo-rbac.yaml
       apiVersion: rbac.authorization.k8s.io/v1
       kind: Role
       metadata:
         name: prometheus-k8s
         namespace: openshift-operators-redhat
       rules:
       - apiGroups:
         - ""
         resources:
         - services
         - endpoints
         - pods
         verbs:
         - get
         - list
         - watch
       ---
       apiVersion: rbac.authorization.k8s.io/v1
       kind: RoleBinding
       metadata:
         name: prometheus-k8s
         namespace: openshift-operators-redhat
       roleRef:
         apiGroup: rbac.authorization.k8s.io
         kind: Role
         name: prometheus-k8s
       subjects:
       - kind: ServiceAccount
         name: prometheus-k8s
         namespace: openshift-operators-redhat

       $ oc create -f eo-rbac.yaml

5. Comprobar la instalacion del operador:

       $ oc get csv --all-namespaces |grep "Elasticsearch Operator"

       NAMESPACE                                               NAME                                         DISPLAY                  VERSION               REPLACES   PHASE
       default                                                 elasticsearch-operator.4.3.1-202002032140    Elasticsearch Operator   4.3.1-202002032140               Succeeded
       kube-node-lease                                         elasticsearch-operator.4.3.1-202002032140    Elasticsearch Operator   4.3.1-202002032140               Succeeded
       kube-public                                             elasticsearch-operator.4.3.1-202002032140    Elasticsearch Operator   4.3.1-202002032140               Succeeded
       kube-system                                             elasticsearch-operator.4.3.1-202002032140    Elasticsearch Operator   4.3.1-202002032140               Succeeded
       openshift-apiserver-operator                            elasticsearch-operator.4.3.1-202002032140    Elasticsearch Operator   4.3.1-202002032140               Succeeded
       openshift-apiserver                                     elasticsearch-operator.4.3.1-202002032140    Elasticsearch Operator   4.3.1-202002032140               Succeeded
       openshift-authentication-operator                       elasticsearch-operator.4.3.1-202002032140    Elasticsearch Operator   4.3.1-202002032140               Succeeded
       openshift-authentication                                elasticsearch-operator.4.3.1-202002032140    Elasticsearch Operator   4.3.1-202002032140               Succeeded
       ...

6. Crear el proyecto para instalar el "cluster logging operator":

       vi clo-namespace.yaml
       apiVersion: v1
       kind: Namespace
       metadata:
         name: openshift-logging
         annotations:
           openshift.io/node-selector: ""
         labels:
           openshift.io/cluster-monitoring: "true"

       oc create -f clo-namespace.yaml

7. Instalar el Cluster Logging Operator desde la consola de OCP:

    En la consola de OCP, click en **Operators → OperatorHub**.
    Elegir Cluster Logging de la lista de Operators, y click en **Install**.
    En la pagina **Create Operator Subscription**, seleccionar el namespace `openshift-logging` y click en **Suscribe**.
    Comprobar que los operators se han instalado correctamente en la consola de OCP **Operators → Installed Opetators**

8. Crear una instancia para el Cluster logging

    En la consola de OCP ir a **Administration → Custom Resource Definitions**.
    En la pagina **Custom Resource Definitions**, click en **ClusterLogging**.
    En la pagina **Custom Resource Definition Overview**, seleccionar **View Instances en el menu Actions**.
    Aparecera un mensaje de error. Refrescar la pagina para cargar los datos.
    En la pagina **Cluster Loggings**, click en **Create Cluster Logging**.

9. Cambiar el yaml que aparece por el contenido del fichero clusterlogging.yaml

       apiVersion: logging.openshift.io/v1
       kind: ClusterLogging
       metadata:
         name: instance
         namespace: openshift-logging
       spec:
         collection:
           logs:
             fluentd:
               resources:
                 limits:
                   memory: 1Gi
                 requests:
                   cpu: 100m
                   memory: 1Gi
               tolerations:
               - effect: NoSchedule
                 key: infra
                 value: reserved
               - effect: NoExecute
                 key: infra
                 value: reserved
             type: fluentd
         curation:
           curator:
             nodeSelector:
               node-role.kubernetes.io/infra: ""
             resources:
               limits:
                 memory: 200Mi
               requests:
                 cpu: 100m
                 memory: 200Mi
             schedule: '*/10 * * * *'
             tolerations:
             - effect: NoSchedule
               key: infra
               value: reserved
             - effect: NoExecute
               key: infra
               value: reserved
           type: curator
         logStore:
           elasticsearch:
             nodeCount: 3
             nodeSelector:
               node-role.kubernetes.io/infra: ""
             redundancyPolicy: SingleRedundancy
             resources:
               limits:
                 memory: 16Gi
               requests:
                 cpu: 100m
                 memory: 16Gi
             storage:
               size: 300G
               storageClassName: elastic-localstorage-sc
             tolerations:
             - effect: NoSchedule
               key: infra
               value: reserved
             - effect: NoExecute
               key: infra
               value: reserved
           type: elasticsearch
         managementState: Managed
         nodeSelector:
           node-role.kubernetes.io/infra: ""
         tolerations:
         - effect: NoSchedule
           key: infra
           value: reserved
         - effect: NoExecute
           key: infra
           value: reserved
         visualization:
           kibana:
             nodeSelector:
               node-role.kubernetes.io/infra: ""
             proxy:
               resources:
                 limits:
                   memory: 100Mi
                 requests:
                   cpu: 100m
                   memory: 100Mi
             replicas: 1
             resources:
               limits:
                 memory: 1Gi
               requests:
                 cpu: 100m
                 memory: 1Gi
             tolerations:
             - effect: NoSchedule
               key: infra
               value: reserved
             - effect: NoExecute
               key: infra
               value: reserved
           type: kibana

10. Comprobar que los pods arrancan correctamente en los nodos de infra:

        oc get pods -n openshift-logging -owide

https://access.redhat.com/solutions/5034771

### Configurar el Eventrouter:

1. Crear el template de los recursos del Eventrouter y desplegarlo:

       $ vi eventrouter.yaml                 (fichero eventrouter.yaml)
       $ oc process -f eventrouter.yaml | oc apply -f -

2. Comprobar que el eventrouter esta instalado:

       $ oc get pods --selector  component=eventrouter -o name -n openshift-logging
       $ oc logs logging-eventrouter-d649f97c8-qvv8r

       {"verb":"ADDED","event":{"metadata":{"name":"elasticsearch-operator.v0.0.1.158f402e25397146","namespace":"openshift-operators","selfLink":"/api/v1/namespaces/openshift-operators/events/elasticsearch-operator.v0.0.1.158f402e25397146","uid":"37b7ff11-4f1a-11e9-a7ad-0271b2ca69f0","resourceVersion":"523264","creationTimestamp":"2019-03-25T16:22:43Z"},"involvedObject":{"kind":"ClusterServiceVersion","namespace":"openshift-operators","name":"elasticsearch-operator.v0.0.1","uid":"27b2ca6d-4f1a-11e9-8fba-0ea949ad61f6","apiVersion":"operators.coreos.com/v1alpha1","resourceVersion":"523096"},"reason":"InstallSucceeded","message":"waiting for install components to report healthy","source":{"component":"operator-lifecycle-manager"},"firstTimestamp":"2019-03-25T16:22:43Z","lastTimestamp":"2019-03-25T16:22:43Z","count":1,"type":"Normal"}}


### Configurar Curator (persistencia de los logs)

1. Editar cm del curator indicando la cantidad de dias que se quieren tener de retencion:

       oc edit cm/curator -n openshift-logging
       descomentar las lineas del .default y .operators poner los días que se quieren


### Mover el Router a los nodos de infra

1. Mover el router:

       oc patch ingresscontroller/default -n  openshift-ingress-operator  --type=merge -p '{"spec":{"nodePlacement": {"nodeSelector": {"matchLabels": {"node-role.kubernetes.io/infra": ""}},"tolerations": [{"effect":"NoSchedule","key": "infra","value": "reserved"},{"effect":"NoExecute","key": "infra","value": "reserved"}]}}}'

2. Añadir replicas:

       oc patch ingresscontroller/default -n openshift-ingress-operator --type=merge -p '{"spec":{"replicas": 3}}'

3. Comprobar que se ha movido

       oc get pod -n openshift-ingress -owide


### Mover el registry:

1. Mover el registry a los nodos de infra:

       oc patch config/cluster --type=merge -p '{"spec":{"nodeSelector": {"node-role.kubernetes.io/infra": ""},"tolerations": [{"effect":"NoSchedule","key": "infra","value": "reserved"},{"effect":"NoExecute","key": "infra","value": "reserved"}]}}'

2. Comprobar:

       oc get pod -nopenshift-image-registry -owide


### Comprobaciones finales

1. Comprobar que no hay pods en fallo:

       oc get pods -A |grep -i -v running |grep -i -v comple

2. Comprobar que se tiene acceso a al `grafana` y al `kibana`
