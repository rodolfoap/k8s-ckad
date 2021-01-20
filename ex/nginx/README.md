# Usage

* Requires `../coredns` to be running before. The containers' `nfs.meia.lan` resolution is provided by CoreDNS.

* If minikube, enable Ingress with: `minikube addons enable ingress`.

* DNS resolution to `nfs.meia.lan` is required (if minikube, inside /etc/hosts)

```
192.168.1.50    		nfs.meia.lan
```

* Minikube or bare-metal K8s don't have an integrated load balancer. Only NodePort or Ingress can be used, so, in order to publish to the cluster exterior, see https://stackoverflow.com/questions/44110876/kubernetes-service-external-ip-pending.

```
$ minikube tunnel

Status:
	machine: minikube
	pid: 32179
	route: 10.96.0.0/12 -> 192.168.49.2
	minikube: Running
	services: [nginx-lbal]
    errors:
		minikube: no errors
		router: no errors
		loadbalancer emulator: no errors

$ kl

>>	NAMESPACE              NAME                                            READY   STATUS    RESTARTS   AGE     IP             NODE       NOMINATED NODE   READINESS GATES
	default                pod/nginx-85779445d7-cckk4                      1/1     Running   0          2m52s   172.17.0.5     minikube   <none>           <none>
	default                pod/nginx-85779445d7-d7hhz                      1/1     Running   0          2m52s   172.17.0.7     minikube   <none>           <none>
	default                pod/nginx-85779445d7-dzvlm                      1/1     Running   0          2m52s   172.17.0.6     minikube   <none>           <none>
	kube-system            pod/coredns-74ff55c5b-tm62q                     1/1     Running   1          14h     172.17.0.4     minikube   <none>           <none>
	kube-system            pod/etcd-minikube                               1/1     Running   1          14h     192.168.49.2   minikube   <none>           <none>
	kube-system            pod/kube-apiserver-minikube                     1/1     Running   1          14h     192.168.49.2   minikube   <none>           <none>
	kube-system            pod/kube-controller-manager-minikube            1/1     Running   1          14h     192.168.49.2   minikube   <none>           <none>
	kube-system            pod/kube-proxy-6b5x6                            1/1     Running   1          14h     192.168.49.2   minikube   <none>           <none>
	kube-system            pod/kube-scheduler-minikube                     1/1     Running   1          14h     192.168.49.2   minikube   <none>           <none>
	kube-system            pod/storage-provisioner                         1/1     Running   2          14h     192.168.49.2   minikube   <none>           <none>
	kubernetes-dashboard   pod/dashboard-metrics-scraper-c95fcf479-zv29l   1/1     Running   1          14h     172.17.0.2     minikube   <none>           <none>
	kubernetes-dashboard   pod/kubernetes-dashboard-6cff4c7c4f-5d4kk       1/1     Running   2          14h     172.17.0.3     minikube   <none>           <none>

>>	NAMESPACE              NAME                                TYPE           CLUSTER-IP       EXTERNAL-IP      PORT(S)                  AGE     SELECTOR
	default                service/kubernetes                  ClusterIP      10.96.0.1        <none>           443/TCP                  14h     <none>
	default                service/nginx-clip                  ClusterIP      10.108.167.93    <none>           80/TCP                   2m51s   name=nginx
	default                service/nginx-lbal                  LoadBalancer   10.100.227.254   10.100.227.254   80:30088/TCP             2m24s   name=nginx
	default                service/nginx-nport                 NodePort       10.110.209.213   <none>           80:30080/TCP             2m51s   name=nginx
	kube-system            service/kube-dns                    ClusterIP      10.96.0.10       <none>           53/UDP,53/TCP,9153/TCP   14h     k8s-app=kube-dns
	kubernetes-dashboard   service/dashboard-metrics-scraper   ClusterIP      10.108.139.16    <none>           8000/TCP                 14h     k8s-app=dashboard-metrics-scraper
	kubernetes-dashboard   service/kubernetes-dashboard        ClusterIP      10.100.5.36      <none>           80/TCP                   14h     k8s-app=kubernetes-dashboard

>>	NAMESPACE     NAME                        DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE   CONTAINERS   IMAGES                          SELECTOR
	kube-system   daemonset.apps/kube-proxy   1         1         1       1            1           kubernetes.io/os=linux   14h   kube-proxy   k8s.gcr.io/kube-proxy:v1.20.0   k8s-app=kube-proxy

>>	NAMESPACE              NAME                                        READY   UP-TO-DATE   AVAILABLE   AGE     CONTAINERS                  IMAGES                                SELECTOR
	default                deployment.apps/nginx                       3/3     3            3           2m53s   nginx                       nginx                                 name=nginx
	kube-system            deployment.apps/coredns                     1/1     1            1           14h     coredns                     k8s.gcr.io/coredns:1.7.0              k8s-app=kube-dns
	kubernetes-dashboard   deployment.apps/dashboard-metrics-scraper   1/1     1            1           14h     dashboard-metrics-scraper   kubernetesui/metrics-scraper:v1.0.4   k8s-app=dashboard-metrics-scraper
	kubernetes-dashboard   deployment.apps/kubernetes-dashboard        1/1     1            1           14h     kubernetes-dashboard        kubernetesui/dashboard:v2.1.0         k8s-app=kubernetes-dashboard

>>	NAMESPACE              NAME                                                  DESIRED   CURRENT   READY   AGE     CONTAINERS                  IMAGES                                SELECTOR
	default                replicaset.apps/nginx-85779445d7                      3         3         3       2m53s   nginx                       nginx                                 name=nginx,pod-template-hash=85779445d7
	kube-system            replicaset.apps/coredns-74ff55c5b                     1         1         1       14h     coredns                     k8s.gcr.io/coredns:1.7.0              k8s-app=kube-dns,pod-template-hash=74ff55c5b
	kubernetes-dashboard   replicaset.apps/dashboard-metrics-scraper-c95fcf479   1         1         1       14h     dashboard-metrics-scraper   kubernetesui/metrics-scraper:v1.0.4   k8s-app=dashboard-metrics-scraper,pod-template-hash=c95fcf479
	kubernetes-dashboard   replicaset.apps/kubernetes-dashboard-6cff4c7c4f       1         1         1       14h     kubernetes-dashboard        kubernetesui/dashboard:v2.1.0         k8s-app=kubernetes-dashboard,pod-template-hash=6cff4c7c4f

>>	NAMESPACE   NAME                   CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM         STORAGECLASS   REASON   AGE     VOLUMEMODE
	            persistentvolume/nfs   1Gi        RWX            Retain           Bound    default/nfs   nfs                     2m55s   Filesystem

>>	NAMESPACE   NAME                        STATUS   VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS   AGE     VOLUMEMODE
	default     persistentvolumeclaim/nfs   Bound    nfs      1Gi        RWX            nfs            2m54s   Filesystem

$ curl 10.100.227.254
Hello, World!
```

Once this is running, check that Ingress has got an IP address:
```
kll
	NAME                              CLASS    HOSTS            ADDRESS        PORTS   AGE
	ingress.networking.k8s.io/nginx   <none>   nginx.meia.lan   192.168.49.2   80      3m43s
						   ^^^^^^^^^^^^^^   ^^^^^^^^^^^^
```
So, add such mapping to `/etc/hosts` or to your DNS server.
```
192.168.49.2 	nginx.meia.lan
```

Then, browse for `http://nginx.meia.lan`

# Troubleshooting

### Ingress issues

* Problem:
```
k.describe ingress.networking.k8s.io/nginx
	...
	Default backend:  default-http-backend:80 (<error: endpoints "default-http-backend" not found>)
```
* Solution: a) Ingress is not installed; b) When Ingress is installed, the error continues to raise, dissmiss it.

### DNS problems

* If nginx/pods don't start, there's probably an NFS problem.

	* Check that the NFS service is started and reachable. Run a `k.bash.img nginx` and try an NFS mount.
	* Check that Minikube (`/etc/hosts`) has an entry to `nfs.meia.lan`.
	* Check that CoreDNS is available.
