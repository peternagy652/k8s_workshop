# Exercise IX.

Store data and configuration as Secrets.

## What are Secrets

A Secret is an object that contains a small amount of sensitive data such as a password, a token, or a key. Such information might otherwise be put in a Pod specification or in a container image. Using a Secret means that you don't need to include confidential data in your application code.

Secrets are similar to ConfigMaps but are specifically intended to hold confidential data.

Kubernetes Secrets are, by default, stored unencrypted in the API server's underlying data store (etcd). Anyone with API access can retrieve or modify a Secret, and so can anyone with access to etcd. Additionally, anyone who is authorized to create a Pod in a namespace can use that access to read any Secret in that namespace; this includes indirect access such as the ability to create a Deployment.

Built-in Secrt types:
|Built-in type | Usage|
| --- | --- |
| Opaque | arbitrary user-defined data |
| kubernetes.io/service-account-token | ServiceAccount token |
| kubernetes.io/dockercfg | serialized ~/.dockercfg file |
| kubernetes.io/dockerconfigjson | serialized ~/.docker/config.json file |
| kubernetes.io/basic-auth | credentials for basic authentication |
| kubernetes.io/ssh-auth | credentials for SSH authentication |
| kubernetes.io/tls | data for a TLS client or server |
| bootstrap.kubernetes.io/token | bootstrap token data |

Individual secrets are limited to 1MiB in size. This is to discourage creation of very large secrets that could exhaust the API server and kubelet memory. However, creation of many smaller secrets could also exhaust memory. You can use a resource quota to limit the number of Secrets (or other resources) in a namespace.

## Create a Secret

Really familiar, just like we created a ConfigMap. If we don't specify a type the default is going to be `Opaque`.

Let's give it a shot, create a secret for our postgre deployment, add POSTGRES_USER, POSTGRES_PASSWORD and POSTGRES_DB to our secret.

Don't forget to base64 encode your actual values.

## Use this Secret

We use these values at two places in our current deployment: the database deployment and our server deployment.

Modify both of these deployments to utilize the secret file.

This way, we don't have to store these configuration like information together with our deployment files, and also since multiple manifests can reference these Secrets we don't need to modify at multiple places if we want to change things.

Deploy everything and verify everything is working as expected.§

## Dotfiles as Secret

You can make your data "hidden" by defining a key that begins with a dot. This key represents a dotfile or "hidden" file.

Files beginning with dot characters are hidden from the output of ls -l; you must use ls -la to see them when listing directory contents.

See `dotfile_example.yaml` amongst the resources to this exercise.

## Certificates as Secret

Kubernetes provides a builtin Secret type kubernetes.io/tls for storing a certificate and its associated key that are typically used for TLS.

One common use for TLS secrets is to configure encryption in transit for an Ingress, but you can also use it with other resources or directly in your workload. When using this type of Secret, the tls.key and the tls.crt key must be provided in the data (or stringData) field of the Secret configuration, although the API server doesn't actually validate the values for each key.

This is just a convenience thing, and Opaque Secret can be user similarly.

## Create Secret from CLI

Secrets can be conveniently created using kubectl as well.

Generic Opaque Secrets from literals:
```
kubectl create secret generic <secret-name> --type='Opaque' --from-literal=<key>=<value>
```
Using multiple literals by just repeating the `--from-literal` section.

Generic Secrets from files:
```
kubectl create secret generic <secret-name> --from-file=<key>=<path/to/file>
```
You can even compine `--from-file` and `--from-literal` the result is going to be a concatenation of the two.

TLS secret:
```
kubectl create secret tls <secret-name> --cert=<path/to/crt/file> --key=<path/to/key/file>
```

## Immutable Secrets

Kubernetes lets you mark specific Secrets (and ConfigMaps) as immutable. Preventing changes to the data of an existing Secret has the following benefits:

* protects you from accidental (or unwanted) updates that could cause applications outages
* (for clusters that extensively use Secrets - at least tens of thousands of unique Secret to Pod mounts), switching to immutable Secrets improves the performance of your cluster by significantly reducing load on kube-apiserver. The kubelet does not need to maintain a [watch] on any Secrets that are marked as immutable.

```
apiVersion: v1
kind: Secret
metadata: ...
data: ...
immutable: true
```

## Container image pull secrets

If you want to fetch container images from a private repository, you need a way for the kubelet on each node to authenticate to that repository. You can configure image pull secrets to make this possible. These secrets are configured at the Pod level.

The imagePullSecrets field is a list of references to secrets in the same namespace. You can use an imagePullSecrets to pass a secret that contains a Docker (or other) image registry password to the kubelet. The kubelet uses this information to pull a private image on behalf of your Pod. 

You can manually create imagePullSecrets, and reference these from a ServiceAccount. Any Pods created with that ServiceAccount or created with that ServiceAccount by default, will get their imagePullSecrets field set to that of the service account.

(More on ServiceAccount in Exercise XVII.)

## Information security for Secrets

Just as we mentioned already, by default Secrets are unencrypted, and stored this way in etcd.

To safely store Secrets that really has to be secrets:
* Enable Encryption at Rest for Secrets: https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/
* Enable or configure RBAC rules with least-privilege access to Secrets: https://kubernetes.io/docs/reference/access-authn-authz/authorization/
* Restrict Secret access to specific containers.
* Use external Secret store providers: https://secrets-store-csi-driver.sigs.k8s.io/concepts.html#provider-for-the-secrets-store-csi-driver

There are good/best practices for handling Secrets: https://kubernetes.io/docs/concepts/security/secrets-good-practices/