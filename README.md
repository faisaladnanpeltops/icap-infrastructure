## ICAP Infrastructure

Each of the cluster types that form the Glasswall ICAP System are defined through the Helm charts in the subfolders.

### Adaptation Cluster
Deploying to local cluster (Docker Desktop).

Reset the Kubernetes cluster.

```
cd .\adaptation
```

Create the Kubernetes namespace
```
kubectl create ns icap-adaptation
```

Create container registry secret
```
kubectl create -n icap-adaptation secret docker-registry regcred	\ 
	--docker-server=https://index.docker.io/v1/ 	\
	--docker-username=<username>	\
	--docker-password=<password>	\
	--docker-email=<email address>
```

Install the cluster components
```
helm install . --namespace icap-adaptation --generate-name
```

If required, the following steps provide access to the RabbitMQ Management Console

Open a command prompt into the RabbitMQ Pod
```
kubectl exec --stdin --tty -n icap-adaptation rabbitmq-controller-747n4 -- /bin/bash
```

The cluster's services should now be deployed
```
> kubectl get pods -n icap-adaptation
NAME                                 READY   STATUS    RESTARTS   AGE
adaptation-service-64cc49f99-kwfp6   1/1     Running   0          3m22s
mvp-icap-service-b7ddccb9-gf4z6      1/1     Running   0          3m22s
rabbitmq-controller-747n4            1/1     Running   0          3m22s
```

Enable the Management Plugin, this step takes a couple of minutes
```
rabbitmq-plugins enable rabbitmq_management
```

Exit from the RabbitMQ Pod.
Setup of port forwarding from a local port (e.g. 8080) to the RabbitMQ Management Port
```
kubectl port-forward -n icap-adaptation rabbitmq-controller-747n4 8080:15672
```
The management console now accessible through the browser
```
http://localhost:8080/
```

