## Exercise XI.

Using 3rd party components and inject TLS certificates into our Ingress.

Dont forget to create the TLS cert first

kubectl create secret tls tls-secret --cert=Ex1/Resources/certificates/localhost.crt --key=Ex1/Resources/certificates/localhost.unencrypted.key