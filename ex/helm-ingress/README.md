curl -s https://get.helm.sh/helm-v3.5.0-linux-amd64.tar.gz	|tar xvfz - --strip-components=1 -C /usr/local/bin/ linux-amd64/helm
or
curl -s https://get.helm.sh/helm-v3.5.0-linux-arm.tar.gz	|tar xvfz - --strip-components=1 -C /usr/local/bin/ linux-arm/helm
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm install ingress-nginx ingress-nginx/ingress-nginx
