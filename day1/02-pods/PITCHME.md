## Pods

---

A pod is a collection of containers sharing a network and mount namespace
and is the basic unit of deployment in Kubernetes. All containers in a pod
are scheduled on the same node.

---

### Launch a pod using the container image

```bash
$ kubectl run sise --image=mhausenblas/simpleservice:0.5.0 --port=9876
```

+++

We can now see that the pod is running:

```bash
$ kubectl get pods
NAME                      READY     STATUS    RESTARTS   AGE
sise-3210265840-k705b     1/1       Running   0          1m

$ kubectl describe pod sise-3210265840-k705b | grep IP:
IP:                     172.17.0.3
```

+++

From within the cluster this pod is accessible via the pod IP `172.17.0.3`,
which we've learned from the `kubectl describe` command above:

+++

```bash
[cluster] $ curl 172.17.0.3:9876/info
{"host": "172.17.0.3:9876", "version": "0.5.0", "from": "172.17.0.1"}
```

+++

Note that `kubectl run` creates a deployment, so in order to
get rid of the pod you have to execute `kubectl delete deployment sise`.

---

### Launch a pod using configuration file

You can also create a pod from a configuration file

+++?code=src/pods/pod.yaml

+++

```bash
$ kubectl create -f src/pods/pod.yaml

$ kubectl get pods
NAME                      READY     STATUS    RESTARTS   AGE
twocontainers             2/2       Running   0          7s
```

+++

Now we can exec into the `CentOS` container and access the `simpleservice`
on localhost:

```bash
$ kubectl exec twocontainers -c shell -i -t -- bash
[root@twocontainers /]# curl -s localhost:9876/info
{"host": "localhost:9876", "version": "0.5.0", "from": "127.0.0.1"}
```

+++

Specify the `resources` field in the pod to influence how much CPU and/or RAM a
container in a [pod](src/pods/constraint-pod.yaml)
can use (here: `64MB` of RAM and `0.5` CPUs):

+++?code=src/pods/constraint-pod.yaml

+++

```bash
$ kubectl create -f src/pods/constraint-pod.yaml

$ kubectl describe pod constraintpod
...
Containers:
  sise:
    ...
    Limits:
      cpu:      500m
      memory:   64Mi
    Requests:
      cpu:      500m
      memory:   64Mi
...
```

---

### Clean Up

To remove all the pods created, just run:

```bash
$ kubectl delete pod twocontainers

$ kubectl delete pod constraintpod
```

---

### Exercises 

1. Create a configuration file that can deploy [nginx](https://hub.docker.com/_/nginx/) docker image as a pod.   
1. Make deployment.
1. Access kubernetes host using minikube ssh command.
1. Check that nginx is running and listens on `80` port. You can use `curl --head` command to do this.
