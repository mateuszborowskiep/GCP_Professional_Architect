**Kubernetes Architecture**
![[Screenshot 2022-07-25 at 09.04.43.png]]

**kubectl command syntax **

![[Screenshot 2022-07-26 at 13.10.09.png]]


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
