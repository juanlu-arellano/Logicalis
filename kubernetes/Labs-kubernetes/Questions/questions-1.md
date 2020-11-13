Core Concepts

Practice questions based on these concepts

* Understand Kubernetes API Primitives
* Create and Configure Basic Pods

#### 1. List all the namespaces in the cluster:

<details><summary>show</summary>
<p>

```bash
kubectl get namespaces
kubectl get ns
```

</p>
</details>

#### 2. List all the pods in all namespaces
<details><summary>show</summary>
<p>

```bash
kubectl get po --all-namespaces
```

</p>
</details>

#### 3. List all the pods in the particular namespace
<details><summary>show</summary>
<p>

```bash
kubectl get po -n <namespace name>
```

</p>
</details>

#### 4. List all the services in the particular namespace
<details><summary>show</summary>
<p>

```bash
kubectl get svc -n <namespace name>
```

</p>
</details>

#### 5. List all the pods showing name and namespace with a json path expression
<details><summary>show</summary>
<p>

```bash
kubectl get pods -o=jsonpath="{.items[*]['metadata.name', 'metadata.namespace']}"
```

</p>
</details>

#### 6. Create an nginx pod in a default namespace and verify the pod running
<details><summary>show</summary>
<p>

```bash
// creating a pod
kubectl run nginx --image=nginx --restart=Never
// List the pod
kubectl get po
```

</p>
</details>

#### 7. Create the same nginx pod with a yaml file
<details><summary>show</summary>
<p>

```bash
// get the yaml file with --dry-run flag
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx-pod.yaml

// cat nginx-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}

// create a pod
kubectl create -f nginx-pod.yaml
```

</p>
</details>

#### 8. Output the yaml file of the pod you just created
<details><summary>show</summary>
<p>

```bash
kubectl get po nginx -o yaml
```

</p>
</details>

#### 9. Output the yaml file of the pod you just created without the cluster-specific information
<details><summary>show</summary>
<p>

```bash
kubectl get po nginx -o yaml --export
```

</p>
</details>

#### 10. Get the complete details of the pod you just created
<details><summary>show</summary>
<p>

```bash
kubectl describe pod nginx
```

</p>
</details>

#### 11. Delete the pod you just created
<details><summary>show</summary>
<p>

```bash
kubectl delete po nginx
kubectl delete -f nginx-pod.yaml
```

</p>
</details>

#### 12. Delete the pod you just created without any delay (force delete)
<details><summary>show</summary>
<p>

```bash
kubectl delete po nginx --grace-period=0 --force
```

</p>
</details>

#### 13. Create the nginx pod with version 1.17.4 and expose it on port 80
<details><summary>show</summary>
<p>

```bash
kubectl run nginx --image=nginx:1.17.4 --restart=Never --port=80
```

</p>
</details>

#### 14. Change the Image version to 1.15-alpine for the pod you just created and verify the image version is updated
<details><summary>show</summary>
<p>

```bash
kubectl set image pod/nginx nginx=nginx:1.15-alpine
kubectl describe po nginx
// another way it will open vi editor and change the version
kubeclt edit po nginx
kubectl describe po nginx
```

</p>
</details>

#### 15. Change the Image version back to 1.17.1 for the pod you just updated and observe the changes
<details><summary>show</summary>
<p>

```bash
kubectl set image pod/nginx nginx=nginx:1.17.1
kubectl describe po nginx
kubectl get po nginx -w # watch it
```

</p>
</details>

#### 16. Check the Image version without the describe command
<details><summary>show</summary>
<p>

```bash
kubectl get po nginx -o jsonpath='{.spec.containers[].image}{"\n"}'
```

</p>
</details>

#### 17. Create the nginx pod and execute the simple shell on the pod
<details><summary>show</summary>
<p>

```bash
// creating a pod
kubectl run nginx --image=nginx --restart=Never
// exec into the pod
kubectl exec -it nginx /bin/sh
```

</p>
</details>

#### 18. Get the IP Address of the pod you just created
<details><summary>show</summary>
<p>

```bash
kubectl get po nginx -o wide
```

</p>
</details>

#### 19. Create a busybox pod and run command ls while creating it and check the logs
<details><summary>show</summary>
<p>

```bash
kubectl run busybox --image=busybox --restart=Never -- ls
kubectl logs busybox
```

</p>
</details>

#### 20. If pod crashed check the previous logs of the pod
<details><summary>show</summary>
<p>

```bash
kubectl logs busybox -p
```

</p>
</details>

#### 21. Create a busybox pod with command sleep 3600
<details><summary>show</summary>
<p>

```bash
kubectl run busybox --image=busybox --restart=Never -- /bin/sh -c "sleep 3600"
```

</p>
</details>

#### 22. Check the connection of the nginx pod from the busybox pod
<details><summary>show</summary>
<p>

```bash
kubectl get po nginx -o wide
// check the connection
kubectl exec -it busybox -- wget -o- <IP Address>
```

</p>
</details>

#### 23. Create a busybox pod and echo message ‘How are you’ and delete it manually
<details><summary>show</summary>

```bash
kubectl run busybox --image=nginx --restart=Never -it -- echo "How are you"
kubectl delete po busybox
```

</p>
</details>

#### 24. Create a busybox pod and echo message ‘How are you’ and have it deleted immediately
<details><summary>show</summary>

```bash
// notice the --rm flag
kubectl run busybox --image=nginx --restart=Never -it --rm -- echo "How are you"
```

</p>
</details>

#### 25. Create an nginx pod and list the pod with different levels of verbosity
<details><summary>show</summary>

```bash
// create a pod
kubectl run nginx --image=nginx --restart=Never --port=80
// List the pod with different verbosity
kubectl get po nginx --v=7
kubectl get po nginx --v=8
kubectl get po nginx --v=9
```

</p>
</details>

#### 26. List the nginx pod with custom columns POD_NAME and POD_STATUS
<details><summary>show</summary>

```bash
kubectl get po -o=custom-columns="POD_NAME:.metadata.name, POD_STATUS:.status.containerStatuses[].state"
```

</p>
</details>

#### 27. List all the pods sorted by name
<details><summary>show</summary>

```bash
kubectl get pods --sort-by=.metadata.name
```

</p>
</details>

#### 28. List all the pods sorted by created timestamp
<details><summary>show</summary>

```bash
kubectl get pods--sort-by=.metadata.creationTimestamp
```

</p>
</details>
