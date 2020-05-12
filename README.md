# Adserver

AdServer is a program which fetches Ad script from the server which fetches image from server and paste it into div

## Components

Project consist of two parts:
1. Backend
2. Frontend

### 1. Backend

* Backend is developed in nodejs with express framework and jade view templating engine.
* Runs on 3000 port (internally)
* Public IP of backend server: http://178.128.130.194
* ### Routes
    * **Get JS**  */render/getjs*
        * This endpoint will get js code which will be executed on browser to get imge from below endpoint.
    * **Get Image** */render/get-image*
        * This endpoint will get image stored in s3 bucket.

### 2. Frontend

* Frontend has simple html file which has 1 div tag and 1 script tag. That's it!

    Below is the div where image tag will be created and pasted
    ```html
    <div id='some_random_id'>

    </div>
    ```
    Below code will generate new script tag, load it's content from server and paste it inside head tag
    ```javascript
        var script=document.createElement('script');
        script.setAttribute("type","application/javascript");
        script.setAttribute("src", "http://178.128.130.194/render/getjs");
        document.getElementsByTagName("head")[0].appendChild(script);
    ```

## Deployment

### Dockerization
* Backend node server deployment is done using dockerization, Here is Dockefile of our backend server

    ```docker
    FROM node:latest
    RUN mkdir -p /src/app
    WORKDIR /src/app
    COPY . /src/app
    RUN npm install
    EXPOSE 3000 
    CMD ["npm","start"]
    ```
### Kubernetes orchestration

* Docker build and pushed to dockerhub
* Kubernetes cluster fetches pushed images from dockerhub, here is deployment.yaml for the same:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
    name: adserver
    spec:
    selector:
        matchLabels:
        app: adserver
    replicas: 1
    template:
        metadata:
        labels:
            app: adserver
        spec:
        containers:
        - name: adserver
            image: shbhsngh/adserver-image
            ports:
            - containerPort: 3000
    ```
* Kuberentes service forward port 80 to 3000 container port and create public IP, Here is yaml for the same:
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
    name: adserver
    spec:
    selector:
        app: adserver
    ports:
        - protocol: TCP
        port: 80
        targetPort: 3000
    type: LoadBalancer
    ```

# THANK YOU FOR READING! 
Any suggestion, write to me at shbhsngh@gmail.com