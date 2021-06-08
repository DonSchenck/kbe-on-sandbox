

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
You will need to download or clone the following three repositories (repos) from Github.com:

`git clone https://github.com/donschenck/quotesweb`  
`git clone https://github.com/donschenck/quotemysql`  
`git clone https://github.com/donschenck/qotd-python`


## The Tutorial
### What we're creating
This tutorial will guide you through using Kubernetes to create three components.  

First, a backend application, written in Python 3.8, that supplies "Quote Of The Day"-type data via a RESTful API. The endpoints are as follows:  

/  
Returns the string "qotd" to simply identify the service.

/version  
Returns a string denoting the version id of the service.  

/writtenin  
Returns the programming language in which the service is written. In this case it is "Python", but there this same service is available in several different programming languages.  

/quotes  
Returns a JSON array of all of the quotes.  

/quotes/random  
Returns a JSON object of one random quote from among the set of available quotes.  

/quotes/{id}  
Returns a JSON object of one specific quote within the set of available quotes.  


### Logging into the sandbox
**TODO** Copy the login command from your sandbox and use that.

### Creating a backend program called "quotes", including setting environment variables to be used by the code.

In this step we will create three Kubernetes objects: a Deployment, a Service, and a Route (which is similar to the Ingress and Ingress Controller objects).

<hr>  

### About Route, Ingress, and Ingress Controller  
Because Red Hat Developer Sandbox for OpenShift is administered by Red Hat, you do not have administrator access to the Kubernetes cluster. One of the limitations of this is that you are not granted rights to create Ingress and Ingress Controller objects.

OpenShift has its own built-in Ingress-like object, the Route. For this tutorial, we're going to "cheat" and use the Route object. Be aware that we are using this workaround; in your own Kubernetes cluster you'll want to create the Ingress and Ingress Controller objects.
<hr>

In your qotd-python/k8s directory:  
`kubectl apply -f quotes-deployment.yaml`  
`kubectl apply -f service.yaml`  
`kubectl apply -f route.yaml`  

#### pods and labels
When you create the deployment, Kubernetes will pull the image from the image registry named in the yaml file and will create a pod. It will also assign the labels which you specified in the deployment.

The pod name is automatically generated from the deployment name, with random characters appended to it.

Viewing the contents of the file quotes-deployment.yaml, we can see that the pods will be named "quotesweb" (plus the random characters, e.g. "quotesweb-5468c95fc6-5sp9j"), while the label will be "app=quotesweb".

Note that the pod name and app name *do not* need to be the same.

Note that the deployment (quotes-deployment.yaml) will use the following image:
quay.io/donschenck/quotes:v1


### Creating a React.js frontend program called "quotesweb"
In your quotesweb/k8s directory:  
`kubectl apply -f quotesweb-deployment.yaml`  
`kubectl apply -f quotesweb-service.yaml`  
`kubectl apply -f quotesweb-route.yaml`  
Note that the deployment (quotesweb-deployment.yaml) will use the following image:
quay.io/donschenck/quotesweb:v1

### Viewing quotesweb in our browser
`kubectl get routes`  
Use the route for quotesweb; paste that into your browser.

### Scaling backend to two pods and observing the result in quotesweb
`kubectl scale deployments/quotesweb --replicas=2`  

### Creating a Persistent Volume Claim to support MariaDB running in Kubernetes
In your quotesmysql directory:  
`kubetl apply -f mysqlvolume.yaml`

### Creating a Secret to be use with the database
**TODO**
### Creating a MariaDB database, "quotesdb", running in Kubernetes
In your quotesmysql directory:  

`kubectl apply -f mysql-secret.yaml`  
`kubectl apply -f mysqlvolume.yaml`  
`kubectl apply -f mysql-deployment.yaml`  


### Creating and populating the table "quotes" in the "quotesdb" database.
#### PowerShell:
`kubectl get pods`  
`$podname="{mysql pod name from previous kubectl get pods command}"`    

`kubectl cp ./create_database_quotesdb.sql ${podname}:/tmp/create_database_quotesdb.sql`  
`kubectl cp ./create_database.sh ${podname}:/tmp/create_database.sh`  
`kubectl exec deploy/mysql -- /bin/bash ./tmp/create_database.sh`  
  
`kubectl cp ./create_table_quotes.sql ${podname}:/tmp/create_table_quotes.sql`  
`kubectl cp ./create_tables.sh ${podname}:/tmp/create_tables.sh`  
`kubectl exec deploy/mysql -- /bin/bash ./tmp/create_tables.sh`  

`kubectl cp ./populate_table_quotes.sql ${podname}:/tmp/populate_table_quotes_POWERSHELL.sql`  
`kubectl cp ./quotes.csv ${podname}:/tmp/quotes.csv`  
`kubectl cp ./populate_tables.sh ${podname}:/tmp/populate_tables_POWERSHELL.sh`  
`kubectl exec deploy/mysql -- /bin/bash ./tmp/populate_tables.sh`  

`kubectl cp ./query_table_quotes.sql ${podname}:/tmp/query_table_quotes.sql`  
`kubectl cp ./query_table_quotes.sh ${podname}:/tmp/query_table_quotes.sh`  
`kubectl exec deploy/mysql -- /bin/bash ./tmp/query_table_quotes.sh`  


#### ash:  
`kubectl get pods`  
`export PODNAME="{mysql pod name from previous kubectl get pods command}"`  

`kubectl cp ./create_database_quotesdb.sql $PODNAME:/tmp/create_database_quotesdb.sql`  
`kubectl cp ./create_database.sh $PODNAME:/tmp/create_database.sh`  
`kubectl exec deploy/mysql -- /bin/bash ./tmp/create_database.sh`  

`kubectl cp ./create_table_quotes.sql $PODNAME:/tmp/create_table_quotes.sql`  
`kubectl cp ./create_tables.sh $PODNAME:/tmp/create_tables.sh`  
`kubectl exec deploy/mysql -- /bin/bash ./tmp/create_tables.sh`  

`kubectl cp ./populate_table_quotes.sql $PODNAME:/tmp/populate_table_quotes_BASH.sql`  
`kubectl cp ./quotes.csv $PODNAME:/tmp/quotes.csv`  
`kubectl cp ./populate_tables.sh $PODNAME:/tmp/populate_tables_BASH.sh`  
`kubectl exec deploy/mysql -- /bin/bash ./tmp/populate_tables.sh`  

`kubectl cp ./query_table_quotes.sql $PODNAME:/tmp/query_table_quotes.sql`  
`kubectl cp ./query_table_quotes.sh $PODNAME:/tmp/query_table_quotes.sh`  
`kubectl exec deploy/mysql -- /bin/bash ./tmp/query_table_quotes.sh` 

Expose service:  
`kubectl expose deploy/mysql --name mysql --port 3306 --type NodePort`  

### Destroying the MariaDB pod to observe Kubernetes' "Self Healing"
### Updating the backend program "quotes" to Version 2 and observing the results in "quotesweb"
`kubectl set image deploy quotes quotes=quay.io/donschenck/quotes:v2`