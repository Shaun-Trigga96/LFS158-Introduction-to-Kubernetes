# Kubernetes Architecture

## Chapter Overview

In this chapter, we will explore the `Kubernetes` architecture, the components of a `control plane node`, the role of the `worker nodes`, the `cluster state management` with `etcd` and the network setup requirements. We will also learn about the `Container Network Interface (CNI)`, as Kubernetes' network specification.

## Learning Objectives

By the end of this chapter, you should be able to:

- Discuss the Kubernetes architecture.
- Explain the different components of the control plane and worker nodes.
- Discuss cluster state management with etcd.
- Review the Kubernetes network setup requirements.

## Kubernetes Architecture

At a very high level, Kuber`netes is a`cluster` of compute systems categorized by their distinct roles:

- One or more control plane nodes
- One or more worker nodes (optional, but recommended).

![Components of the Kubernetes Cluster](ComponentsoftheKubernetesCluster.png)

## Control Plane Node Overview

The `control plane node` provides a running environment for the `control plane agents` responsible for managing the state of a `Kubernetes cluster`, and it is the brain behind all operations inside the `cluster`. The `control plane components` are agents with very distinct roles in the cluster's management. In order to communicate with the Kubernetes cluster, users send requests to the `control plane` via a `Command Line Interface (CLI)` tool, a `Web User-Interface (Web UI) Dashboard`, or an `Application Programming Interface (API)`.

It is important to keep the control plane running at all costs. Losing the control plane may introduce downtime, causing service disruption to clients, with possible loss of business. To ensure the control plane's fault tolerance, control plane `node replicas` can be added to the cluster, configured in `High-Availability (HA)` mode. While only one of the control plane nodes is dedicated to actively managing the cluster, the control plane components stay in sync across the control plane node replicas. This type of configuration adds resiliency to the cluster's control plane, should the active control plane node fail.

To persist the Kubernetes `cluster's state`, all cluster configuration data is saved to a `distributed key-value store` which only holds cluster state related data, no client workload generated data. The `key-value store` may be configured on the control plane node (stacked topology), or on its dedicated host (external topology) to help reduce the chances of data store loss by decoupling it from the other control plane agents.

In the stacked key-value store topology, HA control plane node replicas ensure the key-value store's resiliency as well. However, that is not the case with `external key-value store topology`, where the dedicated key-value store hosts have to be separately replicated for HA, a configuration that introduces the need for additional hardware, hence additional operational costs.

## Control Plane Node Components

A control plane node runs the following essential control plane components and agents: `API server`, `scheduler`, `controller managers`, and`key-value data store`.

In addition, the control plane node runs: `container runtime`, `node agent (kubelet)`, `proxy` (`kube-proxy`), optional add-ons for `observability`, such as `dashboard`, `cluster-level monitoring`, and `logging`.

## Components

### API Server

All the administrative tasks are coordinated by the `kube-apiserver`, a central control plane component running on the control plane node. The API Server intercepts RESTful calls from users, administrators, developers, operators and external agents, then validates and processes them. During processing the API Server reads the Kubernetes cluster's current state from the key-value store, and after a call's execution, the resulting state of the Kubernetes cluster is saved in the key-value store for persistence. The API Server is the only control plane component to talk to the key-value store, both to read from and to save Kubernetes cluster state information - acting as a middle interface for any other control plane agent inquiring about the cluster's state.

The API Server is highly configurable and customizable. It can scale horizontally, but it also supports the addition of custom secondary API Servers, a configuration that transforms the primary API Server into a proxy to all secondary, custom API Servers, routing all incoming RESTful calls to them based on custom defined rules.

### Scheduler

The role of the `kube-scheduler` is to assign new workload objects, such as pods encapsulating containers, to nodes - typically worker nodes. During the scheduling process, decisions are made based on current Kubernetes cluster state and new workload object's requirements. The scheduler obtains from the key-value store, via the API Server, resource usage data for each worker node in the cluster. The scheduler also receives from the API Server the new workload object's requirements which are part of its configuration data.

Requirements may include constraints that users and operators set, such as scheduling work on a node labeled with `disk==ssd` key-value pair. The scheduler also takes into account `Quality of Service (QoS)` requirements, data locality, affinity, anti-affinity, taints, toleration, cluster topology, etc. Once all the cluster data is available, the scheduling algorithm filters the nodes with predicates to isolate the possible node candidates which then are scored with priorities in order to select the one node that satisfies all the requirements for hosting the new workload. The outcome of the decision process is communicated back to the API Server, which then delegates the workload deployment with other control plane agents.

The scheduler is highly configurable and customizable through `scheduling policies`, `plugins`, and `profiles`. Additional custom schedulers are also supported, then the object's configuration data should include the name of the custom scheduler expected to make the scheduling decision for that particular object; if no such data is included, the default scheduler is selected instead.

A scheduler is extremely important and complex in a `multi-node Kubernetes cluster`, while in a single-node Kubernetes cluster possibly used for learning and development purposes, the scheduler's job is quite simple.

### Controller Managers

The `controller managers` are components of the control plane node running controllers or operator processes to regulate the state of the Kubernetes cluster. `Controllers` are watch-loop processes continuously running and comparing the cluster's desired state (provided by objects' configuration data) with its current state (obtained from the key-value store via the API Server). In case of a mismatch, corrective action is taken in the cluster until its current state matches the desired state.

#### kube-controller-manager

The `kube-controller-manager` runs controllers or operators responsible to act when nodes become unavailable, to ensure container pod counts are as expected, to create endpoints, service accounts, and API access tokens.

#### cloud-controller-manager

The `cloud-controller-manager` runs controllers or operators responsible to interact with the underlying infrastructure of a cloud provider when nodes become unavailable, to manage storage volumes when provided by a cloud service, and to manage load balancing and routing.

### Key-Value Data Store (etcd)

[`etcd`](https://etcd.io/) is an open source project under the [Cloud Native Computing Foundation (CNCF)](https://www.cncf.io/). `etcd` is a strongly consistent, distributed key-value data store used to persist a Kubernetes cluster's state. New data is written to the data store only by appending to it, data is never replaced in the data store. Obsolete data is compacted (or shredded) periodically to minimize the size of the data store.

Out of all the control plane components, only the API Server is able to communicate with the etcd data store.

etcd's CLI management tool - `etcdctl`, provides snapshot save and restore capabilities which come in handy especially for a single etcd instance Kubernetes cluster - common in Development and learning environments. However, in Stage and Production environments, it is extremely important to replicate the data stores in HA mode, for cluster configuration data resiliency.

## etcd Deployment Topologies

Some Kubernetes cluster bootstrapping tools, such as `kubeadm`, by default, provision stacked etcd control plane nodes, where the data store runs alongside and shares resources with the other control plane components on the same control plane node.

### Stacked etcd Topology

![Stacked etcd Topology](StackedetcdTopology.png)

*In stacked topology, etcd runs on the same nodes as other control plane components.*

### External etcd Topology

For data store isolation from the control plane components, the bootstrapping process can be configured for an external etcd topology, where the data store is provisioned on a dedicated separate host, thus reducing the chances of an etcd failure.

![External etcd Topology](ExternaletcdTopology.png)

*In external topology, etcd runs on dedicated separate hosts.*

## etcd High Availability

Both stacked and external etcd topologies support HA configurations. etcd is based on the [`Raft Consensus Algorithm`](https://raft.github.io/) which allows a collection of machines to work as a coherent group that can survive the failures of some of its members. At any given time, one of the nodes in the group will be the leader, and the rest of them will be the followers. etcd gracefully handles leader elections and can tolerate node failure, including leader node failures. Any node can be treated as a leader.

Keep in mind however, that the leader/followers hierarchy is distinct from the primary/secondary hierarchy, meaning that neither node is favored for the leader role, and neither node outranks other nodes. A leader will remain active until it fails, at which point in time a new leader is elected by the group of healthy followers.

![Leader and Followers](LeaderandFollowers.png)

*etcd uses leader-follower architecture for high availability.*

## Additional Information

`etcd` is written in the `Go` programming language. In Kubernetes, besides storing the cluster state, `etcd` is also used to store configuration details such as `subnets`, `ConfigMaps`, `Secrets`, etc.

## Worker Node Overview

A `worker node` provides a running environment for client applications. These applications are microservices running as application containers. In Kubernetes the application containers are encapsulated in `Pods`, controlled by the cluster control plane agents running on the control plane node. `Pods` are scheduled on worker nodes, where they find required compute, memory and storage resources to run, and networking to talk to each other and the outside world. A `Pod` is the smallest scheduling work unit in Kubernetes. It is a logical collection of one or more containers scheduled together, and the collection can be started, stopped, or rescheduled as a single unit of work.

Also, in a multi-worker Kubernetes cluster, the network traffic between client users and the containerized applications deployed in Pods is handled directly by the worker nodes, and is not routed through the control plane node.

## Worker Node Components

A `worker node` has the following components: `container runtime`, `node agent (kubelet)`, `kubelet CRI shims`, `proxy (kube-proxy)`, `add-ons` (for `DNS`, observability components such as `dashboards`, `cluster-level monitoring` and `logging`, and `device plugins`).

## Components

### Container Runtime

Although Kubernetes is described as a `"container orchestration engine"`, it lacks the capability to directly handle and run containers. In order to manage a container's lifecycle, Kubernetes requires a container runtime on the node where a Pod and its containers are to be scheduled. A runtime is required on each node of a Kubernetes cluster, both control plane and worker. The [recommendation](https://kubernetes.io/docs/setup/) is to run the Kubernetes control plane components as containers, hence the necessity of a runtime on the control plane nodes.

Kubernetes supports several container runtimes:

#### [CRI-O](https://cri-o.io/)

A lightweight container runtime for Kubernetes, supporting [quay.io](https://quay.io/) and [Docker Hub](https://hub.docker.com/) image registries.

#### [containerd](https://containerd.io/)

A simple, robust, and portable container runtime.

#### [Docker Engine](https://www.docker.com/)

A popular and complex container platform which uses `containerd` as a container runtime.

#### [Mirantis Container Runtime](https://www.mirantis.com/software/container-runtime/)

Formerly known as the Docker Enterprise Edition.

### Node Agent - kubelet

The `kubelet` is an agent running on each node, control plane and workers, and it communicates with the control plane. It receives Pod definitions, primarily from the API Server, and interacts with the container runtime on the node to run containers associated with the Pod. It also monitors the health and resources of Pods running containers.

The `kubelet` connects to container runtimes through a plugin based interface - the [Container Runtime Interface](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-node/container-runtime-interface.md) (`CRI`). The `CRI` consists of `protocol buffers`, `gRPC API`, `libraries`, and additional specifications and tools. In order to connect to interchangeable container runtimes, `kubelet` uses a `CRI shim`, an application which provides a clear abstraction layer between `kubelet` and the container runtime.

![Container Runtime Interface](ContainerRuntimeInterface.png)

*Container Runtime Interface (Retrieved from [blog.kubernetes.io](http://blog.kubernetes.io/2016/12/container-runtime-interface-cri-in-kubernetes.html))*

As shown above, the `kubelet` acting as grpc client connects to the CRI shim acting as `grpc server` to perform container and image operations. The `CRI` implements two services: `ImageService` and `RuntimeService`. The `ImageService` is responsible for all the image-related operations, while the `RuntimeService` is responsible for all the Pod and container-related operations.

### kubelet - CRI Shims

Originally the kubelet agent supported only a couple of container runtimes, first the Docker Engine followed by rkt, through a unique interface model integrated directly in the kubelet source code. However, this approach was not intended to last forever even though it was especially beneficial for Docker. In time, Kubernetes started migrating towards a standardized approach to container runtime integration by introducing the CRI. Kubernetes adopted a decoupled and flexible method to integrate with various container runtimes without the need to recompile its source code. Any container runtime that implements the CRI could be used by Kubernetes to manage containers.

Shims are Container Runtime Interface (CRI) implementations, interfaces or adapters, specific to each container runtime supported by Kubernetes. Below we present some examples of CRI shims:

#### cri-containerd

`cri-containerd` allows containers to be directly created and managed with containerd at kubelet's request:

![cri-containerd](cri-containerd.png)

*cri-containerd (Retrieved from [blog.kubernetes.io](http://blog.kubernetes.io/2017/11/containerd-container-runtime-options-kubernetes.html))*

#### CRI-O

`CRI-O` enables the use of any Open Container Initiative (OCI) compatible runtime with Kubernetes, such as runC:

![CRI-O](CRI-O.png)

*CRI-O (Retrieved from [cri-o.io](http://cri-o.io/))*

#### dockershim and cri-dockerd

Before Kubernetes release v1.24 the `dockershim` allowed containers to be created and managed by invoking the Docker Engine and its internal runtime containerd. Due to Docker Engine's popularity, this shim has been the default interface used by `kubelet`. However, starting with Kubernetes release v1.24, the `dockershim` is no longer being maintained by the Kubernetes project, its specific code is removed from `kubelet` source code, thus will no longer be supported by the `kubelet` node agent of Kubernetes.

As a result, Docker, Inc., and Mirantis have agreed to introduce and maintain a replacement adapter, `cri-dockerd` that would ensure that the Docker Engine will continue to be a container runtime option for Kubernetes, in addition to the Mirantis Container Runtime (MCR). The introduction of cri-dockerd also ensures that both Docker Engine and MCR follow the same standardized integration method as the CRI-compatible runtimes.

![dockershim to cri-dockerd](dockershimtocri-dockerd.png)

*dockershim to cri-dockerd (Retrieved from [GitHub](https://github.com/kubernetes/website/issues/30974#issuecomment-1030264920))*

> `Additional Information:` Additional details about the deprecation process of the dockershim can be found on the [Updated: Dockershim Removal FAQ page](https://kubernetes.io/blog/2022/02/17/dockershim-faq/).

### Proxy - kube-proxy

The `kube-proxy` is the network agent which runs on each node, control plane and workers, responsible for dynamic updates and maintenance of all networking rules on the node. It abstracts the details of Pods networking and forwards connection requests to the containers in the Pods.

The kube-proxy is responsible for TCP, UDP, and SCTP stream forwarding or random forwarding across a set of Pod backends of an application, and it implements forwarding rules defined by users through Service API objects.

The kube-proxy node agent operates in conjunction with the `iptables` of the node. Iptables is a firewall utility created for the Linux OS that can be managed by users through a CLI utility of the same name. The iptables utility is available for and pre-installed on many Linux distributions.

### Add-ons

Add-ons are cluster features and functionality not yet available in Kubernetes, therefore implemented through 3rd-party plugins and services.

#### DNS

`Cluster DNS` is a DNS server required to assign DNS records to Kubernetes objects and resources.

#### Dashboard

A general purpose web-based user interface for cluster management.

#### Monitoring

Collects cluster-level container metrics and saves them to a central data store.

#### Logging

Collects cluster-level container logs and saves them to a central log store for analysis.

#### Device Plugins

For system hardware resources, such as GPU, FPGA, high-performance NIC, to be advertised by the node to application pods.

## Kubernetes Networking Challenges

`Decoupled microservices-based` applications rely heavily on networking to mimic the tight-coupling once available in the monolithic era. Networking, in general, is not the easiest to understand and implement. Kubernetes is no exception - as a containerized microservices orchestrator, it needs to address several distinct networking challenges:

## Core Networking Challenges

1. `Container-to-Container communication inside Pods`
2. `Pod-to-Pod communication on the same node and across cluster nodes`
3. `Service-to-Pod communication within the same namespace and across cluster namespaces`
4. `External-to-Service communication for clients to access applications in a cluster`

All these networking challenges must be addressed by a Kubernetes cluster and its plugins.

## Container-to-Container Communication Inside Pods

Making use of the underlying host operating system's kernel virtualization features, a container runtime creates an isolated network space for each container it starts. On Linux, this isolated network space is referred to as a `network namespace`. A network namespace can be shared across containers, or with the host operating system.

When a grouping of containers defined by a Pod is started, a special infrastructure `Pause container` is initialized by the Container Runtime for the sole purpose of creating a network namespace for the Pod. All additional containers, created through user requests, running inside the Pod will share the Pause container's network namespace so that they can all talk to each other via localhost.

## Pod-to-Pod Communication Across Nodes

In a Kubernetes cluster, Pods (groups of containers) are scheduled on nodes in a nearly unpredictable fashion. Regardless of their host node, Pods are expected to be able to communicate with all other Pods in the cluster, all without the implementation of `Network Address Translation (NAT)`. This is a fundamental requirement of any networking implementation in Kubernetes.

The Kubernetes network model aims to reduce complexity, and it treats Pods as VMs on a network, where each VM is equipped with a network interface - thus each Pod receiving a unique IP address. This model is called `"IP-per-Pod"` and ensures Pod-to-Pod communication, just as VMs are able to communicate with each other on the same network.

### Container Network Interface (CNI)

Containers share the Pod's network namespace and must coordinate port assignment inside the Pod just as applications would on a VM, all while being able to communicate with each other on `"localhost"` - inside the Pod. However, containers are integrated with the overall Kubernetes networking model through the use of the [Container Network Interface (CNI)](https://www.cni.dev/) supported by [CNI plugins](https://github.com/containernetworking/cni#3rd-party-plugins).

`CNI` is a set of specifications and libraries which allow plugins to configure the networking for containers. While there are a few [core plugins](https://github.com/containernetworking/plugins#plugins), most CNI plugins are 3rd-party Software Defined Networking (SDN) solutions implementing the Kubernetes networking model.

In addition to addressing the fundamental requirement of the networking model, some networking solutions offer support for Network Policies. Popular SDN solutions include:

- [Flannel](https://github.com/coreos/flannel/)
- [Weave](https://www.weave.works/oss/net/)
- [Calico](https://www.tigera.io/project-calico/)
- [Cilium](https://cilium.io/)

![Container Network Interface (CNI) Core Plugins](ContainerNetworkInterface(CNI)CorePlugins.png)

### How CNI Works

The container runtime offloads the IP assignment to CNI, which connects to the underlying configured plugin, such as Bridge or MACvlan, to get the IP address. Once the IP address is given by the respective plugin, CNI forwards it back to the requested container runtime.

## External-to-Pod Communication

A successfully deployed containerized application running in Pods inside a Kubernetes cluster may require accessibility from the outside world. Kubernetes enables external accessibility through `Services`, which are complex encapsulations of network routing rule definitions stored in iptables on cluster nodes and implemented by kube-proxy agents.

By exposing services to the external world with the aid of kube-proxy, applications become accessible from outside the cluster over a virtual IP address and a dedicated port number.

## Additional Resources

For more details, you can explore the [Kubernetes documentation](https://kubernetes.io/docs/concepts/cluster-administration/networking/).
