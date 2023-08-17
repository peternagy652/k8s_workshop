# Exercise VI.

Introduce a Service to route internal traffic.

We have seen that routing or trying to route traffic towards a pod can be problematic if we want to solve it ourselves: Service to the rescue.

## Create a Service for the backend component

A new kind of k8s manifest: `Service`. The aim of a service is to expose a network application that is running as one or more pods in the cluster. It is an abstraction on top of pods/deployments.

In order to properly utilize we need to utilize a `selector` in our Service specification, that is used to determine the group of pods that are exposed by this service.

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