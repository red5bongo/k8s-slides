## Replication controllers

---

A replication controller (RC) is a supervisor for long-running pods.
An RC will launch a specified number of pods called `replicas` and makes
sure that they keep running, for example when a node fails or something
inside of a pod, that is, in one of its containers goes wrong.

---

Let's create an [RC](src/rcs/rc.yaml)
that supervises a single replica of a pod:

+++?code=src/rcs/rc.yaml

+++

```bash
$ kubectl create -f src/rcs/rc.yaml
```

+++

You can see the RC and the pod it looks after like so:

```bash
$ kubectl get rc
NAME                DESIRED   CURRENT   READY     AGE
rcex                1         1         1         3m

$ kubectl get pods --show-labels
NAME           READY     STATUS    RESTARTS   AGE    LABELS
rcex-qrv8j     1/1       Running   0          4m     app=sise
```

+++

To scale up, that is, to increase the number of replicas, do:

```bash
$ kubectl scale --replicas=3 rc/rcex

$ kubectl get pods -l app=sise
NAME         READY     STATUS    RESTARTS   AGE
rcex-1rh9r   1/1       Running   0          54s
rcex-lv6xv   1/1       Running   0          54s
rcex-qrv8j   1/1       Running   0          10m

```

---

### Clean Up

Finally, to get rid of the RC and the pods it is supervising, use:

```bash
$ kubectl delete rc rcex
replicationcontroller "rcex" deleted
```

---

Note that, going forward, the RCs are called [replica sets](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) (RS), supporting set-based selectors. The RS are already in use in the context of [deployments](/deployments/).


---

### Exercises 

1. Redeploy rcex replication controller.
1. Deploy a new pod with label `app=sise`
1. Observe how number of pods managed by replication controller has changed.
