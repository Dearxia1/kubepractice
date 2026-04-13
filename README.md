# 🐳 Kubernetes Minikube Practice

Welcome to my Kubernetes practice repository! 

During a practical class, I worked inside a **GitHub Codespace** (`/workspaces/kubepractice`) running Minikube to explore the core concepts of Kubernetes: Deployments, Pods, Services, and Ingress routing. 

Instead of just leaving the raw terminal output of my practice, I have documented my entire learning journey below. Each chapter explores what I did, what the commands actually mean, and answers the big "why" and "how" questions from the perspective of a student wrapping their head around the magic of Kubernetes.

## 📚 Table of Contents - The Learning Journey

1. **[Chapter 1: Unboxing Minikube and the Dashboard](./tutorial/01-minikube-setup.md)**
   Starting Minikube inside a Codespace, visualising the cluster through the dashboard, and verifying if our Docker engine is behaving.

2. **[Chapter 2: Deployments and Pods](./tutorial/02-deployments-and-pods.md)**
   Deploying our first `webapp` image (Nginx), scaling it to 3 replicas, and understanding the abstraction layers (`Deployment` -> `ReplicaSet` -> `Pod`).

3. **[Chapter 3: Services and Networking](./tutorial/03-kubernetes-services.md)**
   How do we actually reach our apps? Exploring and visualizing `ClusterIP`, `NodePort`, and `LoadBalancer` with architectural diagrams. 

4. **[Chapter 4: Ingress Routing](./tutorial/04-ingress-routing.md)**
   Playing the smart traffic cop! Setting up a frontend and a backend and routing URL paths to different services using a single Ingress Controller.

5. **[Chapter 5: Conceptual Questions answered](./tutorial/05-conceptual-questions.md)**
   A final summary answering the main theoretical questions of the practice.

---
*Documented with curiosity and caffeine.* ☕