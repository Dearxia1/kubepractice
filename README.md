# kubepractice

## 1. Dashboard y estado de Minikube
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

@Dearxia1 ➜ /workspaces/kubepractice (main) $ docker ps
CONTAINER ID   IMAGE                                 COMMAND                  CREATED          STATUS          PORTS                                                                                                                                  NAMES
c98a917cc43a   gcr.io/k8s-minikube/kicbase:v0.0.50   "/usr/local/bin/entr…"   14 minutes ago   Up 14 minutes   127.0.0.1:32768->22/tcp, 127.0.0.1:32769->2376/tcp, 127.0.0.1:32770->5000/tcp, 127.0.0.1:32771->8443/tcp, 127.0.0.1:32772->32443/tcp   minikube

@Dearxia1 ➜ /workspaces/kubepractice (main) $ minikube status
minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured

## 2. Crear deployment `webapp`
@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl create deployment webapp --image=nginx:alpine --replicas=3

deployment.apps/webapp created

@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl get nodes
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   19m   v1.35.1

@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl get deployments
NAME     READY   UP-TO-DATE   AVAILABLE   AGE
webapp   3/3     3            3           15s

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

> Se creó el deployment `webapp` con `nginx:alpine` y 3 réplicas.

## 3. Ver detalles del deployment
@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl describe deployment webapp
Name:                   webapp
Namespace:              default
Labels:                 app=webapp
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=webapp
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:   25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=webapp
  Containers:
   nginx:
    Image:         nginx:alpine
    Port:          <none>
    Host Port:     <none>
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   webapp-6bfcdc4bc9 (3/3 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  3m57s  deployment-controller  Scaled up replica set webapp-6bfcdc4bc9 from 0 to 3

> Información esencial del deployment.

## 4. Servicio ClusterIP y prueba interna
@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl expose deployment webapp --name=webapp-clusterip --port=80 --target-port=80 --type=ClusterIP
service/webapp-clusterip exposed

@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl get service webapp-clusterip
NAME               TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
webapp-clusterip   ClusterIP   10.98.159.20   <none>        80/TCP    74s

@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl run test-client --image=busybox --rm -it -- sh

/ # wget -O- webapp-clusterip
Connecting to webapp-clusterip (10.98.159.20:80)
writing to stdout
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy, 
API gateway, load balancer, content cache, or other features.</p>

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional 
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
-                    100% |***************************************************|   896  0:00:00 ETA
written to stdout

/ # exit

@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl get endpoints webapp-clusterip
Warning: v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME               ENDPOINTS                                   AGE
webapp-clusterip   10.244.0.5:80,10.244.0.6:80,10.244.0.7:80   4m5s

@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl get pods -o wide
NAME                      READY   STATUS    RESTARTS   AGE   IP           NODE       NOMINATED NODE   READINESS GATES
webapp-6bfcdc4bc9-5jqzp   1/1     Running   0          10m   10.244.0.6   minikube   <none>           <none>
webapp-6bfcdc4bc9-dwp27   1/1     Running   0          10m   10.244.0.5   minikube   <none>           <none>
webapp-6bfcdc4bc9-tbdxl   1/1     Running   0          10m   10.244.0.7   minikube   <none>           <none>

> Servicio interno validado desde un pod `busybox`.

## 5. Servicio NodePort y prueba externa
@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl expose deployment webapp --name=webapp-nodeport --port=80 --target-port=80 --type=NodePort
service/webapp-nodeport exposed

@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl get service webapp-nodeport
NAME              TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
webapp-nodeport   NodePort   10.110.181.181   <none>        80:30513/TCP   11s

@Dearxia1 ➜ /workspaces/kubepractice (main) $ minikube ip
192.168.49.2

@Dearxia1 ➜ /workspaces/kubepractice (main) $ curl http://192.168.49.2:30513
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy, 
API gateway, load balancer, content cache, or other features.</p>

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional 
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl describe service webapp-nodeport
Name:                     webapp-nodeport
Namespace:                default
Labels:                   app=webapp
Annotations:              <none>
Selector:                 app=webapp
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.110.181.181
IPs:                      10.110.181.181
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30513/TCP
Endpoints:                10.244.0.6:80,10.244.0.7:80,10.244.0.5:80
Session Affinity:         None
External Traffic Policy:  Cluster
Internal Traffic Policy:  Cluster
Events:                   <none>

> La aplicación es accesible externamente por NodePort.

## 6. Inspección interna de Minikube
@Dearxia1 ➜ /workspaces/kubepractice (main) $ minikube ssh
Linux minikube 6.8.0-1044-azure #50~22.04.1-Ubuntu SMP Wed Dec  3 15:13:22 UTC 2025 x86_64

docker@minikube:~$ docker ps
CONTAINER ID   IMAGE                          COMMAND                  CREATED          STATUS          PORTS     NAMES
c484314b588e   nginx                          "/docker-entrypoint.…"   16 minutes ago   Up 16 minutes             k8s_nginx_webapp-6bfcdc4bc9-tbdxl_default_50bac106-e39f-41e5-968c-8d8336620404_0
3034dfeff0d4   nginx                          "/docker-entrypoint.…"   16 minutes ago   Up 16 minutes             k8s_nginx_webapp-6bfcdc4bc9-5jqzp_default_401f4a94-d38d-44e1-8f20-5a7e61ec07d0_0
f95b8556a612   nginx                          "/docker-entrypoint.…"   16 minutes ago   Up 16 minutes             k8s_nginx_webapp-6bfcdc4bc9-dwp27_default_a7e0c381-fc3f-4186-bc77-2ebef8b5053f_0
...

docker@minikube:~$ sudo iptables -t nat -L KUBE-NODEPORTS -n
Chain KUBE-NODEPORTS (1 references)
target     prot opt source               destination         
KUBE-EXT-HH54PX2LENPCJ4PN  6    --  0.0.0.0/0            127.0.0.0/8          /* default/webapp-nodeport */ tcp dpt:30513 nfacct-name  localhost_nps_accepted_pkts
KUBE-EXT-HH54PX2LENPCJ4PN  6    --  0.0.0.0/0            0.0.0.0/0            /* default/webapp-nodeport */ tcp dpt:30513

docker@minikube:~$ exit
logout

> Se confirmó el pod y la regla NAT para NodePort.

## 7. Servicio LoadBalancer `webapp-lb`
@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl expose deployment webapp --name=webapp-lb --port=80 --target-port=80 --type=LoadBalancer
service/webapp-lb exposed

@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl get service webapp-lb
NAME        TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
webapp-lb   LoadBalancer   10.102.28.180   10.102.28.180   80:32684/TCP   2m17s

@Dearxia1 ➜ /workspaces/kubepractice (main) $ EXTERNAL_IP=$(kubectl get service webapp-lb -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
@Dearxia1 ➜ /workspaces/kubepractice (main) $ echo $EXTERNAL_IP
10.102.28.180

@Dearxia1 ➜ /workspaces/kubepractice (main) $ curl $EXTERNAL_IP
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy, 
API gateway, load balancer, content cache, or other features.</p>

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional 
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

@Dearxia1 ➜ /workspaces/kubepractice (main) $ echo "Abre en tu navegador: http://$EXTERNAL_IP"
Abre en tu navegador: http://10.102.28.180

> El LoadBalancer devuelve la página NGINX correctamente.

## 8. Backend y frontend
@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl create deployment backend --image=nginx:alpine --replicas=2
deployment.apps/backend created

@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl exec -it deployment/backend -- sh -c 'echo "Backend Service - $(hostname)" > /usr/share/nginx/html/index.html'

@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl expose deployment backend --name=backend-service --port=80 --type=ClusterIP
service/backend-service exposed

@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl get service backend-service
NAME              TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
backend-service   ClusterIP   10.110.247.242   <none>        80/TCP    70s

@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl create deployment frontend --image=nginx:alpine --replicas=2
deployment.apps/frontend created

@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl expose deployment frontend --name=frontend-service --port=80 --type=LoadBalancer
service/frontend-service exposed

@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl get services
NAME               TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
backend-service    ClusterIP      10.110.247.242   <none>          80/TCP         4m14s
frontend-service   LoadBalancer   10.99.142.181    10.99.142.181   80:32082/TCP   2m50s
kubernetes         ClusterIP      10.96.0.1        <none>          443/TCP        52m
webapp-clusterip   ClusterIP      10.98.159.20     <none>          80/TCP         28m
webapp-lb          LoadBalancer   10.102.28.180    10.102.28.180   80:32684/TCP   14m
webapp-nodeport    NodePort       10.110.181.181   <none>          80:30513/TCP   21m

> Backend y frontend creados con servicios internos y externos.

## 9. Ingress
@Dearxia1 ➜ /workspaces/kubepractice (main) $ minikube addons enable ingress

💡  ingress is an addon maintained by Kubernetes. For any concerns contact minikube on GitHub.
You can view the list of minikube maintainers at: https://github.com/kubernetes/minikube/blob/master/OWNERS
    ▪ Using image registry.k8s.io/ingress-nginx/kube-webhook-certgen:v1.6.7
    ▪ Using image registry.k8s.io/ingress-nginx/controller:v1.14.3
    ▪ Using image registry.k8s.io/ingress-nginx/kube-webhook-certgen:v1.6.7
🔎  Verifying ingress addon...
🌟  The 'ingress' addon is enabled

@Dearxia1 ➜ /workspaces/kubepractice (main) $ kubectl get pods -n ingress-nginx
NAME                                        READY   STATUS      RESTARTS   AGE
ingress-nginx-admission-create-cl9l9        0/1     Completed   0          6m39s
ingress-nginx-admission-patch-d74gr         0/1     Completed   0          6m39s
ingress-nginx-controller-596f8778bc-x7l4r   1/1     Running     0          6m39s

> Ingress habilitado y controlador en ejecución.

Dearxia1 ➜ /workspaces/kubepractice (main) $ # Crear un recurso Ingress para enrutar el tráfico a nuestros servicios
cat <<EOF | kubectl apply -f -
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /frontend
        pathType: Prefix
kubectl get ingressIngress se ha creado
ingress.networking.k8s.io/example-ingress created
NAME              CLASS   HOSTS   ADDRESS   PORTS   AGE
example-ingress   nginx   *                 80      0s
@Dearxia1 ➜ /workspaces/kubepractice (main) $ # Obtener la IP del Ingress
INGRESS_IP=$(kubectl get ingress example-ingress -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
echo $INGRESS_IP
192.168.49.2
@Dearxia1 ➜ /workspaces/kubepractice (main) $ # Probar las rutas
curl $INGRESS_IP/frontend
curl $INGRESS_IP/backend
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy, 
API gateway, load balancer, content cache, or other features.</p>

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional 
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy, 
API gateway, load balancer, content cache, or other features.</p>

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional 
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

----


kubectl delete deployment webapp frontend backend
 
 
¿Cuáles son las diferencias clave entre los tres tipos de servicios que has implementado?
¿En qué escenarios utilizarías cada tipo de servicio?
¿Cuál es la ventaja de usar Ingress sobre simplemente exponer múltiples servicios LoadBalancer?
Si un pod falla, ¿cómo afecta esto a los endpoints de un servicio? Pruébalo eliminando un pod.
¿Cómo se relaciona el selector de un servicio con las etiquetas de los pods?
 