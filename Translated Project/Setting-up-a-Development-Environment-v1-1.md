### **App Dev: Setting up a Development Environment v1.1**

##### **Objective:**

- Provision a Google Compute Engine instance.
- Connect to the instance using SSH.
- Install software on the instance.
- Verify the software installation.


##### **Setup**

Successful login to GCP console with credentials provided by QwickLab. 

1. **Verify login credential:**

```
gcloud auth list
```


2. **Set default zone and region:**

```
gcloud compute project-info add-metadata  --metadata google-compute-default-region=us-central1,google-compute-default-zone=us-central1-a
```

#### Task 1:  

3. **Creating a Compute Engine Virtual Machine Instance:**

```
gcloud compute instances create dev-instance --zone us-central1-a --scopes https://www.googleapis.com/auth/cloud-platform --tags http
```

4.	**Setup http firewall rule:**

``` 
gcloud compute firewall-rules create http-access --allow tcp:80,tcp:443 --target-tags=http --direction=INGRESS
```

5. **Verify the availability the just created VM instance:**
``` 
gcloud compute instances list | grep dev-instance
```
6. **Then SSH into "dev-instance" -**
```
gcloud compute ssh dev-instance
```
7. **Update dev-instance : ( Update the Debian package list)**
```
sudo apt-get update
```
8. **Install git : **
```
sudo apt-get install git
```
9. **Download  Node.js and setup script:**
```
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
```
10. **Install npm and Node.js:**
```
sudo apt install nodejs
```
11. **Check the version of Node.js:**
```
node -v
```
12. **To clone the class repository, execute the following command:**
```
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
```
13. **Change the working directory**
```
cd ~/training-data-analyst/courses/developingapps/nodejs/devenv/
```
14. **Run the Web Server**
```
sudo node server/app.js
```
15. **Test webserver**
```
curl 34.122.15.177
```
16. **Return to the SSH window, and stop the application by pressing Ctrl+C.**
17. **Run a simple Node.js application that lists Compute Engine instances**
```
node list-gce-instances.js
```