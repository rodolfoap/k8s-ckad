# CoreDNS Adding DNS Zone

```
k.ns.set kube-system

# Save old config
kubectl get cm     coredns -n kube-system -o yaml > configmap-before.yaml
kubectl get deploy coredns -n kube-system -o yaml > deployment-before.yaml

# Patch config
kubectl patch configmap/coredns --patch "$(cat configmap-patch.yaml)"
kubectl patch deployment/coredns --patch "$(cat deployment-patch.yaml)"

# Save new config
kubectl get cm     coredns -n kube-system -o yaml > configmap-after.yaml
kubectl get deploy coredns -n kube-system -o yaml > deployment-after.yaml

# Check differences
sdiff -w 220 configmap-before.yaml configmap-after.yaml|less
sdiff -w 220 deployment-before.yaml deployment-after.yaml|less

# Test DNS
kubectl run -it --rm --restart=Never --image=infoblox/dnstools:latest dnstools

	# host meia.lan
		meia.lan has address 192.168.1.50

	# ping -c1 meia.lan
		PING meia.lan (192.168.1.50): 56 data bytes
		64 bytes from 192.168.1.50: seq=0 ttl=63 time=0.185 ms
		--- meia.lan ping statistics ---
		1 packets transmitted, 1 packets received, 0% packet loss
		round-trip min/avg/max = 0.185/0.185/0.185 ms
```
