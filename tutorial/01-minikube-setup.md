# Chapter 1: Unboxing Minikube and the Dashboard

> **Student's note**: *Before we begin, it's very important to highlight that this entire practice was carried out inside a GitHub Codespace (`/workspaces/kubepractice`), which means everything is running in a cloud-based containerized Linux environment instead of a local operating system.*

One of the first questions you might ask when learning Kubernetes is: *How do I even run it on my computer without setting up a massive cluster of physical servers?* 

This is where **Minikube** comes to the rescue. Minikube allows us to run a single-node Kubernetes cluster right inside our development environment. 

### Starting the Kubernetes Dashboard

Since interacting entirely via the command line can be intimidating at first, Kubernetes provides a graphical Dashboard.

```bash
@Dearxia1 ➜ /workspaces/kubepractice (main) $ minikube dashboard
🔌  Enabling dashboard ...
    ▪ Using image docker.io/kubernetesui/metrics-scraper:v1.0.8
    ▪ Using image docker.io/kubernetesui/dashboard:v2.7.0
💡  Some dashboard features require the metrics-server addon. To enable all features please run:

        minikube addons enable metrics-server

🤔  Verifying dashboard health ...
🚀  Launching proxy ...
🤔  Verifying proxy health ...
🎉  Opening http://127.0.0.1:39651/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/ in your default browser...
👉  http://127.0.0.1:39651/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/
^C
```

> **Wait, what just happened here?**
> Minikube fetched the necessary Docker images (`kubernetesui`) and spun up a proxy server. The output essentially tells me: *"Hey, your visual dashboard is ready, click this local link to view it!"*. Since I'm using a Codespace, it forwards this local port to my browser so I can navigate my cluster visually! I hit `Ctrl+C` (`^C`) after launching it to return to my terminal prompt while it continues running in the background.

### Checking the Engines: Where is Minikube?

Minikube claims to run on Docker... so let's verify if Docker is actually doing the heavy lifting by running `docker ps` to list active containers:

```bash
@Dearxia1 ➜ /workspaces/kubepractice (main) $ docker ps
CONTAINER ID   IMAGE                                 COMMAND                  CREATED          STATUS          PORTS                                                                                                                                  NAMES
c98a917cc43a   gcr.io/k8s-minikube/kicbase:v0.0.50   "/usr/local/bin/entr…"   14 minutes ago   Up 14 minutes   127.0.0.1:32768->22/tcp, 127.0.0.1:32769->2376/tcp, 127.0.0.1:32770->5000/tcp, 127.0.0.1:32771->8443/tcp, 127.0.0.1:32772->32443/tcp   minikube
```

> **Aha!**
> There it is. Minikube itself is just a Docker container running the `kicbase` image! It exposes ports for SSH (`22`), the Docker daemon (`2376`), and the Kubernetes API (`8443`). That means my "Kubernetes Node" is essentially just a Docker container pretending to be a whole computer inside my Codespace.

### Confirming the Cluster Status

Finally, let's ask Minikube if all its internal components are healthy using `minikube status`:

```bash
@Dearxia1 ➜ /workspaces/kubepractice (main) $ minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

> **What does this tell me?**
> - **Control Plane**: My single node is acting as the brain of the cluster.
> - **kubelet**: The agent that makes sure our containers describe exactly what we want is officially `Running`.
> - **apiserver**: The gateway for all `kubectl` commands is `Running`.
> - **kubeconfig**: My `kubectl` tool is properly configured and pointing to this cluster.

With the engines running, it's time to actually deploy our first application using Kubernetes!

[➡️ Go to next chapter: Deployments and Pods](./02-deployments-and-pods.md)
