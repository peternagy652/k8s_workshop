# Exercise XI.

Using 3rd party components and inject TLS certificates into our Ingress.

## Tackle TLS

First of all we should tackle our fresh Ingress and use it with TLS.

The first step to do that is to create a secret that stores our certificate and our key for that.

The certificate can be fount at `Ex1/Resources/certificates/localhost.crt` and the key at `Ex1/Resources/certificates/localhost.unencrypted.key`.

Use kubectl to create a tls Secret:
```
kubectl create secret tls <secret-name> --cert=Ex1/Resources/certificates/localhost.crt --key=Ex1/Resources/certificates/localhost.unencrypted.key
```

## Use the newly created cert for our ingress

Take the ingress from the last example and add TLS to it that references the Secret that we just created.

The certificate from the first example is valid for multiple SANs: localhost, *.local.dev

This means we also need to change the domain name that we used in the previous example.

Deploy our changes and try our HTTPS connection.

## Cert Manager

Cert Manager is an X.509 certificate management tool for Kubernetes (https://cert-manager.io/).

It will obtain certificates from a variety of Issuers, both popular public Issuers as well as private Issuers, and ensure the certificates are valid and up-to-date, and will attempt to renew certificates at a configured time before expiry.

```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.12.0/cert-manager.yaml
```

Let's explore this tool.

## The Issuer

A CustomResource type that is provided by cert-manager. Issuers, and ClusterIssuers, are Kubernetes resources that represent certificate authorities (CAs) that are able to generate signed certificates by honoring certificate signing requests. All cert-manager certificates require a referenced issuer that is in a ready condition to attempt to honor the request.

An Issuer is a namespaced resource, and it is not possible to issue certificates from an Issuer in a different namespace. This means you will need to create an Issuer in each namespace you wish to obtain Certificates in.

If you want to create a single Issuer that can be consumed in multiple namespaces, you should consider creating a ClusterIssuer resource. This is almost identical to the Issuer resource, however is non-namespaced so it can be used to issue Certificates across all namespaces.

In this example we want to create a CA issuer. We are going to use our own CA to sign all our certificates used by the created Ingresses.

In order to do so, first we need to create a Secret that our issuer can use as a CA certificate, let's call this Secret `root-ca`. The CA certificate itself, and the decrypted private key can be found amongst the resources of the first exercise.

```
kubectl create secret tls root-ca --cert=Ex1/Resources/certificates/ca.cert.pem --key=Ex1/Resources/certificates/ca.decrypted.key
```

Now that we have our `root-ca` created we can create an Issuer using this certificate.

```
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: ca-issuer
spec:
  ca:
    secretName: root-ca
```

Now this Issuer is going to generate and sign our certificates that we need.

Extend our current client Ingress to use this Issuer.

## Letsencrypt staging Issuer

Cert-manager can be used to obtain certificates from a CA using the ACME protocol. The ACME protocol supports various challenge mechanisms which are used to prove ownership of a domain so that a valid certificate can be issued for that domain.

Read about it here: https://cert-manager.io/docs/tutorials/acme/http-validation/