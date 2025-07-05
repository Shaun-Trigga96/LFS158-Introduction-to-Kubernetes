# Kubernetes

## Chapter Overview

In this chapter, we describe `Kubernetes`, its features, and the reasons why you should use it. We will explore the evolution of `Kubernetes` from `Borg`, Google's very own distributed workload manager.

We will also learn about the C`loud Native Computing Foundation (CNCF)`, which currently hosts the `Kubernetes` project, along with other popular cloud native projects, such as `Argo`, `Cilium`, `Prometheus`, `Fluentd`, `etcd`, `CoreDNS`, `cri-o`, `containerd`, `Helm`, `Envoy`, `Istio`, and `Linkerd`, just to name a few.

## Learning Objectives

By the end of this chapter, you should be able to:

- Define Kubernetes.
- Explain the reasons for using Kubernetes.
- Discuss the features of Kubernetes.
- Discuss the evolution of Kubernetes from Borg.
- Explain the role of the Cloud Native Computing Foundation.

## What Is Kubernetes?

According to the [Kubernetes website](https://kubernetes.io/),

*"Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications"*.

`Kubernetes` comes from the Greek word `κυβερνήτης`, which means **helmsman* or *ship pilot*. With this analogy in mind, we can think of `Kubernetes` as the pilot on a ship of `containers`.

`Kubernetes` is also referred to as `k8s` (pronounced Kate's), as there are 8 characters between k and s.

`Kubernetes` is highly inspired by the` Google Borg system`, a container and workload orchestrator for its global operations, Google has been using for more than a decade. It is an open source project written in the `Go` language and licensed under the [`License, Version 2.0`](https://www.apache.org/licenses/LICENSE-2.0).

`Kubernetes` was started by `Google` and, with its v1.0 release in July 2015, `Google` donated it to the [`Cloud Native Computing Foundation (CNCF)`](https://www.cncf.io/), one of the largest sub-foundations of the[ Linux Foundation](https://www.linuxfoundation.org/).

New `Kubernetes` versions are released in 4 month cycles. The current stable version is 1.29` (as of December 2023).

## From Borg to Kubernetes

According to the abstract of Google's [Borg paper](https://research.google/pubs/pub43438/), published in 2015:

> *"Google's Borg system is a cluster manager that runs hundreds of thousands of jobs, from many thousands of different applications, across a number of clusters each with up to tens of thousands of machines".*

For more than a decade, Borg has been Google's secret, running its worldwide containerized workloads in production. Services we use from Google, such as Gmail, Drive, Maps, Docs, etc., are all serviced using Borg.

Among the initial authors of `Kubernetes` were Google employees who have used `Borg` and developed it in the past. They poured in their valuable knowledge and experience while designing Kubernetes. Several features/objects of `Kubernetes` that can be traced back to `Borg`, or to lessons learned from it, are:

- API servers
- Pods
- IP-per-Pod
- Services
- Labels

We will explore all of them, and more, in this course.

# Kubernetes Features

Kubernetes offers a very rich set of features for container orchestration. Some of its fully supported features are:

## Core Features

### Automatic Bin Packing

Kubernetes automatically schedules containers based on resource needs and constraints, to maximize utilization without sacrificing availability.

### Designed for Extensibility

A Kubernetes cluster can be extended with new custom features without modifying the upstream source code.

### Self-Healing

Kubernetes automatically replaces and reschedules containers from failed nodes. It terminates and then restarts containers that become unresponsive to health checks, based on existing rules/policy. It also prevents traffic from being routed to unresponsive containers.

### Horizontal Scaling

Kubernetes scales applications manually or automatically based on CPU or custom metrics utilization.

### Service Discovery and Load Balancing

Containers receive IP addresses from Kubernetes, while it assigns a single Domain Name System (DNS) name to a set of containers to aid in load-balancing requests across the containers of the set.

## Additional Features

### Automated Rollouts and Rollbacks

Kubernetes seamlessly rolls out and rolls back application updates and configuration changes, constantly monitoring the application's health to prevent any downtime.

### Secret and Configuration Management

Kubernetes manages sensitive data and configuration details for an application separately from the container image, in order to avoid a rebuild of the respective image. Secrets consist of sensitive/confidential information passed to the application without revealing the sensitive content to the stack configuration, like on GitHub.

### Storage Orchestration

Kubernetes automatically mounts software-defined storage (SDS) solutions to containers from local storage, external cloud providers, distributed storage, or network storage systems.

### Batch Execution

Kubernetes supports batch execution, long-running jobs, and replaces failed containers.

### IPv4/IPv6 Dual-Stack

Kubernetes supports both IPv4 and IPv6 addresses.

## Platform Integration

Kubernetes supports common Platform as a Service specific features such as application deployment, scaling, and load balancing, but allows users to integrate their desired monitoring, logging and alerting solutions through optional plugins.

There are many additional features currently in alpha or beta phase. They will add great value to any Kubernetes deployment once they become stable features. For example, support for role-based access control (RBAC) is stable only as of the Kubernetes 1.8 release, while cronjob support is stable only as of release 1.21.

# Why Use Kubernetes?

Another one of Kubernetes' strengths is **portability**. It can be deployed in many environments such as local or remote Virtual Machines, bare metal, or in public/private/hybrid/multi-cloud setups.

## Extensibility and Architecture

Kubernetes extensibility allows it to support and to be supported by many 3rd party open source tools which enhance Kubernetes' capabilities and provide a feature-rich experience to its users. Its architecture is modular and pluggable. Not only does it orchestrate modular, decoupled microservices type applications, but also its architecture follows decoupled microservices patterns. Kubernetes' functionality can be extended by writing:

- Custom resources
- Operators
- Custom APIs
- Scheduling rules
- Plugins

## Community Support

For a successful open source project, the community is as important as having great code. Kubernetes is supported by a thriving community across the world. It has more than 3,500 contributors, who, over time, have pushed over 120,000 commits. There are meet-up groups in different cities and countries which meet regularly to discuss Kubernetes and its ecosystem. 

The community is divided into **Special Interest Groups (SIGs)**, groups which focus on special topics, such as:

- Scaling
- Bare metal
- Networking
- Storage
- And more

We will learn more about them in our last chapter, *"Kubernetes Community"*.

# Kubernetes Users

In less than a decade since its debut, Kubernetes has become the platform of choice for many enterprises of various sizes to run their workloads. It is a solution for workload management in banking, education, finance and investments, gaming, information technology, media and streaming, online retail, ridesharing, telecommunications, nuclear research, and many other industries.

There are numerous user [case studies](https://kubernetes.io/case-studies/) and success stories on the [Kubernetes website](https://kubernetes.io/case-studies/), including:

## Notable Users

- [**BlackRock**](https://kubernetes.io/case-studies/blackrock/)
- [**Booking.com**](https://kubernetes.io/case-studies/booking-com/)
- [**Box**](https://kubernetes.io/case-studies/capital-one/)
- [**CapitalOne**](https://kubernetes.io/case-studies/capital-one/)
- [**Huawei**](https://kubernetes.io/case-studies/huawei/)
- [**IBM**](https://kubernetes.io/case-studies/ibm/)
- [**ING**](https://kubernetes.io/case-studies/ing/)
- [**Nokia**](https://kubernetes.io/case-studies/nokia/)
- [**OpenAI**](https://kubernetes.io/case-studies/openai/)
- [**Pearson**](https://kubernetes.io/case-studies/pearson/)
- [**Spotify**](https://kubernetes.io/case-studies/spotify/)
- [**Wikimedia**](https://kubernetes.io/case-studies/wikimedia/)
- **And many more**

## Cloud Native Computing Foundation (CNCF)

The [Cloud Native Computing Foundation ](https://www.cncf.io/)(CNCF) is one of the largest sub-projects hosted by the [Linux Foundation](https://www.linuxfoundation.org/). CNCF aims to accelerate the adoption of containers, microservices, and cloud native applications.

CNCF hosts a multitude of projects, with more to be added in the future. CNCF provides resources to each of the projects, but, at the same time, each project continues to operate independently under its pre-existing governance structure and with its existing maintainers. Projects within CNCF are categorized based on their maturity levels: **Sandbox**, **Incubating**, and **Graduated**. At the time of this writing, over a dozen projects had reached Graduated status with many more Incubating and in the Sandbox.

## CNCF Project Categories

### Graduated Projects
*Popular graduated projects (as of March 2024):*

- [**Kubernetes**](https://kubernetes.io/) - container orchestrator
- [**Argo** ](https://argoproj.github.io/)- workflow engine for Kubernetes
- [**etcd** ](https://etcd.io/)- distributed key-value store
- [**CoreDNS**](https://coredns.io/) - DNS server
- [**containerd**](http://containerd.io/) - container runtime
- [**CRI-O**](https://cri-o.io/) - container runtime
- [**Envoy**](https://www.envoyproxy.io/) - cloud native proxy
- [**Fluentd**](http://www.fluentd.org/) - for unified logging
- [**Flux** ](https://fluxcd.io/)- continuous delivery for Kubernetes
- [**Harbor**](https://goharbor.io/) - registry
- [**Helm**](https://www.helm.sh/) - package management for Kubernetes
- [**Linkerd**](https://linkerd.io/) - service mesh for Kubernetes
- [**Open Policy Agent**](https://www.openpolicyagent.org/) - policy engine
- [**Prometheus**](https://prometheus.io/) - monitoring system and time series DB
- [**Rook** ](https://rook.io/)- cloud native storage orchestrator for Kubernetes

### Incubating Projects
*Key incubating projects:*

- [**Buildpacks.io** ](https://buildpacks.io/)- builds application images
- [**Contour**](https://projectcontour.io/) - ingress controller for Kubernetes
- [**gRPC**](http://www.grpc.io/) - for remote procedure call (RPC)
- [**CNI**](https://www.cni.dev/) - for Linux containers networking
- [**Knative** ](https://knative.dev/)- serverless containers in Kubernetes
- [**KubeVirt**](https://kubevirt.io/) - Kubernetes based Virtual Machine manager
- [**Notary**](https://github.com/theupdateframework/notary) - for data security
- **And many [more](https://www.cncf.io/projects/)**

### Sandbox Projects

There are many dynamic projects in the CNCF [Sandbox](https://www.cncf.io/sandbox-projects/) geared towards metrics, monitoring, identity, scripting, serverless, nodeless, edge, expecting to achieve Incubating and possibly Graduated status. While many active projects are preparing for takeoff, others are being [archived](https://www.cncf.io/archived-projects/) once they become less active and no longer in demand. 

#### Archived Projects
The first projects to be archived include:

- [**rkt**](https://github.com/rkt/rkt) - container runtime
- [**OpenTracing**](https://www.cncf.io/projects/opentracing/) - distributed tracing
- [**Brigade**](https://brigade.sh/) - event driven automation tool
- [**Service Mesh Interface**](https://www.cncf.io/projects/service-mesh-interface-smi/) - service mesh specification

## CNCF Ecosystem Coverage

The projects under CNCF cover the entire lifecycle of a cloud native application, from its execution using container runtimes, to its monitoring and logging. This comprehensive coverage is very important to meet the goals of CNCF.

![CNCF Ecosystem](CNCF.png)

## CNCF and Kubernetes

For Kubernetes, the Cloud Native Computing Foundation provides essential support and governance:

## Key Services and Support

### Trademark and Legal Protection

- Provides a neutral home for the Kubernetes trademark and enforces proper usage
- Provides license scanning of core and vendor code
- Offers legal guidance on patent and copyright issues

### Education and Certification

- Creates and maintains open source learning [curriculum](https://github.com/cncf/curriculum), [training](https://www.cncf.io/training/courses/), and [certification](https://www.cncf.io/training/certification/) programs:
  - **KCNA** - Kubernetes and Cloud Native Associates
  - **CKA** - Kubernetes Administrators
  - **CKAD** - Kubernetes Application Developers
  - **CKS** - Kubernetes Security Specialists
  - **And more**

### Standards and Quality Assurance

- Manages a software conformance[ working group](https://lists.cncf.io/g/cncf-k8s-conformance)

### Marketing and Community

- Actively markets Kubernetes
- Supports ad hoc activities
- Sponsors conferences and meetup events