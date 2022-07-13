GCP Notes

Cloud Shell - basic commands 

List active accounts
gcloud auth list  

List project ID  
gcloud config list project 

Set a default compute zone 
gcloud config set compute/zone us-east1-b // set default compute zone to us-central-a

Set a default region
gcloud config set compute/region us-central1 


Create a web server instances
gcloud compute instances create [INSTANCE-NAME] \
--image-family debian-9 \
  --image-project debian-cloud \
  --zone us-central1-a \
  --tags network-lb-tag \
  --metadata startup-script="#! /bin/bash
    sudo apt-get update
    sudo apt-get install apache2 -y
    sudo service apache2 restart
    echo '<!doctype html><html><body><h1>www1</h1></body></html>' | tee /var/www/html/index.html"

Create a firewall rule to allow external traffic to the VM instances
gcloud compute firewall-rules create www-firewall-network-lb \
    --target-tags network-lb-tag --allow tcp:80
	 
List your instances (with external IP)
gcloud compute instances list 

Check your instances  
curl http://[IP-ADDRESS]  


Create a GKE cluster
gcloud container clusters create [CLUSTER-NAME] 

Authenticate the cluster (after creating cluster, you need authentication credentials) 
gcloud container clusters get-credentials [CLUSTER-NAME]

Create a new Deployment (https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) 
kubectl create deployment hello-server —image=gcr.io/google-samples/hello-app:1.0 // create deployment hello-server from the hello-app container image

Create a Kubernetes Service (Kubernetes resource that lets you expose your app to external traffic
kubectl expose deployment hello-server —type=LoadBalancer —port 8080 // create a Compute Engine load balancer expose on port 8080 

Deleting the cluster
gcloud container clusters delete [CLUSTER-NAME] 


Set up Network and HTTP Load Balancers

Create a static external IP address for you load balancer
gcloud compute addresses create network-lb-ip-1 \
 --region us-central1

Add a legacy HTTP heart check resource:
gcloud compute http-health-checks create basic-check

Add the target pool (the same region as you instances) 
gcloud compute target-pools create www-pool \
    --region us-central1 --http-health-check basic-check

Add the instances to the pool:
gcloud compute target-pools add-instances www-pool \
    --instances [NAME-1], [NAME-2], [NAME-3]

Add a forwarding rule for pool
gcloud compute forwarding-rules create www-rule \
    --region us-central1 \
    --ports 80 \
    --address network-lb-ip-1 \
    --target-pool www-pool

Test Sending traffic to your instances 
View external IP address to www-rule forwarding 
gcloud compute forwarding-rules describe www-rule --region us-central1

Curl test
while true; do curl -m1 [IP_ADDRESS]; done

CREATE an HTTPS load balancer
Create load balancer template
gcloud compute instance-templates create lb-backend-template \
   --region=default \
   --network=default \
   --subnet=default \
   --tags=allow-health-check \
   --image-family=debian-9 \
   --image-project=debian-cloud \
   --metadata=startup-script=“#! /bin/bash
apt-get update
apt-get install -y nginx
service nginx start
sed -i -- 's/nginx/Google Cloud Platform - '"\$HOSTNAME"'/' /var/www/html/index.nginx-debian.html”

Create a managed instance group based on the template (https://cloud.google.com/compute/docs/instance-groups) 
gcloud compute instance-groups managed create lb-backend-group \
   --template=lb-backend-template --size=2 --zone=us-central1-a

Create the fw-allow-health-check firewall rule. This is an ingress rule that allows traffic from the Google Cloud health checking systems (130.211.0.0/22 and 35.191.0.0/16)
gcloud compute firewall-rules create allow-tcp-rule-585 \
    --network=default \
    --action=allow \
    --direction=ingress \
    --source-ranges=130.211.0.0/22,35.191.0.0/16 \
    --target-tags=allow-health-check \
    --rules=tcp:80

Set up global static external IP address 
gcloud compute addresses create lb-ipv4-1 \
    --ip-version=IPV4 \
    --global

Check static IP address 
gcloud compute addresses describe lb-ipv4-1 \
    --format="get(address)" \
    --global

Create a health check for the load balancer:
gcloud compute health-checks create http http-basic-check \
    --port 80

Create a backend service:
gcloud compute backend-services create web-backend-service \
    --protocol=HTTP \
    --port-name=http \
    --health-checks=http-basic-check \
    --global

Add your instance group as the backend to the backend service:
gcloud compute backend-services add-backend web-backend-service \
    --instance-group=lb-backend-group \
    --instance-group-zone=us-central1-a \
    --global

Create a URL map to route the incoming requests to the default backend service:
gcloud compute url-maps create web-map-http \
    --default-service web-backend-service

Create a target HTTP proxy to route requests to your URL map:
gcloud compute target-http-proxies create http-lb-proxy \
    --url-map web-map-http

Create a global forwarding rule to route incoming requests to the proxy:
gcloud compute forwarding-rules create http-content-rule \
    --address=lb-ipv4-1\
    --global \
    --target-http-proxy=http-lb-proxy \
    --ports=80


Create Bucket 
gsutil mb -p [PROJECT_ID] gs://[BUCKET_NAME]


Pub/Sub topics
gcloud pubsub topics create myTopic

Deploy your function
gcloud functions deploy helloWorld \
  --stage-bucket [BUCKET_NAME] \
  --trigger-topic hello_world \
  --runtime nodejs8

Verify the status of the function.
gcloud functions describe helloWorld

Test the function
DATA=$(printf 'Hello World!'|base64) && gcloud functions call helloWorld --data '{"data":"'$DATA'"}

View function logs 
gcloud functions logs read helloWorld




