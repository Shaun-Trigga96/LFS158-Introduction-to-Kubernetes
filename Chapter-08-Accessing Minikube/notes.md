# Accessing Minikube

## Chapter Overview

In this chapter, we will learn about different methods of accessing a Kubernetes cluster.

We can use a variety of external clients or custom scripts to access our cluster for administration purposes. We will explore kubectl as a CLI tool to access the Minikube Kubernetes cluster, the Kubernetes Dashboard as a web-based user interface to interact with the cluster, and the curl command with proper credentials to access the cluster via APIs.

## Learning Objectives

By the end of this chapter, you should be able to:

- Compare methods to access a Kubernetes cluster.
- Access the Minikube Kubernetes cluster with kubectl.
- Access the Minikube Kubernetes cluster from the Dashboard.
- Access the Minikube Kubernetes cluster via APIs.

# Accessing Minikube

Any healthy running Kubernetes cluster can be accessed via any one of the following methods: Command Line Interface (CLI) tools and scripts, web-based User Interface (Web UI) from a web browser, and APIs from CLI or programmatically. These methods are applicable to all Kubernetes clusters.

## Methods to Access Minikube

### Command Line Interface (CLI)

`kubectl` is the Kubernetes Command Line Interface (CLI) client used to manage cluster resources and applications. It is very flexible and easy to integrate with other systems, therefore it can be used standalone, or as part of scripts and automation tools. Once all required credentials and cluster access points have been configured for `kubectl`, it can be used remotely from anywhere to access a cluster.

We will be using `kubectl` extensively to deploy applications, and manage and configure Kubernetes resources.

### Web-based User Interface (Web UI)

The Kubernetes Dashboard provides a Web-based User Interface (Web UI) to interact with a Kubernetes cluster to manage resources and containerized applications. While not as flexible as the `kubectl` CLI client tool, it is still a preferred tool for users who are not as proficient with the CLI.

### APIs

The main component of the Kubernetes control plane is the API Server, responsible for exposing the Kubernetes APIs. The APIs allow operators and users to directly interact with the cluster. Using both CLI tools and the Dashboard UI, we can access the API server running on the control plane node to perform various operations to modify the cluster's state. The API Server is accessible through its endpoints by agents and users possessing the required credentials.

The HTTP API directory tree of Kubernetes can be divided into three independent group types:

- `Core group` (`/api/v1`): This group includes objects such as Pods, Services, Nodes, Namespaces, ConfigMaps, Secrets, etc.
- `Named group`: This group includes objects in `/apis/$NAME/$VERSION` format. These different API versions imply different levels of stability and support:
  - `Alpha level`: It may be dropped at any point in time, without notice. For example, `/apis/batch/v2alpha1`.
  - `Beta level`: It is well-tested, but the semantics of objects may change in incompatible ways in a subsequent beta or stable release. For example, `/apis/certificates.k8s.io/v1beta1`.
  - `Stable level`: Appears in released software for many subsequent versions. For example, `/apis/networking.k8s.io/v1`.
- `System-wide`: This group consists of system-wide API endpoints, like `/healthz`, `/logs`, `/metrics`, `/ui`, etc.

We can access an API Server either directly by calling the respective API endpoints, using the CLI tools, or the Dashboard UI.

Next, we will see how we can access the Minikube Kubernetes cluster we set up in the previous chapter.

### `kubectl`

`kubectl` allows us to manage local Kubernetes clusters like the Minikube cluster, or remote clusters deployed in the cloud. It is generally installed before installing and starting Minikube, but it can also be installed after the cluster bootstrapping step.

A Minikube installation has its own `kubectl` CLI installed and ready to use. However, it is somewhat inconvenient to use as the `kubectl` command becomes a subcommand of the `minikube` command. Users would be required to type longer commands, such as `minikube kubectl -- <subcommand> <object-type> <object-name> -o --option`, instead of just `kubectl <subcommand> <object-type> <object-name> -o --option`. While a simple solution would be to set up an alias, the recommendation is to run the `kubectl` CLI tool as a standalone installation.

Once separately installed, `kubectl` receives its configuration automatically for Minikube Kubernetes cluster access. However, in different Kubernetes cluster setups, we may need to manually configure the cluster access points and certificates required by `kubectl` to securely access the cluster.

There are different methods that can be used to install `kubectl` listed in the Kubernetes documentation. For best results, it is recommended to keep `kubectl` within one minor version of the desired Kubernetes release. Next, we will describe the `kubectl` CLI installation process.

Additional details about the `kubectl` command line client can be found in the [kubectl book](https://kubernetes.io/docs/reference/kubectl/overview/), the [Kubernetes official documentation](https://kubernetes.io/docs/home/), or its [GitHub repository](https://github.com/kubernetes/kubectl).

## Installing kubectl

`kubectl` is the command-line tool for interacting with Kubernetes clusters. This guide provides instructions for installing `kubectl` on Linux, macOS, and Windows.

It's recommended to keep your `kubectl` version within one minor version of your Kubernetes cluster's version for best compatibility.

## Installing `kubectl` on Linux

To install `kubectl` on Linux, follow these steps:

1. `Download the latest stable `kubectl` binary:`

    ```bash
    $ curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
    ```

      - The `https://dl.k8s.io/release/stable.txt` URL displays the latest stable Kubernetes release version.

2. `Install the downloaded binary:`

    ```bash
    $ sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
    ```

    `NOTE:` *To download and set up a specific version of `kubectl` (e.g., `v1.28.3`), use the following command instead*:

    ```bash
    $ curl -LO https://dl.k8s.io/release/v1.28.3/bin/linux/amd64/kubectl
    ```

3. `Verify the installed version:`

    ```bash
    $ kubectl version --client
    ```

4. `Enable shell autocompletion (for Bash):`

    ```bash
    $ sudo apt update && sudo apt install -y bash-completion
    $ source /usr/share/bash-completion/bash_completion
    $ source <(kubectl completion bash)
    $ echo 'source <(kubectl completion bash)' >> ~/.bashrc
    ```

## Installing `kubectl` on macOS

There are two methods to install `kubectl` on macOS: manually and using Homebrew.

## Manual Installation

1. `Download the latest stable binary:`

    ```bash
    $ curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/amd64/kubectl"
    ```

      - The `https://dl.k8s.io/release/stable.txt` URL displays the latest stable Kubernetes release version.

2. `Make it executable and move it to your`PATH`:`

    ```bash
    $ chmod +x ./kubectl
    $ sudo mv ./kubectl /usr/local/bin/kubectl
    $ sudo chown root: /usr/local/bin/kubectl
    ```

    `NOTE:`

      - *To download a specific version of `kubectl` (e.g., `v1.28.3`), use*:

        ```bash
        $ curl -LO https://dl.k8s.io/release/v1.28.3/bin/darwin/amd64/kubectl
        ```

      - For macOS systems with Apple Silicon (ARM-based processors), replace `/amd64/` with `/arm64/` in the download commands.

### Installation using Homebrew

If you have Homebrew installed, you can install `kubectl` with a single command:

```bash
$ brew install kubectl
```

or

```bash
$ brew install kubernetes-cli
```

### Verify Installation (both methods)

```bash
$ kubectl version --client
```

### Enable Shell Autocompletion

A typical helpful post-installation configuration is to enable shell autocompletion for `kubectl` on your favorite shell (bash, fish, zsh).

## Installing `kubectl` on Windows

To install `kubectl` on Windows, you can either download the binary directly or use `curl`. After downloading, the binary needs to be added to your system's `PATH`.

1. `Download the`kubectl.exe`binary:`

      - `Direct Download (latest stable):`
        Obtain the latest stable `kubectl` release version from [https://dl.k8s.io/release/stable.txt](https://dl.k8s.io/release/stable.txt). Then, construct the download URL, for example (as of this update, v1.29.3):
        `https://dl.k8s.io/release/v1.29.3/bin/windows/amd64/kubectl.exe`

      - `Direct Download (specific version):`
        To align with a specific Minikube Kubernetes version (e.g., `v1.28.3`), use:
        `https://dl.k8s.io/release/v1.28.3/bin/windows/amd64/kubectl.exe`

      - `Using`curl`from CLI (if installed):`

        ```powershell
        curl -LO "https://dl.k8s.io/release/v1.28.3/bin/windows/amd64/kubectl.exe"
        ```

2. `Add the`kubectl` binary folder to your `PATH`environment variable.`

      - `Note:` *If Docker Desktop for Windows is installed, it may add its own version of `kubectl` to `PATH`. You might need to place your new `PATH` entry before Docker Desktop's entry or remove Docker Desktop's `kubectl`*.

3. `Verify the installed version:`

    ```powershell
    $ kubectl version --client
    ```

### Enable Shell Autocompletion

A typical helpful post-installation configuration is to enable shell autocompletion for `kubectl` for PowerShell.

## `kubectl` Configuration File

To access a Kubernetes cluster, the `kubectl` client requires the control plane node's endpoint and appropriate credentials for secure interaction with the API Server. When Minikube starts, it automatically generates a configuration file named `config` (often referred to as [kubeconfig](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)) within the user's `~/.kube` directory. This file contains all the necessary connection details for `kubectl`.

By default, the `kubectl` binary parses this `kubeconfig` file to locate the control plane node's connection endpoint and retrieve the required credentials. It's possible to configure multiple `kubeconfig` files with a single `kubectl` client.

To view the connection details, you can either display the content of the `~/.kube/config` file (on Linux) or run the following command:

```bash
$ kubectl config view
```

A redacted example output of `kubectl config view` looks like this:

```yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /home/student/.minikube/ca.crt
    server: https://192.168.99.100:8443
  name: minikube
contexts:
- context:
    cluster: minikube
    user: minikube
  name: minikube
current-context: minikube
kind: Config
preferences: {}
users:
- name: minikube
  user:
    client-certificate: /home/student/.minikube/profiles/minikube/client.crt
    client-key: /home/student/.minikube/profiles/minikube/client.key
```

The `kubeconfig` file includes the API Server's endpoint (e.g., `https://192.168.99.100:8443`) and the Minikube user's client authentication key and certificate data.

Once `kubectl` is installed, you can display information about your Minikube Kubernetes cluster using the `kubectl cluster-info` command:

```bash
$ kubectl cluster-info
```

Example output:

```bash
Kubernetes master is running at https://192.168.99.100:8443
KubeDNS is running at https://192.168.99.100:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

You can find more details about the `kubectl` command-line options [here](https://kubernetes.io/docs/reference/kubectl/overview/).

While the `~/.kube/config` file is automatically created for Minikube clusters, this is not always the case for Kubernetes clusters installed by other tools. In such scenarios, the configuration file may need to be created manually and re-configured to suit various networking and client/server setups.

## Kubernetes Dashboard

The [Kubernetes Dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) is a web-based user interface for managing Kubernetes clusters. In Minikube, the Dashboard is installed as an addon but is disabled by default. Before using the Dashboard, you need to enable it, along with the `metrics-server` addon, which collects usage metrics from the cluster.

To access the Dashboard from Minikube, you can use the `minikube dashboard` command. This command will open a new tab in your web browser displaying the Kubernetes Dashboard, but only after you've listed, enabled the necessary addons, and verified their state.

Here's the sequence of commands:

1. List available Minikube addons:

    ```bash
    $ minikube addons list
    ```

2. Enable the `metrics-server` addon:

    ```bash
    $ minikube addons enable metrics-server
    ```

3. Enable the `dashboard`` addon:

    ```bash
    $ minikube addons enable dashboard
    ```

4. Verify the state of your addons again:

    ```bash
    $ minikube addons list
    ```

5. Launch the Kubernetes Dashboard:

    ```bash
    $ minikube dashboard
    ```

This command should automatically open a new browser tab with the Kubernetes Dashboard.

![Kubernetes Dashboard](KubernetesDashboard.png)

`NOTE:` *If your browser does not open a new tab and display the Dashboard as expected, check the output in your terminal. It might display a URL for the Dashboard (potentially along with some error messages). If the URL is not displayed, you can explicitly request it with the following command*:

```bash
$ minikube dashboard --url
```

Copy and paste the displayed URL into a new tab in your browser. Depending on your terminal's features, you might be able to simply click or right-click the URL to open it directly in your browser.

After a logout/login or a reboot of your workstation, the expected behavior (where `minikube dashboard` directly opens a new browser tab displaying the Dashboard) may be observed.

## APIs with `kubectl proxy`

The `kubectl proxy` command allows you to access the Kubernetes API server by setting up a local proxy. When you issue this command, `kubectl` authenticates with the API server on the control plane node and makes its services available on the default proxy port `8001`.

First, issue the `kubectl proxy` command:

```bash
$ kubectl proxy
Starting to serve on 127.0.0.1:8001
```

This command locks the terminal for as long as the proxy is running. To run it in the background, use `kubectl proxy &`.

When `kubectl proxy` is running, you can send requests to the API over `localhost` on the default proxy port `8001` (from another terminal, as the proxy locks the first one when running in the foreground):

```bash
$ curl http://localhost:8001/
```

This `curl` request will return a JSON object listing all the available API endpoints from the API server:

```json
{
  "paths": [
    "/api",
    "/api/v1",
    "/apis",
    "/apis/apps",
    // ...
    "/logs",
    "/metrics",
    "/openapi/v2",
    "/version"
  ]
}
```

You can explore various path combinations with `curl` or directly in a web browser, such as:

- `http://localhost:8001/api/v1`
- `http://localhost:8001/apis/apps/v1`
- `http://localhost:8001/healthz`
- `http://localhost:8001/metrics`

## APIs with Authentication

When not using `kubectl proxy`, you need to authenticate to the API Server when sending API requests. This can be done by providing a Bearer Token or by providing a set of keys and certificates.

A Bearer Token is an access token generated by the authentication server (the API Server on the control plane node) at the client's request. Using this token, the client can securely communicate with the Kubernetes API Server without providing additional authentication details, and then access resources. The token may need to be provided again for subsequent resource access requests.

Let's create an access token for the `default` ServiceAccount and grant it special permission to access the root directory of the API. This special permission was not necessary when `kubectl proxy` was used. The special permission will be set through a Role-Based Access Control (RBAC) policy, specifically a `ClusterRole` and `ClusterRoleBinding` (RBAC, ClusterRoles, and ClusterRoleBindings will be discussed in a later chapter). This special permission is only needed to access the root directory of the API, but not for `/api`, `/apis`, or other subdirectories.

1. `Create an access token for the `default` ServiceAccount:`

    ```bash
    $ export TOKEN=$(kubectl create token default)
    ```

2. `Create a `ClusterRole` for root API access:`

    ```bash
    $ kubectl create clusterrole api-access-root --verb=get --non-resource-url=/*
    ```

3. `Create a `ClusterRoleBinding` to grant the `api-access-root` role to the `default` ServiceAccount:`

    ```bash
    $ kubectl create clusterrolebinding api-access-root --clusterrole api-access-root --serviceaccount=default:default
    ```

4. `Retrieve the API Server endpoint:`

    ```bash
    $ export APISERVER=$(kubectl config view | grep https | cut -f 2- -d ":" | tr -d " ")
    ```

5. `Confirm that `APISERVER` stores the correct IP by comparing its output with `kubectl cluster-info`:`

    ```bash
    $ echo $APISERVER
    # Expected output similar to: https://192.168.99.100:8443

    $ kubectl cluster-info
    # Expected output similar to: Kubernetes control plane is running at https://192.168.99.100:8443 ...
    ```

6. `Access the API Server using `curl` with the Bearer Token:`

    ```bash
    $ curl $APISERVER --header "Authorization: Bearer $TOKEN" --insecure
    ```

    This will return the list of API paths, similar to when using `kubectl proxy`:

    ```json
    {
      "paths": [
        "/api",
        "/api/v1",
        "/apis",
        "/apis/apps",
        // ...
        "/logs",
        "/metrics",
        "/openapi/v2",
        "/version"
      ]
    }
    ```

You can run additional `curl` commands to retrieve details about specific API groups. These commands should work even without the special root permission defined above and granted to the default ServiceAccount:

```bash
$ curl $APISERVER/api/v1 --header "Authorization: Bearer $TOKEN" --insecure
$ curl $APISERVER/apis/apps/v1 --header "Authorization: Bearer $TOKEN" --insecure
$ curl $APISERVER/healthz --header "Authorization: Bearer $TOKEN" --insecure
$ curl $APISERVER/metrics --header "Authorization: Bearer $TOKEN" --insecure
```

Alternatively, instead of the access token, you can extract the client certificate, client key, and certificate authority data from the `.kube/config` file. Once extracted, they can be encoded and then passed with a `curl` command for authentication. The `curl` command would look similar to the example below. Keep in mind that this example command would only work with base64 encoded client certificate, key, and certificate authority data, and it is provided for illustrative purposes only:

```bash
$ curl $APISERVER --cert encoded-cert --key encoded-key --cacert encoded-ca
```
