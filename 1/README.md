# Assignment 1: Deploy Postgres database using PVC & PV cluster

These are the k8 componets created using yaml files present in [./confs](https://github.com/LF-DevOps-Intern/6_2_opencontainerinitiative-robusgauli-surpriso1997/tree/master/1/confs) directory for deploying a postgres databse:

1. Persistent volume([pv.yml](https://github.com/LF-DevOps-Intern/6_2_opencontainerinitiative-robusgauli-surpriso1997/blob/master/1/confs/pv.yml))

```yml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pg-pv
  namespace: db
spec:
  storageClassName: ""
  persistentVolumeReclaimPolicy: Retain
  accessModes:
    - ReadWriteMany
  capacity:
    storage: 256M
  hostPath:
    path: /db
```

2. Persistent volume claim([pvc.yml](https://github.com/LF-DevOps-Intern/6_2_opencontainerinitiative-robusgauli-surpriso1997/blob/master/1/confs/pvc.yml))

```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pg-pvc
spec:
  storageClassName: ""
  volumeName: pg-pv
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 128M
```

3. Postgres config map for env([pg-config-map.yml](https://github.com/LF-DevOps-Intern/6_2_opencontainerinitiative-robusgauli-surpriso1997/blob/master/1/confs/pg-config-map.yml))

```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: pg-config-map
data:
  POSTGRES_PASSWORD: docker
```

4. Service for connecting to postgres deployment([pg-service.yml](https://github.com/LF-DevOps-Intern/6_2_opencontainerinitiative-robusgauli-surpriso1997/blob/master/1/confs/pg-service.yml))

```yml
apiVersion: v1
kind: Service
metadata:
  name: pg-service
spec:
  type: NodePort
  ports:
    - port: 30432
      targetPort: 5432
      nodePort: 30432
```

5. Postgres deployment with the docker image created from Dockerfile([pg-deployment.yml](https://github.com/LF-DevOps-Intern/6_2_opencontainerinitiative-robusgauli-surpriso1997/blob/master/1/confs/pg-deployment.yml))

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: pg-depl
spec:
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      volumes:
        - name: pg-pv
          persistentVolumeClaim:
            claimName: pg-pvc
      containers:
        - name: postgres
          image: pg-db-image
          imagePullPolicy: Never
          envFrom:
            - configMapRef:
                name: pg-config-map
          volumeMounts:
            - name: pg-pv
              mountPath: /var/lib/postgresql/data
```

6. Dockerfile for creating the postgres image

```Dockerfile
FROM postgres
ENV POSTGRES_PASSWORD docker

```

# Added these components

Those k8 components were created using `k apply -f` command.

1.  Building docker image for deployment
    Before building docker image :

    ```bash

        eval $(minikube docker-env)
    ```

    was run so that the docker image will be picked up by minikube

    ![creating docker image](https://github.com/LF-DevOps-Intern/6_2_opencontainerinitiative-robusgauli-surpriso1997/blob/master/1/screenshots/building-docker-image-for-deployment.png)

2.  Created persistent volume and claim

    ![creating docker image](https://github.com/LF-DevOps-Intern/6_2_opencontainerinitiative-robusgauli-surpriso1997/blob/master/1/screenshots/created-pv-and-pcv.png)

3.  Created deployment

    ![creating docker image](https://github.com/LF-DevOps-Intern/6_2_opencontainerinitiative-robusgauli-surpriso1997/blob/master/1/screenshots/created-deploymnet.png)

4.  Created service

    ![creating docker image](https://github.com/LF-DevOps-Intern/6_2_opencontainerinitiative-robusgauli-surpriso1997/blob/master/1/screenshots/created%20pg%20service.png)

5.  Created config map

    ![creating docker image](https://github.com/LF-DevOps-Intern/6_2_opencontainerinitiative-robusgauli-surpriso1997/blob/master/1/screenshots/created-config-map.png)
