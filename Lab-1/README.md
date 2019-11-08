# Instrucciones Laboratorio 1

### 1. Crear namespace
    $ kubectl create ns formacion
    namespace/formacion created
    
    $ kubectl get ns
    NAME          STATUS   AGE
    default       Active   18d
    formacion     Active   8m
    kube-public   Active   18d
    kube-system   Active   18d

### 1. Crear pod de nginx
    $ cd <path repo>/Lab-1
    $kubectl create -f nginx.yaml 
    pod/nginx created
### 2. Comprobar que el pod esta corriendo
    $ kubectl get pods -nformacion
    NAME    READY   STATUS    RESTARTS   AGE
    nginx   1/1     Running   0          1m
    
    $ kubectl get pods -owide
    NAME    READY   STATUS    RESTARTS   AGE   IP           NODE
    nginx   1/1     Running   0          1m    172.17.0.5   minikube
### 3. Ver Informacion del pod
    $ kubectl describe pod nginx -nformacion
    Name:         nginx
    Namespace:    formacion
    Node:         minikube/10.0.2.15
    Start Time:   Fri, 08 Nov 2019 14:11:40 +0100
    Labels:       app=nginx
    Annotations:  <none>
    Status:       Running
    IP:           172.17.0.5
    IPs:          <none>
    Containers:
      nginx:
        Container ID:   docker://78d7e5353996d8cfb958e8d4f545943e16ed957e8c1dd7d3cae891b889c48b66
        Image:          nginx
        Image ID:       docker-pullable://nginx@sha256:922c815aa4df050d4df476e92daed4231f466acc8ee90e0e774951b0fd7195a4
        Port:           80/TCP
        Host Port:      0/TCP
        State:          Running
          Started:      Fri, 08 Nov 2019 14:11:43 +0100
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from default-token-pttq4 (ro)
    Conditions:
      Type           Status
      Initialized    True 
      Ready          True 
      PodScheduled   True 
    Volumes:
      default-token-pttq4:
        Type:        Secret (a volume populated by a Secret)
        SecretName:  default-token-pttq4
        Optional:    false
    QoS Class:       BestEffort
    Node-Selectors:  <none>
    Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                     node.kubernetes.io/unreachable:NoExecute for 300s
    Events:
      Type    Reason                 Age   From               Message
      ----    ------                 ----  ----               -------
      Normal  Scheduled              4m8s  default-scheduler  Successfully assigned nginx to minikube
      Normal  SuccessfulMountVolume  4m8s  kubelet, minikube  MountVolume.SetUp succeeded for volume "default-token-pttq4"
      Normal  Pulling                4m7s  kubelet, minikube  pulling image "nginx"
      Normal  Pulled                 4m6s  kubelet, minikube  Successfully pulled image "nginx"
      Normal  Created                4m5s  kubelet, minikube  Created container
      Normal  Started                4m5s  kubelet, minikube  Started container
### 4. Acceder al pod con curl nginx
    $ minikube ssh
                             _             _            
                _         _ ( )           ( )           
      ___ ___  (_)  ___  (_)| |/')  _   _ | |_      __  
    /' _ ` _ `\| |/' _ `\| || , <  ( ) ( )| '_`\  /'__`\
    | ( ) ( ) || || ( ) || || |\`\ | (_) || |_) )(  ___/
    (_) (_) (_)(_)(_) (_)(_)(_) (_)`\___/'(_,__/'`\____)

    $ curl http://172.17.0.5
    <!DOCTYPE html>
    <html>
    <head>
    <title>Welcome to nginx!</title>
    <style>
        body {
            width: 35em;
            margin: 0 auto;
            font-family: Tahoma, Verdana, Arial, sans-serif;
        }
    </style>
    </head>
    <body>
    <h1>Welcome to nginx!</h1>
    <p>If you see this page, the nginx web server is successfully installed and
    working. Further configuration is required.</p>

    <p>For online documentation and support please refer to
    <a href="http://nginx.org/">nginx.org</a>.<br/>
    Commercial support is available at
    <a href="http://nginx.com/">nginx.com</a>.</p>

    <p><em>Thank you for using nginx.</em></p>
    </body>
    </html>

