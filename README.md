# watchlist-operator

Demo source for article - https://medium.com/manikkothu/build-kubernetes-operator-using-kubebuilder-4bfef299757d 

### steps 
```
$ go mod init my-watchlist.io
$ kubebuilder init --domain demo.my-watchlist.io
```

```
$ kubebuilder create api --group webapp --kind MyWatchlist --version v1
Create Resource [y/n]
y
Create Controller [y/n]
y
```

```
$ kubebuilder create api --group webapp --kind Redis --version v1
Create Resource [y/n]
y
Create Controller [y/n]
y
```

```
# After create Frontend struc, to auto generate deepcopy methods

$ kubebuilder create api --group webapp --kind Frontend --version v1
Create Resource [y/n]
n
Create Controller [y/n]
n
```

```
$ make manifests
```

```
//start from here if you have genrated all the source and manifests files
$ kubectl create -f config/crd/bases/
$ kubectl create -f config/samples/webapp_v1_redis.yaml 
$ kubectl create -f config/samples/webapp_v1_mywatchlist.yaml 
```

```
$ make run
```

```
$ kubectl port-forward svc/mywatchlist-sample 7000:8080
```

#### get logs from side-car pod 
```
kubectl logs watchlist-operator-controller-manager-8549945dc7-f2qtb -n watchlist-operator-system manager

kubectl logs --follow pod/watchlist-operator-controller-manager-8549945dc7-zh67c -n watchlist-operator-system manager 

# note: manager is the container name of the sidecar, this is required in this case because there are two containers in the manager pod - manager and rbac-proxy
```

#### get all
```
kubectl get all -n watchlist-operator-system
```

#### clean-up
```
kubectl delete -f config/crd/bases/
kubectl delete -f config/samples/webapp_v1_redis.yaml 
kubectl delete -f config/samples/webapp_v1_mywatchlist.yaml 
```


### Deploy from docker repo

```
make deploy IMG=hmanikkothu/mywatchlist-operator:v1

kubectl create -f config/samples/webapp_v1_redis.yaml 
kubectl create -f config/samples/webapp_v1_mywatchlist.yaml 
[forward port]
kubectl port-forward svc/mywatchlist-sample -n default 7000:8080
```

#### clean-up
```
kubectl delete -f config/samples/webapp_v1_redis.yaml 
kubectl delete -f config/samples/webapp_v1_mywatchlist.yaml 

make undeploy IMG=<repo>/mywatchlist-operator:v1

```
#### notes
```
kubectl get redis mywatchlist -n default
kubectl get crd mywatchlists.webapp.demo.my-watchlist.io -o yaml --export

```
