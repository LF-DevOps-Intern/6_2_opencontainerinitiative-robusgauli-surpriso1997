# Assignment 2: Deploy Postgres Client in cluster(psql) and Connect Postgres database from Postgres Client using core-dns's host name:

2 conf files for postgres client deployment and for config maps are created.

1. Postgres client conf file:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pg-client-depl
spec:
  selector:
    matchLabels:
      app: pg-client
  template:
    metadata:
      labels:
        app: pg-client
    spec:
      containers:
        - name: pg-client
          image: postgres
          envFrom:
            - configMapRef:
                name: pg-client-config-map
```

2. Postgres client config map

```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: pg-client-config-map
data:
  POSTGRES_PASSWORD: docker
```

3. These conf files are applied

![running confi files](https://github.com/LF-DevOps-Intern/6_2_opencontainerinitiative-robusgauli-surpriso1997/blob/master/2/screenshots/created-client-config-map-and-deployment.png)

4. Running psql :

![running psql](https://github.com/LF-DevOps-Intern/6_2_opencontainerinitiative-robusgauli-surpriso1997/blob/master/2/screenshots/running-psql-in%3Dk8.png)
