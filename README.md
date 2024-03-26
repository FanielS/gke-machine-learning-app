# Machine Learning Model Deployment on GCE and Kubernetes

This project demonstrates deploying a logistic regression-based machine learning model on Google Compute Engine (GCE) and Kubernetes. It covers setting up a GCE instance, containerizing the application with Docker, and managing deployment with Kubernetes for scalability and maintenance. The model aims to predict customer online purchasing behavior, showcasing modern cloud-based deployment strategies for machine learning applications.



## Authors

- [@faniels](https://www.github.com/octokatherine)


## Implementation Steps
Now that we've outlined what the project entails, let's dive into the detailed steps for executing it. Follow these steps to set up the environment, deploy the application, and manage the deployment efficiently:

### 1.	Create Instance on Google Compute Engine (GCE):

- Navigate to the Google Cloud Console.
- Go to the Compute Engine section.
- Click on "Create instance".
- Configure the instance settings such as machine type, boot disk, and networking.
- Optionally, customize additional settings like startup script, SSH keys, and metadata.
- Click on "Create" to create the instance.


### 2.	Copy Codes and Files to Instance Directory:

I have worked on my machine learning model in my local machine. In this case, I trained a binary classification model using logistic regression that predicts whether the customer will buy the product online or not. Follow the below steps to transfer your local files to your GCE instance.

- Open a terminal or command prompt on your local machine.
- Navigate to the directory containing the files you want to copy.
- Use the gcloud compute scp command to copy files to your instance:
```gcloud compute scp --recurse * cloud_user@instance-name:/path/to/destination/directory --zone=us-west2-a```

Replace ```cloud_user``` with your instance username, ```instance-name``` with the name of your GCE instance, and ```/path/to/destination/directory``` with the path to the destination directory on your GCE instance where you want to copy the files.

Files I copied are Dockerfile,  ML.ipynb,  flask_api.py,  logreg.pkl, online_sales.csv, requirements.txt and  test_data.csv.


### 3.	Create a Git repository to track changes to your project files directly on the GCE instance. 

This will allow you to manage and version control your project effectively.

```git init```

```git add .```

```git commit -m "Initial commit"```

![App Screenshot](https://via.placeholder.com/468x300?text=App+Screenshot+Here)

![App Screenshot](https://via.placeholder.com/468x300?text=App+Screenshot+Here)

![App Screenshot](https://via.placeholder.com/468x300?text=App+Screenshot+Here)


### 4.	Proceed to Kubernetes
Install kubectl if its not already installed
sudo apt-get install kubectl

The below command will dynamically fetche the current project ID and zone of the instance and uses them to create the cluster with 3 nodes.

```gcloud container clusters create kubia --num-nodes=3 --zone=$(curl -H "Metadata-Flavor: Google" http://metadata.google.internal/computeMetadata/v1/instance/zone | cut -d/ -f4) --project=$(curl -H "Metadata-Flavor: Google" http://metadata.google.internal/computeMetadata/v1/project/project-id)```

### 5. Verify that the nodes have been created successfully

```kubectl get nodes```

You should see three nodes being created as shown below.

![App Screenshot](https://via.placeholder.com/468x300?text=App+Screenshot+Here)

### 6.	Build a Docker image using the Dockerfile in the current directory.

*N.B refer to Dockerfile in the GitHub repository for details of the file.*

- Install docker if not installed
```sudo apt-get update```

```sudo apt-get install docker.io```
- Login to docker and insert your username and password when prompted

```sudo docker login```
- Build the docker image
```docker build -t gke-ml-app .```

![App Screenshot](https://via.placeholder.com/468x300?text=App+Screenshot+Here)

- Tag the Docker image with your Docker Hub username and repository name:

```sudo docker tag gke-ml-app faniels/gke-ml-app:latest```

![App Screenshot](https://via.placeholder.com/468x300?text=App+Screenshot+Here)

- Push your docker image to docker hub.

```Sudo docker push repo-name/image-name```

![App Screenshot](https://via.placeholder.com/468x300?text=App+Screenshot+Here)

### 7.	Next, create a Kubernetes Deployment YAML to manage the application's pods. 

This YAML file defines the desired state for your application, including the number of replicas, container image location, and any other configuration options.

*N.B refer to deployment.yaml in the GitHub repository for details of the file.*

### 8. Apply the above deployment using the below apply command:

```kubectl apply -f deployment.yaml```

![App Screenshot](https://via.placeholder.com/468x300?text=App+Screenshot+Here)


### 9. Verify that the pods are created successfully using the following commands:

```kubectl get deployment```

```kubectl get pods```

![App Screenshot](https://via.placeholder.com/468x300?text=App+Screenshot+Here)

### 10.	 Create a Kubernetes Service

Now that the application is running inside pods managed by the deployment, we need to expose it to external traffic using a Kubernetes Service. This will provide a stable endpoint to access the application.

Define the Service YAML Configuration: Create a YAML file specifying the details of the Service. 

*N.B refer to service.yaml in the GitHub repository for details of the file.*

### 11.	 Use the kubectl apply command to apply the YAML configuration to the Kubernetes cluster.

```Kubectl apply -f service.yaml```

![App Screenshot](https://via.placeholder.com/468x300?text=App+Screenshot+Here)

### 12.	Verify the Service: 

After creating the Service, verify that it's running correctly using the below command.

```kubectl get svc ```

You should see the external IP address assigned to the LoadBalancer.

![App Screenshot](https://via.placeholder.com/468x300?text=App+Screenshot+Here)

### 13.	Access your application using the external IP address provided by the LoadBalancer.

![App Screenshot](https://via.placeholder.com/468x300?text=App+Screenshot+Here)

![App Screenshot](https://via.placeholder.com/468x300?text=App+Screenshot+Here)

### 14.	Optionally, you can stop the Kubernetes Deployment

To stop the deployment, you can delete the deployment and service using the below command.

```kubectl delete deployment flask-app```

```kubectl delete service my-flask-app```
