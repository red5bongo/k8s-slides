## Service Discovery

---

Service discovery is the process of figuring out how to connect to a service.
While there is a service discovery option based on [environment
variables](https://kubernetes.io/docs/concepts/services-networking/connect-applications-service/#environment-variables)
available, the DNS-based service discovery is preferable. 

+++
Note that DNS is a [cluster add-on](https://github.com/kubernetes/kubernetes/blob/master/cluster/addons/dns/README.md)
so make sure your Kubernetes distribution provides for one or install it
yourself.

---

Let's create a [service](src/sd/svc.yaml) named
`thesvc` and an [RC](src/sd/rc.yaml) supervising
some pods along with it:

+++?code=src/sd/svc.yaml

+++?code=src/sd/rc.yaml

+++

```bash
$ kubectl create -f src/sd/rc.yaml

$ kubectl create -f src/sd/svc.yaml
```

+++

Now we want to connect to the `thesvc` service from within the cluster, say, from another service.

+++

To simulate this, we create a [jump pod](src/sd/jumpod.yaml)
in the same namespace (`default`, since we didn't specify anything else):

+++?code=src/sd/jumpod.yaml

+++


```bash
$ kubectl create -f src/sd/jumpod.yaml
```

+++

The DNS add-on will make sure that our service `thesvc` is available via the FQDN
`thesvc.default.svc.cluster.local` from other pods in the cluster. Let's try it out:

+++

```bash
$ kubectl exec jumpod -c shell -i -t -- ping thesvc.default.svc.cluster.local
PING thesvc.reshifter.svc.cluster.local (172.30.251.137) 56(84) bytes of data.
...
```

+++

The answer to the `ping` tells us that the service is available via the cluster
IP `172.30.251.137`. We can directly connect to and consume the service (in the same namespace) like so:

 ```bash
 $ kubectl exec jumpod -c shell -i -t -- curl http://thesvc/info
{"host": "thesvc", "version": "0.5.0", "from": "172.17.0.5"}
```

+++

Note that the IP address `172.17.0.5` above is the cluster-internal IP address
of the jump pod.

---

To access a service that is deployed in a different namespace than the one you're
accessing it from, use a FQDN in the form `$SVC.$NAMESPACE.svc.cluster.local`.

+++

Let's see how that works by creating:

1. a [namespace](src/sd/other-ns.yaml) `other`
1. a [service](src/sd/other-svc.yaml) `thesvc` in namespace `other`
1. an [RC](src/sd/other-rc.yaml) supervising the pods, also in namespace `other`

+++?code=src/sd/other-ns.yaml

+++?code=src/sd/other-svc.yaml

+++?code=src/sd/other-rc.yaml

+++


```bash
$ kubectl create -f src/sd/other-ns.yaml

$ kubectl create -f src/sd/other-rc.yaml

$ kubectl create -f src/sd/other-svc.yaml
```

+++

We're now in the position to consume the service `thesvc` in namespace `other` from the
`default` namespace (again via the jump pod):

 ```bash
$ kubectl exec jumpod -c shell -i -t -- curl http://thesvc.other/info
{"host": "thesvc.other", "version": "0.5.0", "from": "172.17.0.5"}
```

---

Summing up, DNS-based service discovery provides a flexible and generic way to
connect to services across the cluster.

---

### Clean Up

You can destroy all the resources created with:

```bash
$ kubectl delete pods jumpod

$ kubectl delete svc thesvc

$ kubectl delete rc rcsise

$ kubectl delete ns other
```

---

### Exercises 

1. Create a pod deployment that consist of nginx container and jumppod contaner. 
1. Make sure that nginx is accessible from jumpod via a DNS name.  (You don't need to create service, you have to access pod by its own DNS name, for details how to do this please reference [official documentation](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pods))
