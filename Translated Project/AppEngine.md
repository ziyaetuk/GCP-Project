# Getting Started with App Engine



## Objectives

- Initialize App Engine.

- Preview an App Engine application running locally in Cloud Shell.

- Deploy an App Engine application, so that others can reach it.

- Disable an App Engine application, when you no longer want it to be visible.


##### List active account:
    gcloud auth list
#####  Verify project ID:
     gcloud config list project


##### Associate App Engine with project :

```
gcloud app create --project=$DEVSHELL_PROJECT_ID
```




##### Clone Sample application python app from git hub:
    git clone https://github.com/GoogleCloudPlatform/python-docs-samples

##### Navigate to the source directory:
        cd python-docs-samples/appengine/standard_python3/hello_world



##### Run Hello World application locally

Update the packages list.

        sudo apt-get update


##### Install Python virtual environments

    sudo apt-get install virtualenv -y


##### Create virtual environment "venv" for python application
    virtualenv -p python3 venv
##### Activate the virtual environment.
        source venv/bin/activate



##### Install python dependencies for application 

    pip install  -r requirements.txt



##### Run the application:

        python main.py


##### Test application locally using curl:

        curl http://127.0.0.1:8080


##### 

## Deploy and run Hello World on App Engine

To deploy your application to the App Engine Standard environment:



##### Navigate to the source directory:

     cd ~/python-docs-samples/appengine/standard_python3/hello_world



##### Deploy Hello World application and choose regions.

        gcloud app deploy
        14


##### Test application. 

     gcloud app browse
