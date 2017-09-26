## Beta-Kubernetes

### Step Zero: Prerequisites

0. Clone this repo
1. Request an account to the support (support-icit@epfl.ch)
2. If you plan to use a shared storage, request a volume  to the support
3. Install and Set Up kubectl - https://kubernetes.io/docs/tasks/tools/install-kubectl/
4. Check if you setup works correctly
```sh
$ kubectl get pods     
No resources found
```
### Step One: Create your first Pod (Container)
```sh
$ kubectl create -f ubuntu.yaml     
pod "ubuntu" created
```
Now list all pods running:
```sh
$ kubectl get pods
NAME      READY     STATUS    RESTARTS   AGE
ubuntu    1/1       Running   0          13s
```
You can log in to your pod 
```sh
$ kubectl exec -it ubuntu -- /bin/bash
root@ubuntu:/# exit
exit
```
### Step Two: Using a shared storage across Pods
0. Request a Persistent Volume (**PV**) to the support **(Only admins can create a PV)**, then you will be able to create a Persistent Volume Claims (**PVC**)

1. Edit the file pvc-lamp-project.yaml and replace the name with the value provided by the support, in this case it's with a NFS share

2. Create the PVC
```sh
$ kubectl create -f pvc-lamp-project.yaml 
persistentvolumeclaim "pvc-nfs-caperez-lamp" created
```
3. Create the pods
   
   3.1. Change the values for the name and the label in yaml files. You can also change settings like the root password with the environment variables in the yaml file for mysql pod
   
   3.2. Create a nginx pod
   ```sh
   $ kubectl create  -f pod-nginx.yaml
   pod "caperez-pod-nginx" created
   ``` 
   In this example, the sub folder "nginx" from the PVC will be mounted /usr/share/nginx/html  in the pod
   
   3.3. Create a mysql pod
   ```sh
   $ kubectl create -f pod-mysql.yaml
   pod "caperez-pod-mysql" created
   ```
   The sub folder "mysql" from the PVC will be mounted /var/lib/mysql in the pod
   
   3.4. Create a ubuntu pod to check the two folders
   ```sh
   $ kubectl create -f ubuntu-pvc.yaml
   Error from server (AlreadyExists): error when creating "ubuntu.yaml": object is being deleted: pods "ubuntu" already exists
   ```
   In this case, the ubuntu pod already exist. Suppress it, wait a while and try again.
   ```sh
   $ kubectl delete -f ubuntu.yaml
   pod "ubuntu" deleted
   .... 
   $ kubectl create -f pod-ubuntu-pvc.yaml
   pod "ubuntu" created
   ```
   
