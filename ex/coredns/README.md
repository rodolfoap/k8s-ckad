k.ns.set kube-system
kubectl get cm coredns -n kube-system -o yaml > coredns.yaml
vi coredns.yaml

kubectl run -it --rm --restart=Never --image=infoblox/dnstools:latest dnstools
---
Patch

kubectl get cm coredns -n kube-system -o yaml > coredns.yaml
kubectl patch deployment patch-demo --patch "$(cat patch-file.yaml)"

kubectl patch configmap/coredns --patch "$(cat configmap-patch.yaml)"
