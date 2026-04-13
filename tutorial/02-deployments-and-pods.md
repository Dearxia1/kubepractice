# Chapter 2: Deployments and Pods - Getting Apps Running

Now that our Minikube cluster is healthy, it's time to test its most fundamental capability: running an application. For this, we'll deploy a simple NGINX web server. 

### Creating the `webapp` Deployment

Instead of manually deploying individual containers (which are called `Pods` in Kubernetes terminology), we use a higher-level abstraction called a **Deployment**. A Deployment allows us to declare the desired state, and Kubernetes will figure out how to maintain it constantly.

```bash
@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl create deployment webapp --image=nginx:alpine --replicas=3

deployment.apps/webapp created
```

> **What does this tell the cluster?**
> "Hey Kubernetes, create a Deployment called `webapp`. Pull the `nginx:alpine` image and make sure you keep exactly 3 copies (replicas) of it running at all times." 

### Inspecting the Cluster State

Did it work? Let's verify what resources were created in the cluster.

First, let's look at our single-node cluster:

```bash
@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl get nodes
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   19m   v1.35.1
```

Now, let's see if our Deployment exists:

```bash
@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl get deployments
NAME     READY   UP-TO-DATE   AVAILABLE   AGE
webapp   3/3     3            3           15s
```

> **Nice!**
> The `3/3` under `READY` indicates that all 3 requested replicas are up and running perfectly. 

### The Under-the-Hood Look: `kubectl get all`

A Deployment is actually just an organizer. It creates another object called a `ReplicaSet`, and that `ReplicaSet` creates the individual `Pods`. Let's run `kubectl get all` to see this whole hierarchy:

```bash
@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl get all
NAME                          READY   STATUS    RESTARTS   AGE
pod/webapp-6bfcdc4bc9-5jqzp   1/1     Running   0          24s
pod/webapp-6bfcdc4bc9-dwp27   1/1     Running   0          24s
pod/webapp-6bfcdc4bc9-tbdxl   1/1     Running   0          24s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   19m

NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/webapp   3/3     3            3           24s

NAME                                DESIRED   CURRENT   READY   AGE
replicaset.apps/webapp-6bfcdc4bc9   3         3         3       24s
```

> **Connecting the dots:**
> 1. The **Deployment** (`deployment.apps/webapp`) defines the overarching plan.
> 2. It creates a **ReplicaSet** (`replicaset.apps/webapp-6bfcdc4bc9`) responsible for maintaining exactly 3 copies.
> 3. The ReplicaSet launches the actual three **Pods** (`pod/webapp-...`). Notice how their names are generated randomly based on the ReplicaSet's ID.

### Peeking Inside: `describe deployment`

If we want the ultimate level of detail on an object, we use `describe`.

```bash
@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl describe deployment webapp
Name:                   webapp
Namespace:              default
Labels:                 app=webapp
Selector:               app=webapp
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
...
Pod Template:
  Labels:  app=webapp
  Containers:
   nginx:
    Image:         nginx:alpine
...
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  3m57s  deployment-controller  Scaled up replica set webapp-6bfcdc4bc9 from 0 to 3
```

> **Key takeaway here:**
> Look at the `Labels` and the `Selector` (`app=webapp`). This is crucial! Kubernetes uses these key-value pairs (labels) to keep track of things. The Deployment looks for Pods with the label `app=webapp` to know if there are 3 of them running. Also, check the `Events` section: it keeps a log showing that it actually scaled up from 0 to 3 pods.

Right now, we have 3 pods running NGINX inside our cluster. But if we try to access them via browser... we can't! They are completely isolated. To fix this, we need **Services**, which we will cover next.

[⬅️ Go to previous chapter: Setup](./01-minikube-setup.md) | [➡️ Go to next chapter: Services and Networking](./03-kubernetes-services.md)
