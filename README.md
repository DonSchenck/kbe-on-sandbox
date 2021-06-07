# kbe-on-sandbox

## Exploring Kubernetes By Example, using Red Hat Developer Sandbox for OpenShift

Prerequisites:
1. Developer Sandbox account
1. kubectl installed on your local PC
1. git installed on your local PC
1. Optional: podman or docker installed on your local PC

Here's what we'll be doing:
* Logging into the sandbox
* Creating a backend program called "quotes", including setting environment variables to be used by the code.
* Creating a React.js frontend program called "quotesweb"
* Viewing quotesweb in our browswer
* Scaling backend to two pods and observing the result in quotesweb
* Creating a Persistent Volume Claim to support MariaDB running in Kubernetes
* Creating a Secret to be use with the database
* Creating a MariaDB database, "quotesdb", running in Kubernetes
* Creating and populating the table "quotes" in the "quotesdb" database.
* Destroying the MariaDB pod to observe Kubernetes' "Self Healing"
* Updating the backend program "quotes" to Version 2 and observing the results in "quotesweb".

The Kubernetes features used, as described at kubernetesbyexample.com, will be:
* pods
* labels
* deployments
* services
* service discovery
* health checks
* environment variables
* namespaces
* volumes
* persistent volumes
* secrets
* logging

## Preparation
`git clone https://github.com/donschenck/quotesweb`  
`git clone https://github.com/donschenck/quotemysql`  
`git clone https://github.com/donschenck/qotd-python`


## The Tutorial
### Logging into the sandbox
**TODO** Copy the login command from your sandbox and use that.
### Creating a backend program called "quotes", including setting environment variables to be used by the code.
In your qotd-python/k8s directory:  
`kubectl apply -f quotes-deployment.yaml`  
`kubectl apply -f service.yaml`  
`kubectl apply -f route.yaml`  
Note that the deployment (quotes-deployment.yaml) will use the following image:
quay.io/donschenck/quotes:v1


### Creating a React.js frontend program called "quotesweb"
In your quotesweb directory:  
`kubectl apply -f quotesweb-deployment.yaml`  
`kubectl apply -f quotesweb-service.yaml`  
`kubectl apply -f quotesweb-route.yaml`  
Note that the deployment (quotesweb-deployment.yaml) will use the following image:
quay.io/donschenck/quotesweb:v1

### Viewing quotesweb in our browser
`kubectl get routes`  
Use the route for quotesweb; paste that into your browser.

### Scaling backend to two pods and observing the result in quotesweb
### Creating a Persistent Volume Claim to support MariaDB running in Kubernetes
### Creating a Secret to be use with the database
### Creating a MariaDB database, "quotesdb", running in Kubernetes
### Creating and populating the table "quotes" in the "quotesdb" database.
### Destroying the MariaDB pod to observe Kubernetes' "Self Healing"
### Updating the backend program "quotes" to Version 2 and observing the results in "quotesweb"