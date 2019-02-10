# Kubernetes Secrete for Private Repository 
#Kubernetes

We will review `Kubernete` secret for private repositories. First we will have to make sure our private docker repository is up. Then we will have to login 
```
docker login my-registry-url:5000
```


This command will create `~/.docker/config.json`  and store all information there. We will need to encryption  all data for our secret.  First let's review how exactly works encryption. To understand run the command 
```
[root@nexus ~]# echo -n 'Hello World'| base64
SGVsbG8gV29ybGQ=
```


As we can see we just crated encryption for our message `Hello World`. Let's encrypt our `~/.docker/config.json` data and crate secrete 

``` 
cat ~/.docker/config.json | base64
```


Copy out put and put inside `mysecret.yaml`. in end file should like this 
``` mysecret.yaml
apiVersion: v1
kind: Secret
metadata:
 name: registrypullsecret
data:
 .dockerconfigjson: <base-64-encoded-json-here>
type: kubernetes.io/dockerconfigjson
```

To create secrete run 
```
kubectl create -f mysecret.yaml && kubectl get secrets registrypullsecret
```

Now we can try to pull and use our private image from our private docker repository.
``` mypod.yaml
apiVersion: v1
kind: Pod
metadata:
 name: jss
spec:
 containers:
 — name: jss
 image: my-registry-url:5000/my-cool-image:0.1
 imagePullSecrets:
 — name: registrypullsecret
``` 

After then we create our file we can deploy to the cluster our pod with following command.
```
kubectl create -f  mypod.yaml && kubectl get pods 
```

