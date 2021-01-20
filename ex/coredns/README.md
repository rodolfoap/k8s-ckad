k.ns.set kube-system
kubectl get cm coredns -n kube-system -o yaml > coredns.yaml
vi coredns.yaml

kubectl run -it --rm --restart=Never --image=infoblox/dnstools:latest dnstools
