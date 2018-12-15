# kubernetes-objects
[Tutorial](https://medium.com/tsys-engineering/kubernetes-objects-e0a8b93b5cdc)

## ssh connection:
```bash
$ ssh -L 30000:localhost:8001 -L 30001:192.168.39.118:2376 -L 9090:192.168.39.118:9090 -L 8443:localhost:8443 ryan@192.
168.1.101
$ curl $(minikube ip):9090
Hello Kubernetes bootcamp! | Running on: hello-pod | v=1
```

## k8s console:
```bash
$ kubectl create -f hello-app.yaml
pod/hello-pod created
$ kubectl get pods hello-pod -w
NAME        READY     STATUS    RESTARTS   AGE
hello-pod   1/1       Running   0          4s
$ kubectl get pod hello-pod -o yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: 2018-12-15T19:54:53Z
  name: hello-pod
  namespace: default
  resourceVersion: "546397"
  selfLink: /api/v1/namespaces/default/pods/hello-pod
  uid: 49feee7d-00a3-11e9-af98-08d0f8f11711
...

$ kubectl port-forward hello-pod 10000:8080 &
Forwarding from 127.0.0.1:10000 -> 8080
Forwarding from [::1]:10000 -> 8080

$ curl http://localhost:10000
Handling connection for 10000
Hello Kubernetes bootcamp! | Running on: hello-pod | v=1

$ kubectl logs -f hello-pod --tail 200 -c hello-app
Kubernetes Bootcamp App Started At: 2018-12-15T20:00:31.196Z | Running On:  hello-pod

Running On: hello-pod | Total Requests: 1 | App Uptime: 45.934 seconds | Log Time: 2018-12-15T20:01:17.130Z
Running On: hello-pod | Total Requests: 2 | App Uptime: 48.395 seconds | Log Time: 2018-12-15T20:01:19.591Z
Running On: hello-pod | Total Requests: 3 | App Uptime: 49.432 seconds | Log Time: 2018-12-15T20:01:20.628Z
Running On: hello-pod | Total Requests: 4 | App Uptime: 286.293 seconds | Log Time: 2018-12-15T20:05:17.489Z
Running On: hello-pod | Total Requests: 5 | App Uptime: 319.673 seconds | Log Time: 2018-12-15T20:05:50.869Z
Running On: hello-pod | Total Requests: 6 | App Uptime: 470.351 seconds | Log Time: 2018-12-15T20:08:21.547Z
Running On: hello-pod | Total Requests: 7 | App Uptime: 474.127 seconds | Log Time: 2018-12-15T20:08:25.323Z

$ kubectl describe pod hello-pod
Name:         hello-pod
Namespace:    default
Node:         minikube/192.168.122.157
Start Time:   Sat, 15 Dec 2018 12:00:27 -0800
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           172.17.0.10
Containers:
  hello-app:
    Container ID:   docker://0cdfc6d2f70f324c3d0c76c65ce3b3de1ecac841360619d00d9247a98e27d2d5
    Image:          hela:8443/hello-k8s:v1
    Image ID:       docker-pullable://hela:8443/hello-k8s@sha256:036874a2767bcbbb755ca91fca74508e7e6fdaafa1d84977e2e8ad6aa969daa5
    Port:           8080/TCP
    Host Port:      9090/TCP
    State:          Running
      Started:      Sat, 15 Dec 2018 12:00:31 -0800
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-nksb4 (ro)
Conditions:
  Type           Status
  Initialized    True
  Ready          True
  PodScheduled   True
Volumes:
  default-token-nksb4:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-nksb4
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason                 Age   From               Message
  ----    ------                 ----  ----               -------
  Normal  Scheduled              19m   default-scheduler  Successfully assigned hello-pod to minikube
  Normal  SuccessfulMountVolume  19m   kubelet, minikube  MountVolume.SetUp succeeded for volume "default-token-nksb4"
  Normal  Pulled                 19m   kubelet, minikube  Container image "hela:8443/hello-k8s:v1" already present on machine
  Normal  Created                19m   kubelet, minikube  Created container
  Normal  Started                19m   kubelet, minikube  Started container
```

## k8s console for labels:
```bash
$ kubectl create -f hello-labels.yaml
$ kubectl get pods --show-labels
NAME                              READY     STATUS    RESTARTS   AGE       LABELS
hello-labels                      1/1       Running   0          1m        appType=api

$ kubectl label pod hello-labels dept=billing
pod/hello-labels labeled
$ kubectl get pods --show-labels
NAME                              READY     STATUS    RESTARTS   AGE       LABELS
hello-labels                      1/1       Running   0          7m        appType=api,dept=billing

$ kubectl get pods -l dept
NAME           READY     STATUS    RESTARTS   AGE
hello-labels   1/1       Running   0          9m

$ kubectl label pod hello-labels dept=customer_service --overwrite
pod/hello-labels labeled
rwibawa@PLSLAPTOP-02742:~/workspace_k8s/kubernetes-objects$ kubectl get pods --show-labels
NAME                              READY     STATUS    RESTARTS   AGE       LABELS
hello-labels                      1/1       Running   0          11m       appType=api,dept=customer_service

# Delete a label
$ kubectl label pod hello-labels appType-
pod/hello-labels labeled
$ kubectl get pods --show-labels
NAME                              READY     STATUS    RESTARTS   AGE       LABELS
hello-labels                      1/1       Running   0          13m       dept=customer_service

$ kubectl port-forward pod/hello-labels :8080 &
Forwarding from 127.0.0.1:58305 -> 8080
Forwarding from [::1]:58305 -> 8080

$ curl http://localhost:58305
Handling connection for 58305
Hello Kubernetes bootcamp! | Running on: hello-labels | v=1

$ kubectl delete pod -l dept
pod "hello-labels" deleted
```

