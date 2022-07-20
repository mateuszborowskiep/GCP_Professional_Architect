gcloud container clusters get-credentials echo-cweb --zone=us-central1-a

kubectl create deployment echo-web --image=gcr.io/qwiklabs-resources/echo-app:v1

docker push gcr.io/google-samples/echo-app:2.0

kubectl update deployment echo-web --image=



kubectl apply

kubectl expose deployment echo-web --type=LoadBalancer --port 80 --target-port 8000

Copy code from bucket 
gsutil cp -r gs://spls/gsp021/* .


Please follow the below steps:

1. To deploy your first version of the application, run the following commands in Cloud Shell to get up and running:

gcloud container clusters get-credentials echo-cluster --zone=us-central1-a

kubectl create deployment echo-web --image=gcr.io/qwiklabs-resources/echo-app:v1

kubectl expose deployment echo-web --type=LoadBalancer --port 80 --target-port 8000

2. Download application tar for the lab

gsutil cp gs://$DEVSHELL_PROJECT_ID/echo-web-v2.tar.gz .

3. Unzip the file

tar -xvf echo-web-v2.tar.gz

4. Build image in the lab

gcloud builds submit --tag gcr.io/$DEVSHELL_PROJECT_ID/echo-app:v2 .

5. Authorize cloud shell to access Kubernetes cluster

gcloud container clusters get-credentials echo-cluster --zone us-central1-a

6. Edit echo-web deployments

kubectl edit deploy echo-web

Press i to edit the file

7. Change container image to v2 from v1

Press esc and paste :wq

8. Scale replicas

kubectl scale deploy echo-web --replicas=2

9. Once you have successfully completed a section, wait for a minute or two and then click on the “Run Step” button. It will immediately evaluate your score.
