Creating a Virtual Machines Lab
# Task 1: Create a utility virtual machine
Create a VM

gcloud  compute instances create instance-1 --zone=us-central1-c --machine-type=n1-standard-1 --subnet=default --no-address


# Task 2: Create a Windows virtual machine
Create a VM

gcloud compute instances create instance-2 --zone=europe-west2-a --machine-type=n1-standard-2 --image=windows-server-2016-dc-core-v20200813 --image-project=windows-cloud --boot-disk-size=100GB --boot-disk-type=pd-ssd --boot-disk-device-name=instance-2 --no-shielded-secure-boot --shielded-vtpm --tags=http-server,https-server

gcloud compute firewall-rules create default-allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server

gcloud compute firewall-rules create default-allow-https --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:443 --source-ranges=0.0.0.0/0 --target-tags=https-server

# Task 3: Create a custom virtual machine
Create a VM

gcloud compute instances create instance-4 --zone=us-west1-b --custom-cpu 6 --custom-memory 32