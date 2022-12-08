gcloud compute instance create Instance name \

>         --network nucleus-vpc \
>         --zone us-east1-b \
>         --machine-type f1-micro \
>         --omage-project deboan-cloud \
>

gcloud container clusters create nucleus-backend \
>          --num-nodes 1 \
>          --network nucleus-vpc \
>          --region us-east1
          
gcloud compute instance-templates create web-server-template \
>       --metadata-from-file startup-script=startup.sh \
>       --network nucleus-vpc \
>       --machine-type g1-small \
>       --region us-east1
       
gcloud compute instance-groups managed create web-server-group \
>       --base-instance-name web-server \
>       --size 2 \
>       --template web-server-template \
>       --region us-east1
       
gcloud compute firewall-rules create <Copy FIREWALL_NAME given in the lab> \
>     --allow tcp:80 \
>     --network nucleus-vpc
  
gcloud compute http-health-checks create http-basic-check
  
gcloud compute instance-groups managed \
>     set-named-ports web-server-group \
>     --named-ports http:80 \
>     --region us-east1
  
gcloud compute backend-services create web-server-backend \
>     --protocol HTTP \
>    --http-health-checks http-basic-check \
>     --global
  
gcloud compute backend-services add-backend web-server-backend \
>       --instance-group web-server-group \
>       --instance-group-region us-east1 \
>       --global  
  
gcloud compute url-maps create web-server-map \
>       --default-service web-server-backend

gcloud compute target-http-proxies create http-lb-proxy \
>       --url-map web-server-map

gcloud compute forwarding-rules create permit-tcp-rule-261 \
>     --global \
>     --target-http-proxy http-lb-proxy \
>     --ports 80
  
gcloud compute forwarding-rules list  
