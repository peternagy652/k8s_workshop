# Excercise IV.

Getting familiar with kubectl and deploy our first "something" to our fresh cluster.

We assume at this point (thanks to Excercise II.) that you know a thing or two already, what is a namespace, what is a pod, and such things.

## Create a new namespace in your fresh cluster

We need a place to deploy our stuff to, we don't really want to use the default namespace, let's create one now.

```
kubectl create namespace <name-your-namespace>
```

## Have the backend working in a pod

Create your first kubernetes manifest.

The backend image can be pulled from: peternagy652/k8s_sample_backend:1.0.0

Set the backend to use inmemory storage for the time beeing.

The goal is to end up with a kubernetes manifest that contains the definition for the pod, let's call it `backend.yaml`

Once this is ready, we can use kubectl to deploy it.

```
kubectl apply -n <your-namespace> -f <path-to-backend.yaml>
```

## How to check if it is working

Once we deployed our new little pod to our cluster, we are interested in how does it perform, what does it do. How to check this?

### Using kubectl

Listing the pods:
```
kubectl get pods -n <your-namespace> [-o wide]
```

Getting the logs:
```
kubectl logs -f <pod-name> -n <your-namespace>
```
(secretly the same thing as `kibectl logs [pod-name] --all-containers=true`)

If there are multiple containers in a pod, we can even specify the container that we are interested in:
```
kubectl logs [POD name] [-c CONTAINER name] [--follow] [flags]
```

Get a lot of information about a specific pod:
```
kubectl get pod <pod-name> -n <namespace> -o yaml
```

### Using Lens

Just navigate to the pod, and use some buttons.

## Interact with the pod

It's great, it's working, how to do something with it, or how to interact with it?

### Exec into pod
```
kubectl exec --stdin --tty <pod-name> -n <namespace> -- /bin/sh
```

### Port forward
```
kubectl port-forward -n <namespace> <pod-name> [localport:remoteport]
```

If you leave localport empty, kubectl is going to select one for you.

## Tired already switching namespaces and contexts?

Give `kubectx`, `kubent` and `kubens`.