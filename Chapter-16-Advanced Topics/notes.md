# Advanced Topics

## Chapter Overview

So far, in this course, we have spent most of our time understanding the basic Kubernetes concepts and simple workflows to build a solid foundation.

To support `enterprise-class` production workloads, Kubernetes also supports `multi-node pod controllers`, `stateful application controllers`, `batch controllers`, `auto-scaling`, `resource` and `quota management`, `package management`, `security contexts`, `network` and `security policies`, etc. In this chapter, we will briefly cover a limited number of such advanced topics, since diving into specifics would be out of scope for this course.

## Learning Objectives

By the end of this chapter, you should be able to:

Discuss advanced Kubernetes concepts: StatefulSets, Helm, Network Policy, Security Admission, Security Context, etc.

### Annotations

[Annotations](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/) in Kubernetes allow you to attach arbitrary, non-identifying metadata to objects in a key-value format. Unlike labels, annotations are not used for identifying or selecting objects. They serve a variety of purposes for storing information that is useful for humans or for automated tools, but not for direct Kubernetes object selection.

`Key characteristics and uses of Annotations:`

* `Non-identifying metadata:` They are purely for information and do not affect how Kubernetes schedules or runs your workloads.
* `Arbitrary data:` You can store various types of information, such as:
  * Build/release IDs, PR numbers, git branch names, etc.
  * Contact information for responsible personnel (phone/pager numbers).
  * Pointers to external systems like logging, monitoring, analytics, audit repositories, or debugging tools.
  * Specific configuration or state information for controllers (e.g., Ingress controller details).
  * Deployment state and revision information.

`Example of Annotating an Object Imperatively:`

You can easily add annotations to an existing object, like a Pod, using `kubectl annotate`:

```bash
$ kubectl annotate pod mypod key1=value1 key2=value2
```

`Example of Annotating in a YAML Manifest:`

When defining an object declaratively, you can include annotations within its `metadata` section:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webserver
  annotations:
    description: Deployment based PoC dates 2nd Mar'2022
# ... rest of the Deployment definition
```

`Saving Configuration with `--save-config=true`:`

The `--save-config=true` option used with `kubectl run` or `kubectl create` imperatively adds an annotation to the created object, storing the command used to create it. This can be useful for auditing or re-creating objects.

Let's test this:

```bash
# With --save-config=true
$ kubectl run saved --image=nginx:alpine --save-config=true
# Verify the annotation
$ kubectl get pod saved -o yaml | grep 'kubectl.kubernetes.io/last-applied-configuration'
```

Example output:

```yaml
  kubectl.kubernetes.io/last-applied-configuration: |
    {"apiVersion":"v1","kind":"Pod","metadata":{"annotations":{},"labels":{"run":"saved"},"name":"saved","namespace":"default"}}
```

Now, try without the `--save-config` option to see the difference (you'll need to delete the previous one first):

```bash
$ kubectl delete pod saved
pod "saved" deleted

# Without --save-config
$ kubectl run saved --image=nginx:alpine
# Verify the absence of the annotation
$ kubectl get pod saved -o yaml | grep 'kubectl.kubernetes.io/last-applied-configuration'
```

You should see no output from the `grep` command, indicating the annotation was not added.

### Quota and Limits Management

In shared Kubernetes clusters, managing resource consumption fairly among multiple users or teams is crucial. Kubernetes addresses this with `ResourceQuota` and `LimitRange` API resources.

`ResourceQuota:`

[`ResourceQuota`](https://kubernetes.io/docs/concepts/policy/resource-quotas/) provides constraints that limit the aggregate resource consumption per Namespace. This ensures that no single team or user can monopolize cluster resources.

You can set quotas for:

* `Compute Resource Quota:` Limits the total sum of CPU, memory, etc., that can be requested or limited across all Pods in a Namespace.

    ```yaml
    apiVersion: v1
    kind: ResourceQuota
    metadata:
      name: compute-resources
      namespace: myspace
    spec:
      hard:
        requests.cpu: "1"
        requests.memory: 1Gi
        limits.cpu: "2"
        limits.memory: 2Gi
        requests.nvidia.com/gpu: 4 # Example for custom resources like GPUs
    ```

* `Storage Resource Quota:` Limits the total sum of storage resources (e.g., total size of `PersistentVolumeClaims`, or `requests.storage` for ephemeral volumes).
* `Object Count Quota:` Restricts the number of objects of a given type within a Namespace (e.g., number of Pods, ConfigMaps, Services, etc.).

    ```yaml
    apiVersion: v1
    kind: ResourceQuota
    metadata:
      name: object-counts
      namespace: myspace
    spec:
      hard:
        configmaps: "10"
        persistentvolumeclaims: "4"
        pods: "4"
        secrets: "10"
        services: "10"
        services.loadbalancers: "2" # Specific service types can also be limited
    ```

`LimitRange:`

[`LimitRange`](https://kubernetes.io/docs/concepts/policy/limit-range/) is used in conjunction with `ResourceQuota` to enforce resource allocation constraints on individual Pods or Containers within a Namespace.

A `LimitRange` can:

* Set compute resource usage limits per Pod or Container (CPU, memory).
* Set storage request limits per `PersistentVolumeClaim`.
* Set a request-to-limit ratio for a resource.
* Set default requests and limits that are automatically injected into Containers' environments at runtime if not explicitly defined in the Pod spec.

`Example LimitRange Manifest:`

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-constraint-per-container
  namespace: myspace
spec:
  limits:
  - default:          # default limits applied if not specified
      cpu: 500m
    defaultRequest:   # default requests applied if not specified
      cpu: 500m
    max:              # max defines the highest allowed value
      cpu: "1"
    min:              # min defines the lowest allowed value
      cpu: 100m
    type: Container
```

### Autoscaling

While manual scaling is possible for a few Kubernetes objects, production clusters require dynamic scaling solutions to efficiently manage resources based on utilization, availability, and demand. Kubernetes offers various autoscalers:

* [`Horizontal Pod Autoscaler (HPA):`](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)

  * An algorithm-based controller that automatically adjusts the number of Pod replicas (in a ReplicaSet, Deployment, or Replication Controller).
  * Scales based on observed CPU utilization or other custom metrics.
  * `Example imperative command:`
        ```bash
        kubectl autoscale deploy myapp --min=2 --max=10 --cpu-percent=80
        ```
        This command creates an HPA that scales the `myapp` Deployment between 2 and 10 replicas, aiming to keep CPU utilization at 80%.

*[`Vertical Pod Autoscaler (VPA):`](https://github.com/kubernetes/autoscaler/tree/master/vertical-pod-autoscaler#readme)

* Automatically sets and dynamically adjusts Container resource requirements (CPU and memory) for Pods at runtime.
* Based on historical utilization data, current resource availability, and real-time events.
* Installed as a Custom Resource.

* [`Cluster Autoscaler:`](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler#cluster-autoscaler)

  * Automatically [re-sizes the underlying Kubernetes cluster](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md) (adds or removes nodes).
  * Adds nodes when there are unschedulable Pods due to insufficient resources.
  * Removes nodes when they are underutilized.

### Jobs and CronJobs

These resources are designed for batch processing and scheduled tasks.

* [`Job:`](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/)

  * Creates one or more Pods to perform a given task.
  * Ensures the task is completed successfully, automatically handling Pod failures by restarting them.
  * Once the task is complete, all associated Pods terminate.
  * `Configuration options:`
    * `parallelism`: Number of Pods allowed to run in parallel.
    * `completions`: Number of successful Pod completions required for the Job to finish.
    * `activeDeadlineSeconds`: Maximum duration for the Job to run.
    * `backoffLimit`: Number of retries before the Job is marked as failed.
    * `ttlSecondsAfterFinished`: Delay before a finished Job is automatically cleaned up.

* [`CronJob:`](https://kubernetes.io/docs/tasks/job/automated-tasks-with-cron-jobs/)

  * Allows Jobs to be performed at scheduled times/dates (like a cron daemon).
  * A new Job object is created for each execution cycle.
  * `Configuration options:`
    * `startingDeadlineSeconds`: Deadline to start a Job if the scheduled time was missed.
    * `concurrencyPolicy`: Defines behavior for concurrent Jobs (e.g., `Allow`, `Forbid`, `Replace`).

### StatefulSets

The [`StatefulSet`](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) controller is specifically designed for stateful applications that require:

* `Unique identity:` Such as stable network identifiers (e.g., fixed hostnames) and persistent storage.
* `Strict ordering:` For deployment, scaling, and termination of Pods (e.g., in a leader-follower database cluster).

`StatefulSet` provides identity and guaranteed ordering. However, they have strict `Service` and `Storage Volume` dependencies, making their configuration and deployment more challenging than stateless Deployments. They also support scaling, rolling updates, and rollbacks.

### Custom Resources

In Kubernetes, a `resource` is an API endpoint that stores a collection of API objects (e.g., the Pod resource contains all Pod objects). While built-in resources cover many use cases, you can extend Kubernetes' capabilities with `Custom Resources`.

* `Dynamic Nature:` Custom resources can be added or removed from a running cluster at any time without modifying the Kubernetes source code.
* `Custom Controllers:` To make a custom resource declarative and functional, you typically need to create and install a `custom controller`. This controller watches for changes to your custom resource objects and performs the necessary actions.
* `Methods to Add Custom Resources:`
  * [`Custom Resource Definitions (CRDs):`](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) The simplest way to add custom resources. It does not require programming knowledge to define the resource schema, though building the custom controller (the logic that acts on the CRD) still requires programming.
  * [`API Aggregation:`](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/apiserver-aggregation/) Provides more fine-grained control. It involves writing API Aggregators, which are subordinate API services that sit behind the primary API Server. The primary API Server acts as a proxy, forwarding requests for custom resources to the appropriate aggregated API service.

### Security Contexts and Pod Security Admission

These features help define and enforce security privileges and access control settings for Pods and Containers.

* [`Security Contexts:`](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)

  * Allow you to set Discretionary Access Control (DAC) permissions, privileged running modes, capabilities, security labels, etc., for individual Pods and Containers.
  * Their effect is limited to the specific Pods and Containers where they are configured in the `spec` section.
  * `Example Pod Manifest with Security Contexts:`
        ```yaml
        apiVersion: v1
        kind: Pod
        metadata:
          name: demo
        spec:
          securityContext: # Pod-level security context
            runAsUser: 1000
            runAsGroup: 3000
            fsGroup: 2000 # Applies to volumes
          volumes:
          - name: vol
            emptyDir: {}
          containers:
          - name: busy
            image: busybox:1.28
            command: [ "sh", "-c", "sleep infinity" ]
            volumeMounts:
            - name: vol
              mountPath: /data/demo
            securityContext: # Container-level security context
              allowPrivilegeEscalation: false
        ```
        In this example:
    * `runAsUser: 1000` and `runAsGroup: 3000` set the user and group IDs for all processes in the Pod's containers.
    * `fsGroup: 2000` sets the group ID for the owner of the `vol` volume.
    * `allowPrivilegeEscalation: false` specifically for the `busy` container prevents processes from gaining more privileges than their parent.

* [`Pod Security Admission:`](https://kubernetes.io/docs/concepts/security/pod-security-admission/)

  * A built-in admission controller, enabled by default in the API Server, for Pod Security.
  * Enforces the three `Pod Security Standards` at the namespace level.
  * Automates the application of security context restrictions to Pods during deployment.
  * [`Pod Security Standard Profiles:`](https://kubernetes.io/docs/concepts/security/pod-security-standards/)
    * ``privileged``: Least restrictive, for workloads that require full privileges.
    * ``baseline``: Minimally restrictive, for common application workloads, preventing known privilege escalations.
    * ``restricted``: Highly restrictive, enforcing Pod hardening best practices for security-critical applications and less trusted users.

### Network Policies

Kubernetes' default networking allows all Pods to communicate freely. [`Network Policies`](https://kubernetes.io/docs/concepts/services-networking/network-policies/) provide a mechanism to restrict communication between Pods and other resources inside and outside the cluster.

* `Firewall-like rules:` They define how Pods are allowed to talk to each other, similar to traditional firewalls.
* `Default Behavior:` Pods not covered by any Network Policy will continue to receive unrestricted traffic.
* `Policy Types:`
  * `podSelector`: Selects the Pods to which the policy applies.
  * `Ingress`: Defines rules for inbound traffic to the selected Pods.
  * `Egress`: Defines rules for outbound traffic from the selected Pods.
* `Rule Elements:` Policies define rules based on source/destination `ipBlocks`, `ports`, and selectors for other Pods or Namespaces.
* `Good Practice:` It's recommended to define a "default deny" policy for a Namespace to block all traffic by default, then define specific rules to allow necessary traffic.

`Example Network Policy Manifest:`

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: demo-netpol
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db # This policy applies to pods with label role: db
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from: # Allow ingress from these sources
    - namespaceSelector: # From pods in namespaces with label project: myproject
        matchLabels:
          project: myproject
    - podSelector: # From pods in the same namespace with label role: frontend
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 6379 # To TCP port 6379 on the protected pods
  egress:
  - to: # Allow egress to these destinations
    - ipBlock: # To IPs in this CIDR range
        cidr: 10.0.0.0/24
    ports:
    - protocol: TCP
      port: 5978 # From TCP port 5978 of the protected pods
```

`Important Considerations:`

* Not all Kubernetes networking solutions support Network Policies. The underlying Container Network Interface (CNI) plugin must implement them.
* By default, Network Policies are namespaced, but some plugins offer cluster-wide policies.

### Monitoring, Logging, and Troubleshooting

Effective operation of Kubernetes clusters requires robust monitoring, logging, and troubleshooting capabilities.

`Monitoring:`
Collecting resource usage data (CPU, memory, network) from Pods, Services, and nodes is essential for understanding consumption and making scaling decisions.

* [`Kubernetes Metrics Server:`](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/#metrics-server)

  * A cluster-wide aggregator of resource usage data.
  * Available as a plugin, allowing `kubectl top nodes` and `kubectl top pods` commands.
  * Commands are namespace-specific and support sorting (e.g., `--sort-by=cpu`).

* [Prometheus](https://prometheus.io/):

  * A graduated [CNCF](https://www.cncf.io/) project for scraping metrics from various Kubernetes components and objects.
  * Can also instrument application code using client libraries for custom metrics.

`Logging:`
Collecting logs from different system components is vital for troubleshooting and debugging.

* `Kubernetes Logging Limitations:`
  * Kubernetes itself does not provide cluster-wide logging aggregation by default.
  * `kubectl logs` is limited to currently running containers, or the very last failed container (`-p` or `--previous` flag).
  * `Example commands:`
        ```bash
        $ kubectl logs pod-name
        $ kubectl logs pod-name container-name
        $ kubectl logs pod-name container-name -p
        ```
* `Third-Party Logging Solutions:`
  * Centralized log aggregation requires external tools.
  * A popular approach is using [`Elasticsearch`](https://docs.fluentd.org/output/elasticsearch) (for storage and search) with `Fluentd` (as a log collector agent on nodes). [Fluentd](http://www.fluentd.org/) is an open-source data collector and a CNCF graduated project.

`Troubleshooting:`
Beyond logs, interacting with containers and inspecting events are key troubleshooting techniques.

* `Executing Commands in Containers:`
  * Run a custom command inside a running container:
        ```bash
        $ kubectl exec pod-name -- ls -la /
        $ kubectl exec pod-name -c container-name -- env
        ```
  * Interact with a running container's shell:
        ```bash
        $ kubectl exec pod-name -c container-name -it -- /bin/sh
        ```
        (Using `-it` for interactive terminal).
* `Viewing Events:`
  * Check events to understand why a Pod might not be reaching its expected running state (e.g., missing dependencies, image pull issues).
  * Events can be viewed for the entire cluster or a specific Pod:
        ```bash
        $ kubectl get events
        $ kubectl events # (alternative command)
        $ kubectl describe pod pod-name # Displays events specific to the pod at the end
        ```

### Helm

Deploying complex applications often involves numerous Kubernetes YAML manifests for Deployments, Services, PersistentVolumes, Ingress, etc. Managing these individually can be cumbersome. [`Helm`](https://helm.sh/) is a package manager for Kubernetes that simplifies this process.

* `Charts:` Helm packages all these manifests, along with other metadata, into a well-defined format called a "Chart." Charts can be `templatized`.
* `Repositories:` Charts can be served via repositories, similar to `rpm` and `deb` package repositories for Linux, or container image registries.
* `Functionality:`
  * `CLI Client:` `helm` is a CLI client that runs alongside `kubectl` on your workstation and uses `kubeconfig` to securely communicate with the Kubernetes API server.
  * `Deployment Workflow:` The `helm` client queries Chart repositories, downloads desired Charts, and then requests the API server to deploy the defined resources in the cluster.
* `Analogy:` Helm is analogous to `yum` or `apt` for Kubernetes, managing the lifecycle of applications packaged as Charts.Charts submitted for Kubernetes are available [here](https://artifacthub.io/).

### Service Mesh

A `Service Mesh` is a third-party solution that provides advanced application connectivity and exposure features beyond what native Kubernetes Services and Ingress Controllers offer. They are particularly popular in larger, more dynamic Kubernetes clusters.

* `Key Features:`

  * Service discovery
  * Mutual TLS (mTLS) certificates for encrypted communication
  * Multi-cloud routing
  * Traffic telemetry (observability)
  * Advanced traffic management (e.g., fault injection, traffic splitting)

* `Architecture:`

  * `Data Plane:` Typically consists of a proxy component (often a sidecar container injected into each Pod) that handles all Pod-to-Pod communication. Less frequently, a single proxy per node.
  * `Control Plane:` Manages the Data Plane proxies, responsible for service discovery, telemetry collection, load balancing configuration, network policy enforcement, and optionally ingress/egress gateways.

* `Benefits:`

  * Enhances observability and security within the cluster by providing real-time visibility into intra-cluster traffic and offering alternative ways to restrict and encrypt communication.

* `Popular Implementations:`

  * [Consul](https://www.consul.io/) by [HashiCorp](https://www.hashicorp.com/)
  * [Istio](https://istio.io/) (one of the most popular, backed by Google, IBM, Lyft)
  * [Kuma](https://kuma.io/) by [Kong](https://konghq.com/)
  * [Linkerd](https://linkerd.io/) (a CNCF project)
  * [Cilium](https://cilium.io/) (an eBPF-based service mesh)
  * [Traefik](https://traefik.io/traefik-mesh/) Mesh by [Containous](https://traefik.io/)
  * [Tanzu Service Mesh](https://tanzu.vmware.com/service-mesh) by VMware

### Application Deployment Strategies

Beyond the basic Rolling Update mechanism of Deployments, more sophisticated strategies are used for smoother and safer application updates.

* `Rolling Update (Default Deployment Strategy):`

  * Updates application replicas incrementally, replacing old versions with new ones.
  * Supports rollbacks to previous versions.
* `Challenge:` During the transition, the Service exposes both old and new versions, making it hard to control traffic to specific versions.

* `Canary Strategy:`

  * Runs two application releases (old and new) simultaneously, each managed by independent Deployment controllers.
  * Both Deployments are exposed by the *same* Service.
  * Users control the amount of traffic each Deployment receives by scaling up or down the respective Deployment controllers. This allows for gradual rollout and testing of the new version with a small subset of users before a full rollout.

* `Blue/Green Strategy:`

  * Runs the same application release or two different releases in two entirely `isolated environments` (e.g., "Blue" and "Green").
  * Only one environment is actively receiving production traffic at a time (e.g., "Blue" is live, "Green" is idle or under testing).
  * Traffic is shifted entirely from one environment to the other once the new environment is deemed ready.
  * Requires two independent Deployment controllers, each exposed by their dedicated Services. A separate traffic shifting mechanism (like an Ingress or another Service acting as a traffic router) is needed to switch between the environments.