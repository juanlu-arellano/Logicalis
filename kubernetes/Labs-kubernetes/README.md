# Laboratorios Introduccion a kubernetes

### 1. Requisitos:
- Maquina Virtual linux (la misma que usamos para la parte de docker) con al menos 4GB libres en el home del usuario y 2 CPUs
- Conexion a internet

### 2. Instalacion de kubeclt

    $ curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
    $ chmod +x ./kubectl
    $ sudo mv ./kubectl /usr/local/bin/kubectl

### 3. Instalacion de minikube
    $ curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && chmod +x minikube && sudo cp minikube /usr/local/bin/ && rm minikube
    $ sudo usermod -aG docker <usuario>
    $ newgrp docker
    $ ssh-keygen -t rsa -b 4096
    $ minikube start --driver=docker

### 4. En este punto ya se puede tener acceso al minikube con el kubectl
    $ kubectl cluster-info
    Kubernetes master is running at https://192.168.99.100:8443
    CoreDNS is running at https://192.168.99.100:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

    To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

### 5. Para acceder a la VM de minikube
    $ minikube ssh

### 6. Acceso al dashboard de minikube
    $ minikube dashboard
