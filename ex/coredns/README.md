k.ns.set kube-system
kubectl get cm coredns -n kube-system -o yaml > coredns.yaml
vi coredns.yaml

kubectl run -it --rm --restart=Never --image=infoblox/dnstools:latest dnstools
---
Patch

k.ns.set kube-system
kubectl get cm     coredns -n kube-system -o yaml > configmap-before.yaml
kubectl get deploy coredns -n kube-system -o yaml > deployment-before.yaml
kubectl patch configmap/coredns --patch "$(cat configmap-patch.yaml)"
kubectl patch deployment/coredns --patch "$(cat deployment-patch.yaml)"


kubectl get cm coredns -n kube-system -o yaml > coredns.yaml
kubectl patch deployment patch-demo --patch "$(cat patch-file.yaml)"

kubectl patch configmap/coredns --patch "$(cat configmap-patch.yaml)"
