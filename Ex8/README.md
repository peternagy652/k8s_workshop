# Exercise VIII.

Add a database to our project, as a single pod/deployment for the time beeing.

## Deployment for the DB

We are using postgre, thats clear, we are using the same version that we used in the first example: `postgres:15.4`

Let's create the deployment for it, keep it simple for now, have only 1 replica.

Don't forget about the environment variables.

## Create a service for our DB

Just to make it accessible from other pods easily. Nothing new, nothing fancy.

## What about the server component

In order to utilize our fresh database we need to update our server deployment.

Nothing extra, just add the necessary environemnt variables.

We don't need to delete our previous deployment or resources. We just need to apply the new configuration for our server component, and kubernetes is going to take care of the rest.

After we redeployed our server component let's take a look on our deployments once more.

To be more precise let's take a look on our ReplicaSets

```
kubectl get replicasets -n <namespace>
```

`Deployment`: Higher level abstraction that manages replica sets. Manages a template of pods and uses replica sets to ensure the right amount. Provides a mechanism for rolling updates to reduce downtime. Provides versioning to manage multiple versions of the same application.

`ReplicaSet`: Lower level abstraction that manages the desired number of replicas of a pod. Only  manages replicas of a pod. Has to be manually updated or rolled back. Doesn't provide versioning.

## I already heard about StatefulSets

Yep, not going to do it yet, but we have Ex12 and Ex16 for that.

## Messing around

Scale up our database replicas to three, and smash that list button again on the frontend. Not surprised anymore. 

Or are we?