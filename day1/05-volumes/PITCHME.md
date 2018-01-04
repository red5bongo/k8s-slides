## Volumes

---

A Kubernetes volume is essentially a directory accessible to all containers
running in a pod. In contrast to the container-local filesystem, the data in
volumes is preserved across container restarts.

+++

The medium backing a volume and its contents are determined
by the volume type:

+++

- node-local types such as `emptyDir` or `hostPath`
- file-sharing types such as `nfs`
- cloud provider-specific types like `awsElasticBlockStore`, `azureDisk`, or `gcePersistentDisk`
- distributed file system types, for example `glusterfs` or `cephfs`
- special-purpose types like `secret`, `gitRepo`

---

Let's create a [pod](src/volumes/pod.yaml)
with two containers that use an `emptyDir` volume to exchange data:

+++?code=src/volumes/pod.yaml

+++

```bash
$ kubectl create -f src/volumes/pod.yaml

$ kubectl describe pod sharevol
Name:                   sharevol
Namespace:              default
...
Volumes:
  xchange:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:
```

+++

We first exec into one of the containers in the pod, `c1`, check the volume mount
and generate some data:

```bash
$ kubectl exec sharevol -c c1 -i -t -- bash
[root@sharevol /]# mount | grep xchange
/dev/sda1 on /tmp/xchange type ext4 (rw,relatime,data=ordered)
[root@sharevol /]# echo 'some data' > /tmp/xchange/data
```

+++

When we now exec into `c2`, the second container running in the pod, we can see
the volume mounted at `/tmp/data` and are able to read the data created in the
previous step:

+++

```bash
$ kubectl exec sharevol -c c2 -i -t -- bash
[root@sharevol /]# mount | grep /tmp/data
/dev/sda1 on /tmp/data type ext4 (rw,relatime,data=ordered)
[root@sharevol /]# cat /tmp/data/data
some data
```

---

You can remove the pod with:

```bash
$ kubectl delete pod/sharevol
```

As already described, this will destroy the shared volume and all its contents.

---

### Exercises 

1. Create a pod that runs two containers: first one should run nginx second one sleep command.
1. Create a volume that mounts into `/usr/share/nginx/html` in nginx container and into `/tmp/html` in the second container.
1. Navigate into the second container and create [some html file](https://www.w3schools.com/html/tryit.asp?filename=tryhtml_basic_document) in `/tmp/html` folder.
1. Ensure that nginx now can return this document. 
