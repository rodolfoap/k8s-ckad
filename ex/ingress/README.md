# Ingress

## Install: Minikube
```
minikube addons enable ingress
```

## Install: Baremetal
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.43.0/deploy/static/provider/baremetal/deploy.yaml
```

## Dashboard: Minikube
```
minikube dashboard --url &
	Launching proxy ...
	Verifying proxy health ...
	http://127.0.0.1:39997/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/
```

## Dashboard: Baremetal
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
```

## Exposing a Service
```
k.apply ingress.yaml

kc get ingress --all-namespaces

	NAMESPACE              NAME                CLASS    HOSTS         ADDRESS        PORTS   AGE
	kubernetes-dashboard   dashboard-ingress   <none>   ingress.lan   192.168.49.2   80      17s

grep ingress /etc/hosts
	192.168.49.2	ingress.lan
```

## Troubleshooting
```
k.edit     -n kubernetes-dashboard ingress.networking.k8s.io/dashboard-ingress
k.describe -n kubernetes-dashboard ingress.networking.k8s.io/dashboard-ingress
```

Now, just browse for http://ingress.lan
