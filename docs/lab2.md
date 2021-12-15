---
title: 2. Deploy MySQL
---

# Lab 2: Create MySQL deployment

In the Docker Getting Started this was the command to start MySQL.

**Do not run this command!** It is only here for reference.

```
docker run -d \
    --network todo-app --network-alias mysql \
    -v todo-mysql-data:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=secret \
    -e MYSQL_DATABASE=todos \
    mysql:5.7
```

* In Kubernetes we don't need a network and network-alias
* Kubernetes has volumes, too, but for simplicities sake we will start without volumes (but add them later)
* The container image is `mysql:5.7` and is located on Docker Hub
* There are two environment variables, MYSQL_ROOT_PASSWORD and MYSQL_DATABASE

The Kubernetes deployment and service configuration for MySQL looks like this ([deploy/mysql-v1.yaml](../deploy/mysql-v1.yaml)):

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
  labels:
    app: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:5.7
        ports:
        - containerPort: 3306
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: secret
        - name: MYSQL_DATABASE
          value: todos
---
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  selector:
    app: mysql
  type: NodePort
  ports:
    - protocol: TCP
      port: 3306
```

This isn't too different from the todo deployment, the names and labels are changed, of course the container image reference is different.

The section for environment variables is new:

```
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: secret
        - name: MYSQL_DATABASE
          value: todos
```

It contains 2 key-value pairs in a YAML array called `env`.

1. Deploy the app and check the logs

    In another, second shell start:
    ```
    $ stern mysql
    ```

    and in your first shell issue:

    ```
    $ kubectl apply -f deploy/mysql-v1.yaml
    ```

    There will be a lot of output in the `stern` shell but it should state in the end:

    ```
    mysql-fc666f95b-8kz7p mysql 2020-12-15T13:41:59.551732Z 0 [Note] mysqld: ready for connections.
    mysql-fc666f95b-8kz7p mysql Version: '5.7.32'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)
    ```

So MySQL is running in its own pod and waiting for a connection on port 3306 which is MySQL's default.

---

**Next Step:** [Connect ToDo with MySQL using Environment Variables](lab3.md) 