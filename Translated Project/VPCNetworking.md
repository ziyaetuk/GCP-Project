# VPC Networking

### Objective:
In this lab, I create an auto mode VPC network with firewall rules and two VM instances. Then convert the auto mode network to a custom mode network and create other custom mode. I also test connectivity across networks. All the task were performed via Cloud Shell.

- Explore the default VPC network
- Create an auto mode network with firewall rules
- Convert an auto mode network to a custom mode network
- Create custom mode VPC networks with firewall rules
- Create VM instances using Compute Engine
- Explore the connectivity for VM instances across VPC networks

## Task 1. Explore the default network
1. View all default subnet :
```
gcloud compute networks subnets list |  grep default
```
```
default  us-central1              default  10.128.0.0/20
default  europe-west1             default  10.132.0.0/20
default  us-west1                 default  10.138.0.0/20
default  asia-east1               default  10.140.0.0/20
default  us-east1                 default  10.142.0.0/20
default  asia-northeast1          default  10.146.0.0/20
default  asia-southeast1          default  10.148.0.0/20
default  us-east4                 default  10.150.0.0/20
default  australia-southeast1     default  10.152.0.0/20
default  europe-west2             default  10.154.0.0/20
default  europe-west3             default  10.156.0.0/20
default  southamerica-east1       default  10.158.0.0/20
default  asia-south1              default  10.160.0.0/20
default  northamerica-northeast1  default  10.162.0.0/20
default  europe-west4             default  10.164.0.0/20
default  europe-north1            default  10.166.0.0/20
default  us-west2                 default  10.168.0.0/20
default  asia-east2               default  10.170.0.0/20
default  europe-west6             default  10.172.0.0/20
default  asia-northeast2          default  10.174.0.0/20
default  asia-northeast3          default  10.178.0.0/20
default  us-west3                 default  10.180.0.0/20
default  us-west4                 default  10.182.0.0/20
default  asia-southeast2          default  10.184.0.0/20
```
2. View default firewall rule:
```
gcloud compute firewall-rules list
```
```
NAME                    NETWORK  DIRECTION  PRIORITY  ALLOW                         DENY  DISABLED
default-allow-icmp      default  INGRESS    65534     icmp                                False
default-allow-internal  default  INGRESS    65534     tcp:0-65535,udp:0-65535,icmp        False
default-allow-rdp       default  INGRESS    65534     tcp:3389                            False
default-allow-ssh       default  INGRESS    65534     tcp:22                              False
```
3. Delete all the Firewall rules 
```
gcloud compute firewall-rules delete default-allow-icmp default-allow-rdp default-allow-ssh default-allow-internal
```
4. Delete default Network 
```
gcloud compute networks delete default
```
5. Check if Route still exist:
```
gcloud compute routes list
```
```
Listed 0 items.
```
6. View Firewall : 
```
gcloud compute firewall-rules list
```
6. Try to create a VM instance after deleting firewall and network : error
```
 gcloud compute instances create vm-instance
```
## Task 2. Create an auto mode network
8. Create Network:
```
gcloud compute networks create mynetwork --subnet-mode= auto
```
```
NAME       SUBNET_MODE  BGP_ROUTING_MODE  IPV4_RANGE  GATEWAY_IPV4
mynetwork  AUTO         REGIONAL
```
9. View created network
```
gcloud compute networks list | grep mynetwork
```
10. Create Firewall rule:
```
gcloud compute firewall-rules create mynetwork --network mynetwork --allow icmp,udp,tcp,tcp:22,tcp:3389
```
```
Creating firewall...done.
NAME       NETWORK    DIRECTION  PRIORITY  ALLOW                         DENY  DISABLED
mynetwork  mynetwork  INGRESS    1000      icmp,udp,tcp,tcp:22,tcp:3389        False
```
11. Record the IP address range for the subnets in us-central1 and europe-west1
```
gcloud compute networks subnets list | grep -E  "us-central1 europe-west1"
```
```
mynetwork  us-central1              mynetwork  10.128.0.0/20
mynetwork  europe-west1             mynetwork  10.132.0.0/20
```
12. Create a VM instance mynet-us-vm in us-central1
```
gcloud compute instances create mynet-us-vm --zone=us-central1-c --network=mynetwork
```
```
NAME         ZONE           MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP     STATUS
mynet-us-vm  us-central1-c  n1-standard-1               10.128.0.2   34.122.119.166  RUNNING
```
13. Create a VM instance mynet-eu-vm in europe-west1 
```
gcloud compute instances create mynet-eu-vm --zone=europe-west1-c --network=mynetwork
```
```
NAME         ZONE           MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP     STATUS
mynet-eu-vm  europe-west1-c  n1-standard-1               10.132.0.2   34.77.239.214  RUNNING
```
14. Verify created VM instances
```
gcloud compute instances describe mynet-eu-vm 
gcloud compute instances describe mynet-us-vm
```
15. From cloud shell ssh into mynet-us-vm: 
```
gcloud compute ssh  mynet-us-vm
```
```
ping -c 3 34.77.239.214
```
```
PING 34.77.239.214 (34.77.239.214) 56(84) bytes of data.
64 bytes from 34.77.239.214: icmp_seq=1 ttl=52 time=124 ms
64 bytes from 34.77.239.214: icmp_seq=2 ttl=52 time=124 ms
64 bytes from 34.77.239.214: icmp_seq=3 ttl=52 time=124 ms
```
16. Convert the network to a custom mode network : exit from ssh 
```
gcloud compute networks describe mynetwork
```
```
gcloud compute networks update mynetwork --switch-to-custom-subnet-mode
```
```
Switching network to custom-mode...done.
```
## Task 3. Create custom mode networks
17. Create the managementnet network 
```
gcloud compute networks create managementnet --subnet-mode=custom
gcloud compute networks subnets create managementsubnet-us --network= managementnet --region=us-central1 --range= 10.130.0.0/20
```
18. Create the privatenet network 
```
gcloud compute networks create privatenet --subnet-mode=custom
gcloud compute networks subnets create privatesubnet-us --network=privatenet --region=us-central1 --range=172.16.0.0/24
gcloud compute networks subnets create privatesubnet-eu --network=privatenet --region=europe-west1 --range=172.20.0.0/20
```
19. List the available VPC networks
```
gcloud compute networks subnets list --sort-by=NETWORK
```
```
NAME           SUBNET_MODE  BGP_ROUTING_MODE  IPV4_RANGE  GATEWAY_IPV4
managementnet  CUSTOM       REGIONAL
mynetwork      CUSTOM       REGIONAL
privatenet     CUSTOM       REGIONAL
```
20. List the available VPC subnets
```
gcloud compute networks subnets list --sort-by=NETWORK
```
```
NAME                 REGION                   NETWORK        RANGE
managementsubnet-us  us-central1              managementnet  10.130.0.0/20
mynetwork            us-central1              mynetwork      10.128.0.0/20
mynetwork            europe-west1             mynetwork      10.132.0.0/20
mynetwork            us-west1                 mynetwork      10.138.0.0/20
mynetwork            asia-east1               mynetwork      10.140.0.0/20
mynetwork            us-east1                 mynetwork      10.142.0.0/20
mynetwork            asia-northeast1          mynetwork      10.146.0.0/20
mynetwork            asia-southeast1          mynetwork      10.148.0.0/20
mynetwork            us-east4                 mynetwork      10.150.0.0/20
mynetwork            australia-southeast1     mynetwork      10.152.0.0/20
mynetwork            europe-west2             mynetwork      10.154.0.0/20
mynetwork            europe-west3             mynetwork      10.156.0.0/20
mynetwork            southamerica-east1       mynetwork      10.158.0.0/20
mynetwork            asia-south1              mynetwork      10.160.0.0/20
mynetwork            northamerica-northeast1  mynetwork      10.162.0.0/20
mynetwork            europe-west4             mynetwork      10.164.0.0/20
mynetwork            europe-north1            mynetwork      10.166.0.0/20
mynetwork            us-west2                 mynetwork      10.168.0.0/20
mynetwork            asia-east2               mynetwork      10.170.0.0/20
mynetwork            europe-west6             mynetwork      10.172.0.0/20
mynetwork            asia-northeast2          mynetwork      10.174.0.0/20
mynetwork            asia-northeast3          mynetwork      10.178.0.0/20
mynetwork            us-west3                 mynetwork      10.180.0.0/20
mynetwork            us-west4                 mynetwork      10.182.0.0/20
mynetwork            asia-southeast2          mynetwork      10.184.0.0/20
privatesubnet-us     us-central1              privatenet     172.16.0.0/24
privatesubnet-eu     europe-west1             privatenet     172.20.0.0/20
```
21. Create the firewall rules for managementnet
```
gcloud compute firewall-rules create  managementnet-allow-icmp-ssh-rdp --network managementnet --action=ALLOW --rules=icmp,tcp:22,tcp:3389  --direction=INGRESS --priority=1000 --action=ALLOW --source-ranges=0.0.0.0/0
```
22. Create the firewall rules for privatenet
```
gcloud compute firewall-rules create privatenet-allow-icmp-ssh-rdp --direction=INGRESS --priority=1000 --network=privatenet --action=ALLOW --rules=icmp,tcp:22,tcp:3389 --source-ranges=0.0.0.0/0
```
23. To list all the firewall rules
```
gcloud compute firewall-rules list --sort-by=NETWORK
```
```
NAME                              NETWORK        DIRECTION  PRIORITY  ALLOW                         DENY  DISABLED
managementnet-allow-icmp-ssh-rdp  managementnet  INGRESS    1000      icmp,tcp:22,tcp:3389                False
mynetwork                         mynetwork      INGRESS    1000      icmp,udp,tcp,tcp:22,tcp:3389        False
privatenet-allow-icmp-ssh-rdp     privatenet     INGRESS    1000      icmp,tcp:22,tcp:3389                False
```
24. Create the managementnet-us-vm instance (Virtual Machine)
```
gcloud compute instances create managementnet-us-vm --zone= us-central1-c --subnet=managementsubnet-us --machine-type=f1-micro
```
25. Create the privatenet-us-vm instance (Virtual Machine)
```
gcloud compute instances create privatenet-us-vm --zone=us-central1-c --machine-type=f1-micro --subnet=privatesubnet-us
```
26. List all the VM instances
```
gcloud compute instances list --sort-by=NETWORK
```
```
NAME                 ZONE            MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP     STATUS
mynet-eu-vm          europe-west1-c  n1-standard-1               10.132.0.2   34.77.239.214   RUNNING
managementnet-us-vm  us-central1-c   f1-micro                    10.130.0.2   130.211.204.17  RUNNING
mynet-us-vm          us-central1-c   n1-standard-1               10.128.0.2   34.122.119.166  RUNNING
privatenet-us-vm     us-central1-c   f1-micro                    172.16.0.2   34.123.212.145  RUNNING
```
## Task 4. Explore the connectivity across networks
28. a.	Ping mynet-eu-vm, managementnet-us-vm, and privatenet-us-vm from mynet-us-vm
```
gcloud compute ssh  mynet-us-vm
```
```
ping -c 3 35.187.172.12 
ping -c 3 130.211.204.17 
ping -c 3 34.123.212.145 
```
29. To test connectivity to internal IP
```
ping -c 3 10.132.0.2
PING 10.132.0.2 (10.132.0.2) 56(84) bytes of data.
64 bytes from 10.132.0.2: icmp_seq=1 ttl=64 time=105 ms
64 bytes from 10.132.0.2: icmp_seq=2 ttl=64 time=104 ms
```
```
ping -c 3 10.130.0.2
PING 10.130.0.2 (10.130.0.2) 56(84) bytes of data.
3 packets transmitted, 0 received, 100% packet loss, time 44ms
```
```
ping -c 3 172.16.0.2
PING 172.16.0.2 (172.16.0.2) 56(84) bytes of data.
2 packets transmitted, 0 received, 100% packet loss, time 6ms
```
```
ping -c 3 172.16.0.2
3 packets transmitted, 0 received, 100% packet loss, time 48ms
```
```
exit
```