# Ingress

```
minikube dashboard --url &
	Launching proxy ...
	Verifying proxy health ...
	http://127.0.0.1:39997/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/

minikube addons enable ingress

k.apply ingress.yaml

kc get ingress --all-namespaces

	NAMESPACE              NAME                CLASS    HOSTS         ADDRESS        PORTS   AGE
	kubernetes-dashboard   dashboard-ingress   <none>   ingress.lan   192.168.49.2   80      17s

grep ingress /etc/hosts
	192.168.49.2	ingress.lan

```

Now, just browse for http://ingress.lan
