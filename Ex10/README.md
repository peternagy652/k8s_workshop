# Exercise X.

Getting tired of port forwarding, let's use Ingresses to route trafic from outside the cluster.

## Ingress

Ingress exposes HTTP and HTTPS routes from outside the cluster to services within the cluster. Traffic routing is controlled by rules defined on the Ingress resource.

An Ingress may be configured to give Services externally-reachable URLs, load balance traffic, terminate SSL / TLS, and offer name-based virtual hosting. An Ingress controller is responsible for fulfilling the Ingress, usually with a load balancer, though it may also configure your edge router or additional frontends to help handle the traffic.

An Ingress does not expose arbitrary ports or protocols. Exposing services other than HTTP and HTTPS to the internet typically uses a service of type Service.Type=NodePort or Service.Type=LoadBalancer.

You must have an Ingress controller to satisfy an Ingress. Only creating an Ingress resource has no effect.

You may need to deploy an Ingress controller such as ingress-nginx. You can choose from a number of Ingress controllers: https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/

## Exposing a service without an ingress

We can configure a Service to have a `NodePort` type. This exposes the same port on all the nodes in the cluster, and if we know the external IP address of a Node we can reach this Service.

Change the type of our current `client-service` to NodePort, and try it out in a browser.

Get the right port for this:
```
kubectl get service <servicename>
```

The output should look similar to: 
| NAME | TYPE | CLUSTER-IP | EXTERNAL-IP | PORT(S) | AGE |
| --- | --- | --- | --- | --- | --- |
| client | NodePort | 10.43.93.22 | <none> | 443:32354/TCP | 11m | 

Oh, we need the IP address of our Node in order to access it, we can use kubectl to figure it out:
```
kubectl get nodes -o wide
```

The output should look like something similar:
| NAME | STATUS | ROLES | AGE | VERSION | INTERNAL-IP | EXTERNAL-IP | OS-IMAGE | KERNEL-VERSION | CONTAINER-RUNTIME |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| lima-rancher-desktop | Ready | control-plane,master | 71d | v1.26.5+k3s1 | 192.168.5.15 | 192.168.0.132 | Alpine Linux v3.16 | 5.15.96-0-virt | containerd://1.6.8 |

Use the `External-IP` to access the Service from your browser, in this case it would be: `192.168.0.132:32354`

Aaaaaand it's not working... Damned nginx configuration, let's change that! And don't forget about changing the service as well to serve the http port! (Look at the resources) 

After our changes it should work. Also worth mentioning, that the same thing can be done using `http://192-168-0-132.nip.io:32354/`, the same thing, but with more steps.

## Do it properly this time

First of all, we need an IngressController, otherwise we are not going to be able to satisfy our ingress.

For the sake of simplicity, we are going to use `ingress-nginx` from https://github.com/kubernetes/ingress-nginx/tree/main.

Conveniently they already provide a deployment for their ingress controller for baremetal clusters: https://github.com/kubernetes/ingress-nginx/blob/main/deploy/static/provider/baremetal/deploy.yaml

The same "deployment" (if you look at the file it's way more then just a deployment) can be found in the resources folder. Inspect it and apply it.

This is going to create it's own Namespace, ServiceAccounts, Roles, ClusterRoles, RoleBindings, ClusterRoleBindings (more about these in the RBAC Exercise XVII.), Services, Deployments, Jobs ...

## It's Ingress time

We can create our Ingress resource now, let's prepare the manifest for that. (And use the time to set back our client-service to ClusterIP as well, we don't need it to be exposed any further.)

After we deployed our ingress try it out.

Ouch... DNS_PROBE_FINISHED_NXDOMAIN :(

We don't have a DNS server to configure but we always have our `etc/hosts` file. It's time to edit it.

We already figured out previously the External-IP for our kubernetes cluster (the Node really, but since we only have one node that makes our cluster, it's the same).

Just append at the end of your host file a line:
```
<YourExternalIP> client.local.info
```
In case facing HSTS issues with Chrome: chrome://net-internals/#hsts

## And we are not even close

There are a bunch of other things worth learning about Ingresses, a good startingpoint: https://kubernetes.io/docs/concepts/services-networking/ingress/