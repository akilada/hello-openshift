# Hello Openshift!

This image can be deployed on a Opennshift cluster. It displays:
- a default **Hello world!** message
- the pod name
- node os information

The default "Hello world!" message displayed can be overridden using the `MESSAGE` environment variable.

The default port of 8080 can be overriden using the `PORT` environment variable.

## DockerHub

It is available on DockerHub as:

- [akilada/hello-openshift:0.1](https://hub.docker.com/r/akilada/hello-openshift/)

## Deploy

You can deploy the image to your Kubernetes cluster one of two ways:

Deploy using the hello-kubernetes.yaml, which contains definitions for the service and deployment objects:

```yaml
# hello-openshift.yaml

apiVersion: v1
kind: Service
metadata:
  name: hello-kubernetes
spec:
  type: ClusterIP
  ports:
  - port: 80
    targetPort: 8080
  selector:
    app: hello-kubernetes
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-kubernetes
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello-kubernetes
  template:
    metadata:
      labels:
        app: hello-kubernetes
    spec:
      containers:
      - name: hello-kubernetes
        image: akilada/hello-openshift:0.1
        ports:
        - containerPort: 8080
```

```bash
$ oc apply -f yaml/hello-kubernetes.yaml -n <namespace>
```

This will display a **Hello world!** message when you hit the service endpoint in a browser. You can get the service endpoint ip address by executing the following command and grabbing the returned external ip address value:

```bash
$ oc get service hello-openshift
```

## Custom Message

You can customise the message displayed by the `hello-openshift` container as follows:

Deploy using the hello-opennshift.custom-message.yaml, which contains definitions for the service and deployment objects:

In the definition for the deployment, add an `env` variable with the name of `MESSAGE`. The value you provide will be displayed as the custom message.

```yaml
# hello-openshift.custom-message.yaml

apiVersion: v1
kind: Service
metadata:
  name: hello-kubernetes-custom
spec:
  type: ClusterIP
  ports:
  - port: 80
    targetPort: 8080
  selector:
    app: hello-kubernetes-custom
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-kubernetes-custom
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello-kubernetes-custom
  template:
    metadata:
      labels:
        app: hello-kubernetes-custom
    spec:
      containers:
      - name: hello-kubernetes
        image: akilada/hello-openshift:0.1
        ports:
        - containerPort: 8080
        env:
        - name: MESSAGE
          value: I just deployed this on Openshift!
```

```bash
$ oc apply -f yaml/hello-openshift.custom-message.yaml
```

## Custom Port

By default, the `hello-openshift` app listens on port `8080`. If you have a requirement for the app to listen on another port, you can specify the port via an env variable with the name of PORT. Remember to also update the `containers.ports.containerPort` value to match.

Here is an example:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-openshift-custom
spec:
  replicas: 3
  selector:
    matchLabels:
      app: hello-openshift-custom
  template:
    metadata:
      labels:
        app: hello-openshift-custom
    spec:
      containers:
      - name: hello-openshift
        image: akilada/hello-openshift:0.1
        ports:
        - containerPort: 80
        env:
        - name: PORT
          value: "80"
```
This repo is inspired by the work done by Paul Bouwer.

