### Multi-Container Pods

Practice questions based on these concepts

* Understand multi-container pod design patterns (eg: ambassador, adaptor, sidecar)

#### 29. Create a Pod with three busy box containers with commands “ls; sleep 3600;”, “echo Hello World; sleep 3600;” and “echo this is the third container; sleep 3600” respectively and check the status
<details><summary>show</summary>

```bash
// first create single container pod with dry run flag
kubectl run busybox --image=busybox --restart=Never --dry-run -o yaml -- bin/sh -c "sleep 3600; ls" > multi-container.yaml
// edit the pod to following yaml and create it
kubectl create -f multi-container.yaml
kubectl get po busybox
```

</p>
</details>

#### 30. Check the logs of each container that you just created
<details><summary>show</summary>

```bash
kubectl logs busybox -c busybox1
kubectl logs busybox -c busybox2
kubectl logs busybox -c busybox3
```

</p>
</details>

#### 31. Check the previous logs of the second container busybox2 if any
<details><summary>show</summary>

```bash
kubectl logs busybox -c busybox2 --previous
```

</p>
</details>

#### 32. Run command ls in the third container busybox3 of the above pod
<details><summary>show</summary>

```bash
kubectl exec busybox -c busybox3 -- ls
```

</p>
</details>

#### 33. Show metrics of the above pod containers and puts them into the file.log and verify
<details><summary>show</summary>

```bash
kubectl top pod busybox --containers
// putting them into file
kubectl top pod busybox --containers > file.log
cat file.log
```

</p>
</details>

#### 34. Create a Pod with main container busybox and which executes this “while true; do echo ‘Hi I am from Main container’ >> /var/log/index.html; sleep 5; done” and with sidecar container with nginx image which exposes on port 80. Use emptyDir Volume and mount this volume on path /var/log for busybox and on path /usr/share/nginx/html for nginx container. Verify both containers are running.
<details><summary>show</summary>

```bash
// create an initial yaml file with this
kubectl run multi-cont-pod --image=busbox --restart=Never --dry-run -o yaml > multi-container.yaml
// edit the yml as below and create it
kubectl create -f multi-container.yaml
kubectl get po multi-cont-pod

multi-container.yaml

apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: multi-cont-pod
  name: multi-cont-pod
spec:
  volumes:
  - name: var-logs
    emptyDir: {}
  containers:
  - image: busybox
    command: ["/bin/sh"]
    args: ["-c", "while true; do echo 'Hi I am from Main container' >> /var/log/index.html; sleep 5;done"]
    name: main-container
    resources: {}
    volumeMounts:
    - name: var-logs
      mountPath: /var/log
  - image: nginx
    name: sidecar-container
    resources: {}
    ports:
      - containerPort: 80
    volumeMounts:
    - name: var-logs
      mountPath: /usr/share/nginx/html
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

</p>
</details>

#### 35. Exec into both containers and verify that main.txt exist and query the main.txt from sidecar container with curl localhost
<details><summary>show</summary>

```bash
// exec into main container
kubectl exec -it  multi-cont-pod -c main-container -- sh
cat /var/log/main.txt
// exec into sidecar container
kubectl exec -it  multi-cont-pod -c sidecar-container -- sh
cat /usr/share/nginx/html/index.html
// install curl and get default page
kubectl exec -it  multi-cont-pod -c sidecar-container -- sh
# apt-get update && apt-get install -y curl
# curl localhost
```

</p>
</details>
