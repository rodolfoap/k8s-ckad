# CKAD Crash Course

Solutions to https://github.com/bmuschko/ckad-crash-course.

Check the guide [here](https://github.com/rodolfoap/k8s-ckad/blob/master/slides.pdf).

## Additional Resources

* [Certified Kubernetes Application Developer (CKAD) Study Guide](https://learning.oreilly.com/library/view/certified-kubernetes-application/9781492083726/)
* [LinkedIn Learning: Certified Kubernetes Application Developer (CKAD) Cert Prep: Exam Tips](https://www.linkedin.com/learning/certified-kubernetes-application-developer-ckad-cert-prep-exam-tips)
* [Blog Post About my Experiences](https://bmuschko.com/blog/ckad-prep/)
* [CKAD Exercises by Dimitris-Ilias Gkanatsios](https://github.com/dgkanatsios/CKAD-exercises)
* [Katacoda Practice Questions by Liptan Biswas](https://dev.to/liptanbiswas/ckad-practice-questions-4mpn)
* [Golden Guide to Kubernetes Application Development](https://matthewpalmer.net/kubernetes-app-developer)
* [KodeKloud CKAD Course](https://kodekloud.com/p/kubernetes-certification-course)

==============================================================

# Exercise 1: creating-inspecting-pod

In this exercise, you will practice the creation of a new Pod in a namespace. Once created, you will inspect it, shell into it and run some operations inside of the container.

## Creating a Pod and Inspecting it

1. Create the namespace `ckad-prep`.
2. In the namespace `ckad-prep` create a new Pod named `mypod` with the image `nginx:2.3.5`. Expose the port 80.
3. Identify the issue with creating the container. Write down the root cause of issue in a file named `pod-error.txt`.
4. Change the image of the Pod to `nginx:1.15.12`.
5. List the Pod and ensure that the container is running.
6. Log into the container and run the `ls` command. Write down the output. Log out of the container.
7. Retrieve the IP address of the Pod `mypod`.
8. Run a temporary Pod using the image `busybox`, shell into it and run a `wget` command against the `nginx` Pod using port 80.
9. Render the logs of Pod `mypod`.
10. Delete the Pod and the namespace.

==============================================================

# Exercise 2: creating-using-configmap

In this exercise, you will first create a ConfigMap from predefined values in a file. Later, you'll create a Pod, consume the ConfigMap as environment variables and print out its values from within the container.

## Configuring a Pod to Use a ConfigMap

1. Create a new file named `config.txt` with the following environment variables as key/value pairs on each line.

- `DB_URL` equates to `localhost:3306`
- `DB_USERNAME` equates to `postgres`

2. Create a new ConfigMap named `db-config` from that file.
3. Create a Pod named `backend` that uses the environment variables from the ConfigMap and runs the container with the image `nginx`.
4. Shell into the Pod and print out the created environment variables. You should find `DB_URL` and `DB_USERNAME` with their appropriate values.
5. (Optional) Discuss: How would you approach hot reloading of values defined by a ConfigMap consumed by an application running in Pod?

==============================================================

# Exercise 3: creating-using-secret

In this exercise, you will first create a Secret from literal values. Next, you'll create a Pod and consume the Secret as environment variables. Finally, you'll print out its values from within the container.

## Configuring a Pod to Use a Secret

1. Create a new Secret named `db-credentials` with the key/value pair `db-password=passwd`.
2. Create a Pod named `backend` that uses the Secret as environment variable named `DB_PASSWORD` and runs the container with the image `nginx`.
3. Shell into the Pod and print out the created environment variables. You should find `DB_PASSWORD` variable.
4. (Optional) Discuss: What is one of the benefit of using a Secret over a ConfigMap?

==============================================================

# Exercise 4: creating-securitycontext

In this exercise, you will create a Pod that defines a filesystem group ID as security context. Based on this security context, you'll create a new file and inspect the outcome of the file creation based on the rule defined earlier.

## Creating a Security Context for a Pod

1. Create a Pod named `secured` that uses the image `nginx` for a single container. Mount an `emptyDir` volume to the directory `/data/app`.
2. Files created on the volume should use the filesystem group ID 3000.
3. Get a shell to the running container and create a new file named `logs.txt` in the directory `/data/app`. List the contents of the directory and write them down.

==============================================================

# Exercise 5: defining-resource-requirements

In this exercise, you will create a ResourceQuota with specific CPU and memory limits for a new namespace. Pods created in the namespace will have to adhere to those limits. 

## Defining a Pod’s Resource Requirements

Create a resource quota named `app` under the namespace `rq-demo` using the following YAML definition in the file `rq.yaml`.

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: app
spec:
  hard:
    pods: "2"
    requests.cpu: "2"
    requests.memory: 500m
```

1. Create a new Pod that exceeds the limits of the resource quota requirements e.g. by defining 1G of memory. Write down the error message.
2. Change the request limits to fulfill the requirements to ensure that the Pod could be created successfully. Write down the output of the command that renders the used amount of resources for the namespace.

==============================================================

# Exercise 6: using-service-account

In this exercise, you will create a ServiceAccount and assign it to a Pod.

## Using a ServiceAccount

1. Create a new service account named `backend-team`.
2. Print out the token for the service account in YAML format.
3. Create a Pod named `backend` that uses the image `nginx` and the identity `backend-team` for running processes.
4. Get a shell to the running container and print out the token of the service account.

==============================================================

# Exercise 7: creating-init-container

In this exercise, you will initialize a web application by standing up environment-specific configuration through an init container.

## Creating an Init Container

Kubernetes runs an init container before the main container. In this scenario, the init container retrieves configuration files from a remote location and makes it available to the application running in the main container. The configuration files are shared through a volume mounted by both containers. The running application consumes the configuration files and can render its values.

1. Create a new Pod in a YAML file named `business-app.yaml`. The Pod should define two containers, one init container and one main application container. Name the init container `configurer` and the main container `web`. The init container uses the image `busybox`, the main container uses the image `bmuschko/nodejs-read-config:1.0.0`. Expose the main container on port 8080.
2. Edit the YAML file by adding a new volume of type `emptyDir` that is mounted at `/usr/shared/app` for both containers.
3. Edit the YAML file by providing the command for the init container. The init container should run a `wget` command for downloading the file `https://raw.githubusercontent.com/bmuschko/ckad-crash-course/master/exercises/07-creating-init-container/app/config/config.json` into the directory `/usr/shared/app`.
4. Start the Pod and ensure that it is up and running.
5. Run the command `curl localhost:8080` from the main application container. The response should render a database URL derived off the information in the configuration file.
6. (Optional) Discuss: How would you approach a debugging a failing command inside of the init container?

==============================================================

# Exercise 8: implementing-adapter-pattern

In this exercise, you will implement the adapter pattern for a multi-container Pod.

## Implementing the Adapter Pattern

The adapter pattern helps with providing a simplified, homogenized view of an application running within a container. For example, we could stand up another container that unifies the log output of the application container. As a result, other monitoring tools can rely on a standardized view of the log output without having to transform it into an expected format.

1. Create a new Pod in a YAML file named `adapter.yaml`. The Pod declares two containers. The container `app` uses the image `busybox` and runs the command `while true; do echo "$(date) | $(du -sh ~)" >> /var/logs/diskspace.txt; sleep 5; done;`. The adapter container `transformer` uses the image `busybox` and runs the command `sleep 20; while true; do while read LINE; do echo "$LINE" | cut -f2 -d"|" >> $(date +%Y-%m-%d-%H-%M-%S)-transformed.txt; done < /var/logs/diskspace.txt; sleep 20; done;` to strip the log output off the date for later consumption by a monitoring tool. Be aware that the logic does not handle corner cases (e.g. automatically deleting old entries) and would look different in production systems.
2. Before creating the Pod, define an `emptyDir` volume. Mount the volume in both containers with the path `/var/logs`.
3. Create the Pod, log into the container `transformer`. The current directory should continuously write a new file every 20 seconds.

==============================================================

# Exercise 9: defining-pod-probes

In this exercise, you will create a Pod running a NodeJS application. The Pod will define readiness and liveness probes with different parameters.

## Defining a Pod’s Readiness and Liveness Probe

1. Create a new Pod named `hello` with the image `bmuschko/nodejs-hello-world:1.0.0` that exposes the port 3000. Provide the name `nodejs-port` for the container port.
2. Add a Readiness Probe that checks the URL path / on the port referenced with the name `nodejs-port` after a 2 seconds delay. You do not have to define the period interval.
3. Add a Liveness Probe that verifies that the app is up and running every 8 seconds by checking the URL path / on the port referenced with the name `nodejs-port`. The probe should start with a 5 seconds delay.
4. Shell into container and curl `localhost:3000`. Write down the output. Exit the container.
5. Retrieve the logs from the container. Write down the output.

==============================================================

# Exercise 10: debugging-misconfigured-pod

In this exercise, you will training your debugging skills by inspecting and fixing a misconfigured Pod.

## Fixing a Misconfigured Pod

1. Create a new Pod with the following YAML.

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: failing-pod
  name: failing-pod
spec:
  containers:
  - args:
    - /bin/sh
    - -c
    - while true; do echo $(date) >> ~/tmp/curr-date.txt; sleep
      5; done;
    image: busybox
    name: failing-pod
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

2. Check the Pod's status. Do you see any issue?
3. Follow the logs of the running container and identify an issue.
4. Fix the issue by shelling into the container. After resolving the issue the current date should be written to a file. Render the output.

==============================================================

# Exercise 11: using-labels-annotations

In this exercise, you will exercise the use of labels and annotations for a set of Pods.

## Defining and Querying Labels and Annotations

1. Create three different Pods with the names `frontend`, `backend` and `database` that use the image `nginx`.
2. Declare labels for those Pods as follows:

- `frontend`: `env=prod`, `team=shiny`
- `backend`: `env=prod`, `team=legacy`, `app=v1.2.4`
- `database`: `env=prod`, `team=storage`

3. Declare annotations for those Pods as follows:

- `frontend`: `contact=John Doe`, `commit=2d3mg3`
- `backend`: `contact=Mary Harris`

4. Render the list of all Pods and their labels.
5. Use label selectors on the command line to query for all production Pods that belong to the teams `shiny` and `legacy`.
6. Remove the label `env` from the `backend` Pod and rerun the selection.
7. Render the surrounding 3 lines of YAML of all Pods that have annotations.

==============================================================

# Exercise 12: rolling-updates-deployment

In this exercise, you will create a Deployment with multiple replicas. After inspecting the Deployment, you will update its parameters. Furthermore, you will use the rollout history to roll back to a previous revision.

## Performing Rolling Updates for a Deployment

1. Create a Deployment named `deploy` with 3 replicas. The Pods should use the `nginx` image and the name `nginx`. The Deployment uses the label `tier=backend`. The Pods should use the label `app=v1`.
2. List the Deployment and ensure that the correct number of replicas is running.
3. Update the image to `nginx:latest`.
4. Verify that the change has been rolled out to all replicas.
5. Scale the Deployment to 5 replicas.
6. Have a look at the Deployment rollout history.
7. Revert the Deployment to revision 1.
8. Ensure that the Pods use the image `nginx`.
9. (Optional) Discuss: Can you foresee potential issues with a rolling deployment? How do you configure a update process that first kills all existing containers with the current version before it starts containers with the new version?

==============================================================

# Exercise 13: creating-cronjob

In this exercise, you will create a CronJob and render its executions.

## Creating a Scheduled Container Operation

1. Create a CronJob named `current-date` that runs every minute and executes the shell command `echo "Current date: $(date)"`.
2. Watch the jobs as they are being scheduled.
3. Identify one of the Pods that ran the CronJob and render the logs.
4. Determine the number of successful executions the CronJob will keep in its history.
5. Delete the Job.

==============================================================

# Exercise 14: exposing-service

In this exercise, you will create a Deployment and expose a container port for its Pods. You will demonstrate the differences between the service types ClusterIP and NodePort.

## Routing Traffic to Pods from Inside and Outside of a Cluster

1. Create a Service named `myapp` of type `ClusterIP` that exposes port 80 and maps to the target port 80.
2. Create a Deployment named `myapp` that creates 1 replica running the image `nginx`. Expose the container port 80.
3. Scale the Deployment to 2 replicas.
4. Create a temporary Pods using the image `busybox` and run a `wget` command against the IP of the service.
5. Change the service type so that the Pods can be reached from outside of the cluster.
6. Run a `wget` command against the service from outside of the cluster.
7. (Optional) Discuss: Can you expose the Pods as a service without a deployment?
8. (Optional) Discuss: Under what condition would you use the service type `LoadBalancer`?

==============================================================

# Exercise 15: creating-networkpolicy

In this exercise, you will set up a NetworkPolicy to restrict access to and from a Pod.

## Restricting Access to and from a Pod

Let's assume we are working on an application stack that defines three different layers: a frontend, a backend and a database. Each of the layers runs in a Pod. You can find the definition in the YAML file `app-stack.yaml`. The application needs to run in the namespace `app-stack`.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: frontend
  namespace: app-stack
  labels:
    app: todo
    tier: frontend
spec:
  containers:
    - name: frontend
      image: nginx

---

kind: Pod
apiVersion: v1
metadata:
  name: backend
  namespace: app-stack
  labels:
    app: todo
    tier: backend
spec:
  containers:
    - name: backend
      image: nginx

---

kind: Pod
apiVersion: v1
metadata:
  name: database
  namespace: app-stack
  labels:
    app: todo
    tier: database
spec:
  containers:
    - name: database
      image: mysql
      env:
      - name: MYSQL_ROOT_PASSWORD
        value: example
```

1. Create the required namespace.
2. Copy the Pod definition to the file `app-stack.yaml` and create all three Pods. Notice that the namespace has already been defined in the YAML definition.
3. Create a network policy in the YAML file `app-stack-network-policy.yaml`.
4. The network policy should allow incoming traffic from the backend to the database but disallow incoming traffic from the frontend.
5. Incoming traffic to the database should only be allowed on TCP port 3306 and no other port.

==============================================================

# Exercise 16: creating-persistentvolume

In this exercise, you will create a PersistentVolume, connect it to a PersistentVolumeClaim and mount the claim to a specific path of a Pod.

## Defining and Mounting a PersistentVolume

1. Create a Persistent Volume named `pv`, access mode `ReadWriteMany`, storage class name `shared`, 512MB of storage capacity and the host path `/data/config`.
2. Create a Persistent Volume Claim named `pvc` that requests the Persistent Volume in step 1. The claim should request 256MB. Ensure that the Persistent Volume Claim is properly bound after its creation.
3. Mount the Persistent Volume Claim from a new Pod named `app` with the path `/var/app/config`. The Pod uses the image `nginx`.
4. Check the events of the Pod after starting it to ensure that the Persistent Volume was mounted properly.

