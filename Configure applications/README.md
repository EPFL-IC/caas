## Prerequisites

0. Clone this repo
1. You can read the kubernetes, docker documentation to have a better overview 
- https://kubernetes.io/docs/tasks/configure-pod-container/configmap/
- https://docs.docker.com/compose/environment-variables/

## Using a ConfigMap
### Create a file ConfigMap
In this example max_connections is set to 500 in mysqld.cnf

```sh
$ vi mysql/cm-mysql.yaml 
kind: ConfigMap
apiVersion: v1
metadata:
  name: cm-mysql
data:
  mysqld.cnf: |-
    [mysqld]
    pid-file        = /var/run/mysqld/mysqld.pid
    socket          = /var/run/mysqld/mysqld.sock
    datadir         = /var/lib/mysql
    #log-error      = /var/log/mysql/error.log
    # By default we only accept connections from localhost
    #bind-address   = 127.0.0.1
    # Disabling symbolic-links is recommended to prevent assorted security risks
    symbolic-links=0
    max_connections = 500
```

## Using environment variables
### Set the variables in the container description
Create a user,password,database for an application and set the root password
```sh
$ vi mysql/pod-mysql.yaml 
apiVersion: v1
kind: Pod
metadata:
  name: pod-mysql
  labels:
    name: pod-mysql
spec:
  containers:
    - name: mysql
      image: mysql
      ports:
      - containerPort: 3306
      env:
      - name: MYSQL_ROOT_PASSWORD
        value: s8MpZntb4BDTKrD
      - name: MYSQL_DATABASE
        value: dbForMyApp
      - name: MYSQL_USER
        value: userForMyApp
      - name: MYSQL_PASSWORD
        value: passStrongMyApp
      volumeMounts:
      - name: config-volume
        mountPath: /etc/mysql/conf.d
  volumes:
  - name: config-volume
    configMap:
      name: cm-mysql
```
At the end of the file, config-volume is define to mount the configMap

Creation of the service (can be useful,not mandatory)
```sh
vi mysql/svc-mysql.yaml 
kind: Service
apiVersion: v1
metadata:
  name: svc-mysql
  labels:
    name: svc-mysql
spec:
  type: LoadBalancer
  ports:
    - port: 3306
      name: mysql
  selector:
    name: pod-mysql
```

## Create and check the configuration
```sh
$ kubectl create -f mysql/
configmap "cm-mysql" created
pod "pod-mysql" created
service "svc-mysql" created
```

Check the user creation
```sh
$ kubectl exec -it pod-mysql -- mysql -uroot -ps8MpZntb4BDTKrD <<< "use mysql; select Host,User from user;"
Unable to use a TTY - input is not a terminal or the right kind of file
mysql: [Warning] Using a password on the command line interface can be insecure.
Host	User
%	root
%	userForMyApp
localhost	mysql.sys
localhost	root
```

Check the user's password and the access to the database
```sh
$ kubectl exec -it pod-mysql -- mysql -u userForMyApp -ppassStrongMyApp <<< "show databases;"
Unable to use a TTY - input is not a terminal or the right kind of file
mysql: [Warning] Using a password on the command line interface can be insecure.
Database
information_schema
dbForMyApp
```

The value for max_connections is set correctly
```sh
$ kubectl exec -it pod-mysql -- mysql -u userForMyApp -ppassStrongMyApp <<< "select @@max_connections"
Unable to use a TTY - input is not a terminal or the right kind of file
mysql: [Warning] Using a password on the command line interface can be insecure.
@@max_connections
500
```
