# Exercise VI.

Introduce a Service to route internal traffic.

We have seen that routing or trying to route traffic towards a pod can be problematic if we want to solve it ourselves: Service to the rescue.

## Create a Service for the backend component

A new kind of k8s manifest: `Service`. The aim of a service is to expose a network application that is running as one or more pods in the cluster. It is an abstraction on top of pods/deployments.

In order to properly utilize we need to utilize a `selector` in our Service specification, that is used to determine the group of pods that are exposed by this service.

## Let's examine

Use
```
kubectl get services -n <namespace>
```
To get the services.

Examine and compare the result of the following:

```
kubectl get service <servicename> -n <namespace>
```

```
kubectl get service <servicename> -n <namespace> -o wide
```

```
kubectl get service <servicename> -n <namespace> -o yaml
```

Compare our findings to a Pod.

Take a close attention to the networking part, and the type for the networking.

`ClusterIP`: the default for the created services. True to it's name, it's a unique IP address across the cluster, and the service can be reached through that.

`NodePort`: A NodePort differs from the ClusterIP in the sense that it exposes a port in each Node. When a NodePort is created, kube-proxy exposes a port in the range 30000-32767.

`LoadBalancer`: Creates a service like ClusterIP, opens a port on every node like NodePort, but uses a LoadBalancer implementation from your provider.

`ExternalName`: The ExternalName service was introduced due to the need of connecting to an element outside of the Kubernetes cluster. Think of it not as a way to connect to an item within your cluster, but as a connector to an external element of the cluster. Creates a single endpoint for all communications to that element. You need to specify the `externalName` besides just the type.

## Verification

It is worth to verify that we are now able to reach our backend component from our frontend part through the newly created Service.

Exec into your frontend pod and use curl to reach the backend:
```
curl https://server:8443/api/v1/hello
```

Similarly now we should be able to reach our frontend component as well:
```
kubectl port-forward client 1443:443
```

(If you are using chrome and already disabled default hsts for localhost, you need to explicitly specify https otherwise nginx is going to be mad)