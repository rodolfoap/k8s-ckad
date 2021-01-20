* See https://www.youtube.com/watch?v=X48VuDVv0do&t=4576s

## Usage

```
	k.apply secret.yaml
	k.apply configmap.yaml
	k.apply deployment-database.yaml
	k.apply deployment-frontend.yaml
	k.apply service-database.yaml
	k.apply service-frontend.yaml

	minikube service frontend

		|-----------|----------|-------------|---------------------------|
		| NAMESPACE |   NAME   | TARGET PORT |            URL            |
		|-----------|----------|-------------|---------------------------|
		| default   | frontend |        8081 | http://192.168.49.2:30000 |
		|-----------|----------|-------------|---------------------------|

		 Opening service default/frontend in default browser...
```
