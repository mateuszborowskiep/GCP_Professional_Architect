**Kubernetes Architecture**
![[Screenshot 2022-07-25 at 09.04.43.png]]

**kubectl command syntax **

![[Screenshot 2022-07-26 at 13.10.09.png]]

**Create a GKE cluster**
`gcloud container clusters create [CLUSTER-NAME]` 

**Authenticate the cluster (after creating cluster, you need authentication credentials) 
`gcloud container clusters get-credentials [CLUSTER-NAME]`

**Create a new Deployment (https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) **
`kubectl create deployment hello-server —image=gcr.io/google-samples/hello-app:1.0 // create deployment hello-server from the hello-app container image`

**Create a Kubernetes Service (Kubernetes resource that lets you expose your app to external traffic**
`kubectl expose deployment hello-server —type=LoadBalancer —port 8080 // create a Compute Engine load balancer expose on port 8080` 

**Deleting the cluster**
`gcloud container clusters delete [CLUSTER-NAME]` 


1. To deploy your first version of the application, run the following commands in Cloud Shell to get up and running:

`gcloud container clusters get-credentials echo-cluster --zone=us-central1-a`

`kubectl create deployment echo-web --image=gcr.io/qwiklabs-resources/echo-app:v1`

`kubectl expose deployment echo-web --type=LoadBalancer --port 80 --target-port 8000`

2. Build image in the lab

`gcloud builds submit --tag gcr.io/$DEVSHELL_PROJECT_ID/echo-app:v2 .`

3. Authorize cloud shell to access Kubernetes cluster

`gcloud container clusters get-credentials echo-cluster --zone us-central1-a`

4. Edit echo-web deployments
`kubectl edit deploy echo-web`

5. Scale replicas
`kubectl scale deploy echo-web --replicas=2`
