# Excercise I.

Run the backend and frontend locally and try it out with inmemory and postgres repositories.

## Backend

A go based simple webserver component, that provides CRUD for person objects.
```
Person {
    ID: string,
    Name: string,
    Age: int,
}
```

The repository is accessible at: https://github.com/peternagy652/k8s_sample_backend

Use a simple `make` to build the backend, and create the `k8s_sample_backend` docker image.

## Frontend

A TS based simple webapp using react (definitely overkill). Can be used to generate/list/delete people.

The repository is accessible at: https://github.com/peternagy652/k8s_sample_frontend

Use a simple `make` to build the backend, and create the `k8s_sample_frontend` docker image.

## Database

Plain old simple postgre, 15.4 in this case.
The backend component is going to generate it's own schema on startup, don't worry.

## Start inmemory version

Use `docker compose -f docker-compose_inmemory.yaml up` to start the stack.

## Start postgre version

Use `docker compose -f docker-compose_postgre.yaml up` to start the stack.

## Usage

In order to avoid TLS problems (we are using self signed certificates after all), add the rootCA (`/certificates/ca.cert.pem`) and the dev certificate (`/certificates/localhost.crt`) and set them to `Always trust`

Access the frontend in both cases at `https://localhost:443`