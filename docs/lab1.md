---
title: 1. Deploy ToDo app
---

# Lab 1: Deploy ToDo app 

In the original Docker tutorial, this is the command to deploy the ToDo app stand-alone without database.

**Do not run this command here!** It is only here for reference!

```
$ docker run -dp 3000:3000 getting-started
```

* `getting-started` is the name of the container image that was created when following the Docker tutorial.
* `-p 3000:3000` maps Container port 3000 on local port 3000.

An equivalent Kubernetes deployment and service would look like this (file [deploy/todo-v1.yaml](../deploy/todo-v1.yaml)):

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

The `image:` tag points to my version of the getting-started container image on Docker Hub (`haraldu/getting-started:latest`).

{% comment %}
=======================================================================================

**FYI** 

You could use your own "getting-started" container image, the one you created in the last hands-on (Docker 101 Tutorial). The following command will load the container image from your local Docker image store into Minikube's Docker image store:

```
minikube image load getting-started:local
```

To use this image you will have to modify **all** deployments for the "To-Do" app to use the local version of the image and change the ImagePull Policy to 'Never':

```
    spec:
      containers:
      - name: todo
        image: getting-started:latest
        imagePullPolicy: Never
```

With `imagePullPolicy: Never` Kubernetes will use your locally stored image. Default is `imagePullPolicy: Always` which will try to pull the image from Docker Hub.

**/FYI**

=======================================================================================
{% endcomment %}

The ToDo app runs on port 3000 which will be exposed a NodePort.

1. Apply the configuration:

    ```
    $ kubectl apply -f deploy/todo-v1.yaml
    ```

2. Check the result with

    ```
    $ kubectl get pod
    NAME                       READY   STATUS    RESTARTS   AGE
    todo-app-f8549b989-9cbdr   1/1     Running   0          44s
    ```

3. Check the logs with `stern` (or `podtail`)

    ```
    $ stern todo
    + todo-app-f8549b989-9cbdr › todo
    todo-app-f8549b989-9cbdr todo Using sqlite database at /etc/todos/todo.db
    todo-app-f8549b989-9cbdr todo Listening on port 3000
    ```

    This means the app started on port 3000 and is using sqlite.

4. Accessing the ToDo app

    ```
    $ minikube service todo
    ```



**Note:** If you (or Kubernetes) redeploy the app, all data is gone because it isn't persisted, the app uses a built-in sqlite database which resides inside the container and is destroyed on redeploy.

  ![](todo-app.png)

---

**Next Step:** [Create MySQL deployment](lab2.md) 
