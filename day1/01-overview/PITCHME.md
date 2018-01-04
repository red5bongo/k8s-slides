## Kubernetes Overview

---

### Features

- automatic container scheduling
- horizontal scaling
- automated rollouts and rollbacks

Note:

1. Automatically places containers based on their resource requirements and other constraints, while not sacrificing availability. Mix critical and best-effort workloads in order to drive up utilization and save even more resources.

2. Scale your application up and down with a simple command, with a UI, or automatically based on CPU usage.

3. Kubernetes progressively rolls out changes to your application or its configuration, while monitoring application health to ensure it doesn't kill all your instances at the same time. If something goes wrong, Kubernetes will rollback the change for you. Take advantage of a growing ecosystem of deployment solutions.

+++

- storage orchestration
- self-healing
- service discovery and load balancing
- secret and configuration management
- batch execution

Note:

1. Automatically mount the storage system of your choice, whether from local storage, a public cloud provider such as GCP or AWS, or a network storage system such as NFS, iSCSI, Gluster, Ceph, Cinder, or Flocker.

2. Automatically mount the storage system of your choice, whether from local storage, a public cloud provider such as GCP or AWS, or a network storage system such as NFS, iSCSI, Gluster, Ceph, Cinder, or Flocker.

3. No need to modify your application to use an unfamiliar service discovery mechanism. Kubernetes gives containers their own IP addresses and a single DNS name for a set of containers, and can load-balance across them.

4. Deploy and update secrets and application configuration without rebuilding your image and without exposing secrets in your stack configuration.

5. In addition to services, Kubernetes can manage your batch and CI workloads, replacing containers that fail, if desired.

---

### What Kuberntes is not?

- does not limit the types of applications supported
- does not dictate application frameworks (e.g., Wildfly)
- restrict the set of supported language runtimes (for example, Java, Python, Ruby)
- cater to only 12-factor applications

+++

Does not provide as built-in services

- middleware (e.g., message buses), 
- data-processing frameworks (for example, Spark), 
- databases (e.g., mysql), 
- nor cluster storage systems (e.g., Ceph) 

+++

no a click-to-deploy service marketplace

+++

Does not deploy source code and does not build your application

+++

Allows users to choose their logging, monitoring, and alerting systems

+++

Does not provide nor mandate a comprehensive application configuration language/system

+++

Does not provide nor adopt any comprehensive machine configuration, maintenance, management, or self-healing systems

---

### Use Cases

- review lab: deploy app version on pull request
- CI/CD: run builds, unit and integration tests
- analisys: batch task to generate report based on data

+++

- stateless microservices deployment
- statefull service deployment

Note: statefull apps are harder to orchestrate and this use case is still rare.
Databases and ESB are running on virtual or physical machines while stateless
apps run inside the containers on top of the kubernetes.

---

### Comparison to other container orchestration solutions

---

### Cloud Foundry

- runs 12-factor apps
- has a services marketplace
- can deploy source code
- manage virtual machines with BOSH
- user management

+++

- has no secret and config management
- does not allow persistent storage in the containers
- does not have batch execution
- no deployment rollbacks

---

### Mesosphere

- can run stateful services
- supports batch job execution using Chronos component
- service discovery and load balancing
- data services catalog

+++

- no rolling deployments
- does not deploy source code

---

### Docker Swarm

- has secret and config management
- runs any app that can be put inside the Docker container
- load balancing and service discovery

+++

- does not have batch execution
- no data services catalog
- no persistent volume management across the nodes

---

### Kubernetes architecture

![](https://s3.eu-central-1.amazonaws.com/altoros-public-images/k8s-nodes.png)

+++

### Master node

- schedules containers to worker nodes
- checks health of containers
- hosts cluster API

+++

### Worker node

- run applications in containers
- perform inner load balancing

---

### Components

+++

![](https://s3.eu-central-1.amazonaws.com/altoros-public-images/k8s-components.png)

+++

- <span style='color: red'>kubelet</span> is node agent that runs pods
- <span style='color: red'>kube-proxy</span> is running on each worker node and provides service load balancing
- <span style='color: red'>kube-dns</span>  provides service discovery for pods

+++

- <span style='color: red'>etcd</span> is backing data store for Kubernetes
- <span style='color: red'>Kubernetes dashboard</span>  is UI for cluster operations
- <span style='color: red'>Controller</span>  abstracts interactions with cloud provider
- <span style='color: red'>Scheduler</span>  selects node for new pods

