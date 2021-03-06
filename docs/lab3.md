---
title: 3. Connect ToDo with MySQL using Environment Variables
---

# Lab 3: Connect ToDo with MySQL using Environment Variables


This is the docker command from Docker Getting Started that will connect the ToDo app with MySQL:

```
docker run -dp 3000:3000 \
  -w /app -v "$(pwd):/app" \
  --network todo-app \
  -e MYSQL_HOST=mysql \
  -e MYSQL_USER=root \
  -e MYSQL_PASSWORD=secret \
  -e MYSQL_DB=todos \
  node:12-alpine \
  sh -c "yarn install && yarn run dev"
```

* We don't need a network in Kubernetes
* We will still use the getting-started container image previously used in Lab 1 (instead of mounting a local volume and run the Node.js code from there)
* There are 4 environment variables that we will use

Our deployment and service configuration looks like this now ([deploy/todo-v2.yaml](../deploy/todo-v2.yaml)):

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: todo-app
  labels:
    app: todo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: todo
  template:
    metadata:
      labels:
        app: todo
    spec:
      containers:
      - name: todo
        image: haraldu/getting-started:latest
        ports:
        - containerPort: 3000
        env:
        - name: MYSQL_PASSWORD
          value: secret
        - name: MYSQL_DB
          value: todos
        - name: MYSQL_HOST
          value: mysql
        - name: MYSQL_USER
          value: root
---
apiVersion: v1
kind: Service
metadata:
  name: todo
spec:
  selector:
    app: todo
  type: NodePort
  ports:
    - protocol: TCP
      port: 3000
```

All I did was add the environment variables.

But how will the ToDo app find the MySQL server/pod? The environment variable MYSQL_HOST seems a good pick but it contains only 'mysql'. How is this going to work?

When we created the MySQL service definition with the name 'mysql', this name 'mysql' was registered with the Kubernetes DNS (name service). Our two pods and two services share the same [Kubernetes namespace](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) ("default") and therefore the MySQL service doesn't need any further qualification. Apps in the same namespace can simply call other apps or send requests to them using their name only. Apps in different namespaces can use the fully qualified name: [servicename].[namespacename].cloud.local (mysql.default.cloud.local). 

1. Deploy the configuration to Kubernetes

    Reuse the second shell but redirect `stern` or `podtail` to the Todo app:

    ```
    $ stern todo
    ```

    Then, in the first shell, deploy the new Todo version:

    ```
    $ kubectl apply -f deploy/todo-v2.yaml
    ```

    `stern` output shows:

    ```
    $ stern todo
    + todo-app-f8549b989-hhvj6 › todo
    todo-app-f8549b989-hhvj6 todo Using sqlite database at /etc/todos/todo.db
    todo-app-f8549b989-hhvj6 todo Listening on port 3000
    + todo-app-744bcb9777-vjl7c › todo
    todo-app-744bcb9777-vjl7c todo Waiting for mysql:3306.
    todo-app-744bcb9777-vjl7c todo Connected!
    todo-app-744bcb9777-vjl7c todo Connected to mysql db at host mysql
    todo-app-744bcb9777-vjl7c todo Listening on port 3000
    - todo-app-f8549b989-hhvj6
    ```

    The first pod, todo-app-f8549b989-hhvj6, was connected to sqlite. 
    The new pod, todo-app-744bcb9777-vjl7c, successfully connects to MySQL.
    The last message (preceded with '-') indicates that the first pod is terminated.


2. Test the app in your browser 

    ```
    $ minikube service todo
    ```

    Make sure to add some items!

---

**Next Step:** [MySQL with Persistent Volumes](lab4.md) 