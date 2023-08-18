# Exercise VII.

Create a Deployment and scale up our game.

## What is a deployment?

Basically a blueprint for our deployed components, a recipe. What we have to do is to describe a desired state in a Deployment, the Deployment Controller is going to monitor the actual state and compare it against our desired one at a controlled rate.

## Deployment for the backend

Initially we are going to create a deployment for the backend component.

The goal is to have the deployment applied, that is going to take care of our backend pod.

Lets create a Deployment that creates one pod of the server.


Kubernetes supports multiple rollout strategies for pod deployments. These include:

`Recreate`: Simultaneously terminates and replaces all pods running the old version of the application with new pods.

`Ramped`: Rolls out new application versions while terminating the old pods.

`Rolling update`: Replaces old pods with new ones, one-by-one, with zero downtime.

`Canary deployment`: Replaces a subset of existing pods with new ones, keeping both versions running, and then rolls out the new version to more pods if the test deployment is a success.

## Lets examine this deployment 

Try out different options here:
```
kubectl get deployment <deployment-name> -n <namespace> 
```

```
kubectl get deployment <deployment-name> -n <namespace> -o wide
```

```
kubectl get deployment <deployment-name> -n <namespace> -o yaml
```

## One is not enough

Modify our deployment to deploy 3 replicas. 

Worth to take a look how the created Pods look like, named and behave, especially in conjunction with the previously deployed service.

## Three is too many

We can also use kubectl to modify our deployment at any time.

```
kubectl get deployments -n <namespace>
```
To get all our deployments.

```
kubectl scale --replicas=2 <deploymentname>
```
To scale our deployment to have 2 replicas.

(Worth to try out what happens with 0 replicas.)

## What is happening?

Try to reach your frontend component. We have 1 replica from the frontend, that's quite straightforward, but we have 3 backend replicas. Once you reach your frontend (use port-forwarding) try to add people, and just smash that list button multiple times.

Why?

## If we are already so into it: client

Create a deployment for the frontend component as well, with 3 replicas. 

Add a service for the frontend as well for good measures.

## Port forwarding to client service

Which pod is forwarded?