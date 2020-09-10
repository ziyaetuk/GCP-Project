**Set Default project**

```
gcloud compute set project qwiklabs-gcp-03-327dd05a150f
```


**Task 1. Explore the default network** : Delete the Firewall rules

```
gcloud compute firewall-rules delete default-allow-icmp default-allow-rdp default-allow-ssh default-allow-interna
```

l

**Delete the default network**

```
gcloud compute networks delete default
```



**Task 2. Create an auto mode network**

```
gcloud compute networks create mynetwork --subnet-mode=auto
gcloud compute networks create mynetwork --subnet-mode=auto --bgp-routing-mode=regional

gcloud compute firewall-rules create mynetwork-allow-icmp --network mynetwork --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=icmp

gcloud compute firewall-rules create mynetwork-allow-internal --network mynetwork --direction=INGRESS --priority=65534 --source-ranges=10.128.0.0/9 --action=ALLOW --rules=all

gcloud compute firewall-rules create mynetwork-allow-rdp --network mynetwork --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=tcp:3389

gcloud compute firewall-rules create mynetwork-allow-ssh --network mynetwork --direction=INGRESS --priority=65534 --source-ranges=0.0.0.0/0 --action=ALLOW --rules=tcp:22
```

**Create a VM instance in us-central1**

```
gcloud compute instances create mynet-us-vm --zone us-central1-c --machine-type n1-standard-1 --network mynetwork
```

**Create a VM instance in europe-west1**

```
gcloud compute instances create mynet-eu-vm --zone europe-west1-c --machine-type n1-standard-1 --network mynetwork
```

**Verify connectivity for the VM instances**

```
gcloud compute ssh mynet-us-vm
ping -c 3 10.132.0.2
ping -c 3 mynet-eu-vm
ping -c 3 34.76.194.146
```

**Convert the network to a custom mode network**

```
gcloud compute networks update mynetwork --switch-to-custom-subnet-mode
```

**Task 3. Create custom mode networks** : Create the managementnet network

```
gcloud compute networks create managementnet --subnet-mode=custom
gcloud compute networks subnets create managementsubnet-us --range=10.130.0.0/20 --network=managementnet --region=us-central1
```

**Create the privatenet network**

```
gcloud compute networks create privatenet --subnet-mode=custom
```

**To create the privatesubnet-us subnet, run the following command**

```
gcloud compute networks subnets create privatesubnet-us --network=privatenet --region=us-central1 --range=172.16.0.0/24
```

**To create the privatesubnet-eu subnet, run the following command**

```
gcloud compute networks subnets create privatesubnet-eu --network=privatenet --region=europe-west1 --range=172.20.0.0/20
```

**Create the firewall rules for managementnet**

```
gcloud compute firewall-rules create managementnet-allow-icmp-ssh-rdp --network=managementnet --priority=1000 --action=ALLOW --direction=INGRESS  --source-ranges=0.0.0.0/0 --rules=tcp:3389,tcp:22,icmp
```

**Create the firewall rules for privatenet**

```
gcloud compute firewall-rules create privatenet-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 --network=privatenet --action=ALLOW --rules=icmp,tcp:22,tcp:3389 --source-ranges=0.0.0.0/0
```

**Create the managementnet-us-vm instance**

```
gcloud compute instances create managementnet-us-vm --zone us-central1-c --machine-type f1-micro --network managementnet --subnet managementsubnet-us
```

**Create the privatenet-us-vm instance**

```
gcloud compute instances create privatenet-us-vm --zone=us-central1-c --machine-type=f1-micro --subnet=privatesubnet-us
```

**Task 4. Explore the connectivity across networks** : For mynet-us-vm, click SSH to launch a terminal and connect

```
ping -c 3 34.76.194.146
ping -c 3 34.122.253.196
ping -c 3 34.68.84.165
```

**Return to the SSH terminal for mynet-us-vm.**

```
ping -c 3 10.132.0.2
ping -c 3 10.130.0.2
ping -c 3 172.16.0.2
```


