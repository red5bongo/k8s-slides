## Labels

---

Labels are the mechanism you use to organize Kubernetes objects. A label is a key-value
pair with certain [restrictions](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set)
concerning length and allowed values but without any pre-defined meaning.

---

Let's create a [pod](src/labels/pod.yaml) that initially has one label (`env=development`)

+++?code=src/labels/pod.yaml

+++

```bash
$ kubectl create -f src/labels/pod.yaml

$ kubectl get pods --show-labels
NAME       READY     STATUS    RESTARTS   AGE    LABELS
labelex    1/1       Running   0          10m    env=development
```

+++

In above `get pods` command note the `--show-labels` option that output the
labels of an object in an additional column.

+++

You can add a label to the pod as:

```bash
$ kubectl label pods labelex owner=michael

$ kubectl get pods --show-labels
NAME        READY     STATUS    RESTARTS   AGE    LABELS
labelex     1/1       Running   0          16m    env=development,owner=michael
```

+++

To use a label for filtering, for example to list only pods that have an
`owner` that equals `michael`, use the `--selector` option:

```bash
$ kubectl get pods --selector owner=michael
NAME      READY     STATUS    RESTARTS   AGE
labelex   1/1       Running   0          27m
```

+++

The `--selector` option can be abbreviated to `-l`, so to select pods that are
labelled with `env=development`, do:

```bash
$ kubectl get pods -l env=development
NAME      READY     STATUS    RESTARTS   AGE
labelex   1/1       Running   0          27m
```

---

Let's launch [another pod](src/labels/anotherpod.yaml)
that has two labels (`env=production` and `owner=michael`):

+++?code=src/labels/anotherpod.yaml

+++

```bash
$ kubectl create -f src/labels/anotherpod.yaml
```

+++

Now, let's list all pods that are either labelled with `env=development` or with
`env=production`:

```bash
$ kubectl get pods -l 'env in (production, development)'
NAME           READY     STATUS    RESTARTS   AGE
labelex        1/1       Running   0          43m
labelexother   1/1       Running   0          3m
```

+++

Other verbs also support label selection, for example, you could
remove both of these pods with:

```bash
$ kubectl delete pods -l 'env in (production, development)'
```

+++

You can also delete them normally with:

```bash
$ kubectl delete pods labelex

$ kubectl delete pods labelexother
```

---

Note that labels are not restricted to pods. In fact you can apply them to
all sorts of objects, such as nodes or services.

---

### Exercises 

1. Deploy 3 pods with labels `version=1`, `version=2` and `version=3`.
1. Write a selector  that returns all pods with versions not equal to 3. Refer to the [documentation](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#label-selectors) to review selector syntax.
