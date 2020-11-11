## Guia de instalación usando DHCP temporal y asignando IPs fijas


### DNS configuration:

https://www.itzgeek.com/how-tos/linux/centos-how-tos/configure-dns-bind-server-on-centos-7-rhel-7.html
https://www.unixmen.com/setting-dns-server-centos-7/

Observaciones:

- Si se tiene proxy configurar las siguientes variables de entorno en el nodo bastion:

       export https_proxy=http://<PROXY>
       export http_proxy=http://<PROXY>
       export no_proxy=<IP_BASTION>,api.<CLUSTER_OCP>.<DOMINIO>,api-int.<CLUSTER_OCP>.<DOMINIO>,.<CLUSTER_OCP>.<DOMINIO>,.<DOMINIO>,<VCENTER>


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

       zone "emea.segur.test" IN {
               type master;
               file "emea.segur.test";
               allow-update { none; };
       };

       zone "77.20.10.in-addr.arpa" IN {
               type master;
               file "reverse.emea.segur.test";
               allow-update { none; };
       };

	Configurar ficheros dns y reverse:

       $ vi /var/named/emea.segur.test

       $TTL 86400
       @   IN  SOA     bastion-test.emea.segur.test. root.emea.segur.test. (
               2011071001  ;Serial
               3600        ;Refresh
               1800        ;Retry
               604800      ;Expire
               86400       ;Minimum TTL
       )
       @       IN  NS          bastion-test.emea.segur.test.
       bastion-test           IN  A   10.20.77.181
       esdc1svdla037          IN  A   10.20.77.182
       esdc1svdla038          IN  A   10.20.77.183
       esdc1svdla039          IN  A   10.20.77.184
       esdc1svdla040          IN  A   10.20.77.185
       esdc1svdla041          IN  A   10.20.77.186
       esdc1svdla042          IN  A   10.20.77.187
       api.ocpesdc1lab01      IN  A   10.20.77.181
       api-int.ocpesdc1lab01  IN  A   10.20.77.181
       apps.ocpesdc1lab01     IN  A   10.20.77.181
       *.apps.ocpesdc1lab01   IN  A   10.20.77.181
       etcd-0.ocpesdc1lab01   IN  A   10.20.77.183
       etcd-1.ocpesdc1lab01   IN  A   10.20.77.184
       etcd-2.ocpesdc1lab01   IN  A   10.20.77.185

       _etcd-server-ssl._tcp.ocpesdc1lab01.emea.segur.test. 86400 IN SRV 0 10 2380 etcd-0.ocpesdc1lab01.emea.segur.test.
       _etcd-server-ssl._tcp.ocpesdc1lab01.emea.segur.test. 86400 IN SRV 0 10 2380 etcd-1.ocpesdc1lab01.emea.segur.test.
       _etcd-server-ssl._tcp.ocpesdc1lab01.emea.segur.test. 86400 IN SRV 0 10 2380 etcd-2.ocpesdc1lab01.emea.segur.test.

	Reverse:

       $ vi  /var/named/reverse.emea.segur.test

       $TTL 86400
       @ IN SOA bastion-test.emea.segur.test. root.emea.segur.test. (
       2014112511 ;Serial
       3600 ;Refresh
       1800 ;Retry
       604800 ;Expire
       86400 ;Minimum TTL
       )
       ;Name Server Information
       @ IN NS bastion-test.emea.segur.test.
       ;Reverse lookup for Name Server
       181 IN PTR bastion-test.emea.segur.test.
       ;PTR Record IP address to HostName
       182 IN PTR esdc1svdla037.emea.segur.test.
       183 IN PTR esdc1svdla038.emea.segur.test.
       184 IN PTR esdc1svdla039.emea.segur.test.
       185 IN PTR esdc1svdla040.emea.segur.test.
       186 IN PTR esdc1svdla041.emea.segur.test.
       187 IN PTR esdc1svdla042.emea.segur.test.

	Para chequear el fichero:

       $ named-checkzone emea.segur.test /var/named/emea.segur.test
       zone emea.segur.test/IN: loaded serial 2011071001
       OK

3. Reiniciar el servicio:

       systemctl stop named
       systemctl start named
       systemctl enable named

4. To avoid NetworkManager to overwrite the file /etc/resolv:

       $ vi /etc/sysconfig/network-scripts/ifcfg-ens192

	Remove line: DNS1="x.x.x.x"

       $ vi /etc/resolv.conf

       nameserver 192.168.1.2

    Comprobar que el DNS resuelve correctamente:

       dig esdc1svdla037.emea.segur.test +short
       dig esdc1svdla038.emea.segur.test +short
       dig esdc1svdla039.emea.segur.test +short
       dig esdc1svdla040.emea.segur.test +short
       dig esdc1svdla041.emea.segur.test +short
       dig esdc1svdla042.emea.segur.test +short

       dig etcd-0.ocpesdc1lab01.emea.segur.test +short
       dig etcd-1.ocpesdc1lab01.emea.segur.test +short
       dig etcd-2.ocpesdc1lab01.emea.segur.test +short

       dig -x 10.20.97.166 +short
       dig -x 10.20.97.167 +short
       dig -x 10.20.97.168 +short
       dig -x 10.20.97.169 +short
       dig -x 10.20.97.170 +short
       dig -x 10.20.97.171 +short

       dig api.ocpesdc1lab01.emea.segur.test +short
       dig api-int.ocpesdc1lab01.emea.segur.test +short
       dig *.apps.ocpesdc1lab01.emea.segur.test +short

       dig _etcd-server-ssl._tcp.ocpesdc1lab01.emea.segur.test SRV +short



### Load Balancer:

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
           server esdc1svdla037 esdc1svdla037.emea.segur.test:6443 check
           server esdc1svdla038 esdc1svdla038.emea.segur.test:6443 check
           server esdc1svdla039 esdc1svdla039.emea.segur.test:6443 check
           server esdc1svdla040 esdc1svdla040.emea.segur.test:6443 check

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
           server esdc1svdla037 esdc1svdla037.emea.segur.test:22623 check
           server esdc1svdla038 esdc1svdla038.emea.segur.test:22623 check
           server esdc1svdla039 esdc1svdla039.emea.segur.test:22623 check
           server esdc1svdla040 esdc1svdla040.emea.segur.test:22623 check

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
           server esdc1svdla041 esdc1svdla041.emea.segur.test:80 check
           server esdc1svdla042 esdc1svdla042.emea.segur.test:80 check

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
           server esdc1svdla041 esdc1svdla041.emea.segur.test:443 check
           server esdc1svdla042 esdc1svdla042.emea.segur.test:443 check


3. Comprobar que el fichero es correcto y reiniciar el servicio:

       $ setsebool -P haproxy_connect_any=1
       $ haproxy -f /etc/haproxy/haproxy.cfg -c
       Configuration file is valid

       $ systemctl stop haproxy
       $ systemctl start haproxy
       $ systemctl enable haproxy


### DHCP Configuration

1. Configurar el servidor dhcp:

       $ yum install -y dhcpd

       $ vi /etc/dhcp/dhcpd.conf

       #
       # DHCP Server Configuration file.
       #   see /usr/share/doc/dhcp*/dhcpd.conf.example
       #   see dhcpd.conf(5) man page
       #
       option domain-name "emea.segur.test";
       option domain-name-servers 10.20.77.181;
       max-lease-time 7200;
       default-lease-time 900;
       authoritative;

       subnet 10.20.77.0 netmask 255.255.255.0{

         option subnet-mask 255.255.255.0;
         option domain-search "emea.segur.test";
         option routers 10.20.77.254;
       }

       host esdc1svdla037 {
         hardware ethernet 00:50:56:94:68:7c;
         fixed-address esdc1svdla037.emea.segur.test;
       }
       host esdc1svdla038 {
         hardware ethernet 00:50:56:94:39:3d;
         fixed-address esdc1svdla038.emea.segur.test;
       }
       host esdc1svdla039 {
         hardware ethernet 00:50:56:94:8c:f2;
         fixed-address esdc1svdla039.emea.segur.test;
       }
       host esdc1svdla040 {
         hardware ethernet 00:50:56:94:9d:0e;
         fixed-address esdc1svdla040.emea.segur.test;
       }
       host esdc1svdla041 {
         hardware ethernet 00:50:56:94:57:b5;
         fixed-address esdc1svdla041.emea.segur.test;
       }
       host esdc1svdla042 {
         hardware ethernet 00:50:56:94:2e:0b;
         fixed-address esdc1svdla042.emea.segur.test;
       }
       deny unknown-clients;

2. Reiniciar los servicios:

       $ systemctl stop dhcpd
       $ systemctl start dhcpd
       $ systemctl enable --now dhcpd


### WEB SERVER

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

### En el nodo Bastión:

1. Deshabilitar el firewall:

       $ systemctl stop firewalld
       $ systemctl disable firewalld

2. Crear directorio donde descargar el software:

       $ mkdir /opt/ocp4
       $ cd /opt/ocp4

3. Descargar sw de instalación y oc (cambiar por la versión que querramos instalar):

	Comprobar en https://mirror.openshift.com/pub/openshift-v4/clients/ocp/

       $ yum install -y wget
       $ wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/4.3.8/openshift-client-linux-4.3.8.tar.gz
       $ wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/4.3.8/openshift-install-linux-4.3.8.tar.gz

4. Obtener el  pull secret from https://cloud.redhat.com/openshift/install/vsphere/user-provisioned

5. Descomprimir los ficheros de instalación y del oc:

       $ ls
        openshift-client-linux-4.3.8.tar.gz  openshift-install-linux-4.3.8.tar.gz

       $ tar -xvf openshift-install-linux-4.3.8.tar.gz
       $ tar -xvf openshift-client-linux-4.3.8.tar.gz

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

       $ vi install/install-config.yaml

       apiVersion: v1
       baseDomain: poc.com
       compute:
       - hyperthreading: Enabled
         name: worker
         replicas: 0
       controlPlane:
         hyperthreading: Enabled
         name: master
         replicas: 3
       metadata:
         name: cluster2
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
         noProxy: .poc.com,.cluster2.poc.com,192.168.1.0/24,ip vcenter

       noProxy: nodes real domains, node alias domain, vcenter ip, nodes IP ranges

10. Instalar el filetranspiler:

        $ yum install -y git
        $ git clone https://github.com/ashcrow/filetranspiler.git
        $ yum install -y python3

	Comprobar si estan el file-magic y el PyYAML y si no estan instalarlos:

        $ pip3 list
        DEPRECATION: The default format will switch to columns in the future. You can use --format=(legacy|columns) (or define a format=(legacy|columns) in your pip.conf under the [list] section) to disable this warning.
        pip (9.0.3)
        setuptools (39.2.0)

        $ pip3 install file-magic PyYAML

	Dar permisos al filetranspiler:

        $ cp filetranspiler/filetranspile /usr/local/bin/
        $ chmod +x /usr/local/bin/filetranspile

10. Copiar el script prepare-ign-fixip.sh en el directorio /opt/ocp4, modificarlo con los valores del cluster a instalar y ejecutarlo:

        $ ./prepare-ign-fixip.sh

!OJO A partir de este punto se dispone de 24 horas para terminar la instalación, pasado ese tiempo hay que ejecutar nuevamente el script.

11. Comprobar que se puede descargar el fichero:

        $ wget http://IP-WEBSERVER:81/bootstrap.ign

### In the vSphere client:

1. Descargar el OVA de RHCOS:

      https://mirror.openshift.com/pub/openshift-v4/dependencies/rhcos/4.3/4.3.8/

2. In the vSphere Client, create a folder in your datacenter to store your VMs.

    Click the VMs and Templates view.
    Right-click the name of your datacenter.
    Click New Folder -> New VM and Template Folder.
    In the window that is displayed, enter the folder name. I named it ocp

    From the Hosts and Clusters tab, right-click your clusters name and click Deploy OVF Template.

    On the Select an OVF tab, specify the name of the RHCOS OVA file that you downloaded.
    rhcos-4.3.8-x86_64-vmware.x86_64.ova

    On the Select a name and folder tab, set a Virtual machine name, such as RHCOS, click the name of your vSphere cluster, and select the folder you created in the previous step.

    On the Select a compute resource tab, click the name of your vSphere cluster.

    On the Select storage tab, configure the storage options for your VM.

      Select Thin Provision.

      Select the datastore that you specified in your install-config.yaml file.

    On the Select network tab, specify the network that you configured for the cluster, if available.

    Do not set any value for Ignition config data and Ignition config data encoding

    After the template is created, edit these settings:

      Set 4 cores, 16GB Memory y 120GB disk.

      Configure the memory as "Reserved"

      Set: "edit settings" -> tab "VM Options" -> sección "Advanced" -> "Latency Sensitivity" -> High

      Go to "edit settings" -> tab "VM Options" -> sección "Advanced" -> "Configuration parameters" -> click "Edit configuration"

      Add configuration params:
      guestinfo.ignition.config.data.encoding -> base64
      disk.EnableUUID -> TRUE
      Do not configure "guestinfo.ignition.config.data"

3. Create the VMs:

	In the vCenter console select the template generated righ now, Right Click -> clone -> clone to virtual machine

	On the Select clone options, select Customize this virtual machines hardware.

	Customize Hardware -> tab Virtual Hardware ->

   	bootstrap y master -> 4 vCPU, 16 GB RAM y 120 GB HD
   	infra ->  8 vCPU, 32 GB RAM y 120 GB HD + [300GB Log, 200GB metrics, 50GB alerts]
   	worker -> 8 vCPU, 16 GB RAM y 120 GB HD

	En Customize Hardware -> tab VM Options -> Advanced -> Edit Configuration ->  Add Configuration Params:

	guestinfo.ignition.config.data -> the base64 file we have created for each node (resultado del script prepare-ign-fixip.sh).

	Repeat for each node of the cluster.

4. Gather the MAC address of each node and configure it in the DHCP config file. Restart dhcpd:

       systemctl restart dhcpd

5. Arrancar las VM y comprobar en la cosonla de cada una de ellas q arrancan correctamente.

6. Ejecutar en el nodo bastion:

       ./openshift-install --dir=/opt/ocp4/install/ wait-for bootstrap-complete --log-level=debug

       ./openshift-install --dir=/opt/ocp4/install/ wait-for install-complete --log-level=debug

7. If this wait-for install-complete fails it could be that the "infra" and worker nodes are not automatically "accepted", to check this:

       export KUBECONFIG=/opt/ocp4/install/auth/kubeconfig

8. If you list nodes with oc get nodes there will be only three nodes, the master nodes.
If you check the pods with oc get pods --all-namespaces there will be pods in pending status because there are no nodes available

	Check the CSR status:

       $ oc get csr
       NAME                                                 AGE     REQUESTOR                                                                   CONDITION
       csr-p22zc                                            3m19s   system:serviceaccount:openshift-machine-config-operator:node-bootstrapper   Pending
       system:etcd-server:etcd-0.ocp42cluster1.jordax.com   2m24s   system:serviceaccount:openshift-machine-config-operator:node-bootstrapper   Pending

	(here there could be mucho more CSRs in pending status)

	Accept them:

       $ oc get csr | grep -v NAME | grep Pending | awk '{print $1}' | xargs oc adm certificate approve

	Try wait-for install-complete again:

       ./openshift-install --dir=/opt/ocp4/install/ wait-for install-complete --log-level=debug

9. Para logarse en el cluster:

       export KUBECONFIG=/opt/ocp4/install/auth/kubeconfig
       oc get nodes

10. Comprobar que todos los operators estan Available:

        watch -n5 oc get clusteroperators


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

       $ oc label node esdc1svdla042.emea.segur.test node-role.kubernetes.io/infra=""

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
                 - esdc1cspla507
                 - esdc1cspla508
                 - esdc1cspla509
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
                 - esdc1cspla507
                 - esdc1cspla508
                 - esdc1cspla509
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
                 - usdc1cspla176
                 - usdc1cspla177
                 - usdc1cspla178
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
