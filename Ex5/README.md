# Excercise V.

Getting familiar with ConfigMaps and deploy even more stuff.

## ConfigMap for the frontend

We need to create a ConfigMap (https://kubernetes.io/docs/concepts/configuration/configmap/) for our frontend component in order to supply the nginx configuration.

ConfigMaps can be created directly from files as well:
```
kubectl create configmap NAME [--type=string] [--from-file=[key=]source] [--from-literal=key1=value1] [--dry-run]
```

## Mount the ConfigMap

The ConfigMap has to be also mounted to the frontend pod.

We need to specify a volume that references our ConfigMap.

Additionally we also need to mount the volume to the container by specifying a `mountPath` and this case a `subPath` as well.

## Why this should not work?

Pods are stateless and ephemeral.

What happens on a pod restart?

What happens if we scale up our component?
