# istio-http-example

## Prepare
### kubernetes
You have to prepare kubernetes cluster. I'm using EKS on AWS.

### istioctl

```bash
$ curl -L https://git.io/getLatestIstio | sh -
$ cd istio-1.*
$ sudo cp bin/istioctl /usr/local/bin/
```

### helm

```bash
$ cd istio-1.*
$ kubectl create -f install/kubernetes/helm/helm-service-account.yaml
$ helm init --service-account tiller
```

### istio

```bash
$ kubectl create namespace istio-system
$ cd istio-1.*
$ helm install \
--wait \
--name istio \
--namespace istio-system \
install/kubernetes/helm/istio
...
$ helm status istio
LAST DEPLOYED: Fri Feb  1 22:40:42 2019
NAMESPACE: istio-system
STATUS: DEPLOYED
...
```
If you can find `istio-sidecar-injector`, istio auto injection is running.

```bash
$ kubectl get pods -n istio-system
NAME                                     READY   STATUS    RESTARTS   AGE
istio-citadel-7dd558dcf-ldb44            1/1     Running   0          17h
istio-egressgateway-88887488d-bp7qv      1/1     Running   0          17h
istio-galley-787758f7b8-txr4p            1/1     Running   0          17h
istio-ingressgateway-58c77897cc-qn554    1/1     Running   0          17h
istio-pilot-868cdfb5f7-znvjr             2/2     Running   0          17h
istio-policy-56c4579578-25ztf            2/2     Running   0          17h
istio-sidecar-injector-d7f98d9cb-gnckv   1/1     Running   0          17h
istio-telemetry-7fb48dc68b-4826f         2/2     Running   0          17h
prometheus-76db5fddd5-48zqq              1/1     Running   0          17h
```

## Run

```bash
$ kubectl apply -f namespace.yml
$ kubectl apply -f deployment-backend.yml
$ kubectl apply -f service-backend.yml
$ kubectl apply -f deployment-client.yml
```

If istio auto injection is running, you can find 2 containers per pod.

```bash
$ kubectl get pods -n istio-grpc-example
NAME                         READY   STATUS    RESTARTS   AGE
backend-0-5576d86885-klqw7   2/2     Running   0          13h
backend-0-5576d86885-xsx5g   2/2     Running   0          13h
client-0-79f8b95476-x784d    2/2     Running   1          13h
```

You can confirm http connection.

```bash
$ kubectl logs -f client-0-79f8b95476-x784d -n istio-grpc-example -c python
Name Ryoko Shintani, Age: 37
Name Aoi Yuuki, Age: 26
Name Yui Horie, Age: 42
Name Kana Hanazawa, Age: 29
Name Ayana Taketatsu, Age: 29
-------------------------------------------------------------
Name Kana Asumi, Age: 35
Name Ayane Sakura, Age: 25
Name Yuuko Goto, Age: 43
Name Yukari Tamura, Age: 42
Name Yuka Iguchi, Age: 30
Name Yoko Hikasa, Age: 33
-------------------------------------------------------------
```
