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
