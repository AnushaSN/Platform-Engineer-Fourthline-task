# Platform-Engineer-Fourthline
Technical task repo

For more detailed documents on worklog, steps followed,assignment details, improvement points, setup details etc refer the folder named 'docs'

NOTE: Once done working on the below, use 'terraform destroy' , to delete all the infrastructure setup using the below steps.

Requirements:

Below are the listed requirements for this assignment to run as expected, steps to install them are listed in the next section
1. aws-cli
2. Terraform
3. git
4. docker
5. kubectl
6. helm

Steps to follow to check/replicate the solution setup:

Task1: Creating an EKS cluster

1. install git if not present using:https://git-scm.com/downloads

2. install docker if not present using: https://docs.docker.com/engine/install/

3. install kubectl if not present: 

4. clone this repo to your local
5. setup aws-cli if not already present using: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
6. install helm if not present: 
7. add there configuration details after aws-cli setup at the prompt:
   configuration:

   AWS Access Key ID [None]: YOUR_AWS_ACCESS_KEY_ID
   AWS Secret Access Key [None]: YOUR_AWS_SECRET_ACCESS_KEY
   Default region name [None]: YOUR_AWS_REGION
   Default output format [None]: json
   
4. to confirm installation type 'aws --version' and check for output without errors

5. Install terraform if not already present: https://learn.hashicorp.com/tutorials/terraform/install-cli?in=terraform/aws-get-started

6. verify installation by 'terraform -help'

7. git clone this repo over https : git clone https://github.com/AnushaSN/Platform-Engineer-Fourthline.git

8. terraform init

9. terraform plan - first apply will not destroy any resource, but for subsequent applies, check for list of resources that will be added/modified/deleted, so that you do not accidentally delete any resource.

10. terraform apply

11. the above command will take sometime and once the apply is complete, you should see the cluster created.

12. You can verify the created cluster in the AWS UI - https://eu-west-1.console.aws.amazon.com/eks/home?region=eu-west-1#/clusters

Task2: Deploy a stateful application to the created cluster

1. Check for the cluster connected to using:

2. aws eks --region eu-west-1 update-kubeconfig --name <cluster-name>

3. kubectl cluster-info
   
   check for the cluster details, the name should be the one we created in the above step.

4. clone the repo: git clone https://github.com/AnushaSN/Stateful_App.git

5. create a seperate namespace to deploy nginx
   kubectl create namespace nginx

6. to use this namespace for future commands :
   kubectl config set-context --current --namespace=nginx
   to check if namespace is selected:
   kubectl config view --minify | grep namespace:

7. create the application using statefulset:
   kubectl apply -f web.yml
   create the service:
   kubectl apply -f service.yml

8. check the stateful set and pods created using:
   kubectl get sts
   kubectl get pods
  
   wait for the pods to change to Running state
  
9. to check the persistant volumes created dynamically from statefulset:
      kubectl get pvc
   
10. to check the statefulnes/persistancy of the pods:
   
      Write the Pods' hostnames to their index.html files and verify that the NGINX webservers serve the hostnames:
   
      for i in 0 1; do kubectl exec "web-$i" -- sh -c 'echo "$(hostname)" > /usr/share/nginx/html/index.html'; done
   
      for i in 0 1; do kubectl exec -i -t "web-$i" -- curl http://localhost/; done
   
      you should see output:
      web-0
      web-1
   
      now delete the pods, and wait for the statefulset to create new pods:
   
      kubectl delete pod -l app=nginx
   
      Verify the web servers continue to serve their hostnames:

      for i in 0 1; do kubectl exec -i -t "web-$i" -- curl http://localhost/; done
   
      web-0
      web-1
  
Task3: Deploy Stateless application using given steps:
   
Unable to complete the task in time - the container does not start, and needs more time to debug: error: standard_init_linux.go:228: exec user process caused: exec format error
   
   But these are the steps for the given tasks:
   
   - Enable HPA for the deployment and set the target utilization to 80% for CPU and 85% for memory.
      -  for enabling HPA, the following changes to be made in values.yml
            -  autoscaling:
               enabled: true
               minReplicas: 1
               maxReplicas: 3
               metrics:
               - type: Resource
                 resource:
                  name: cpu
                  target:
                     type: Utilization
                     averageUtilization: 80
                  - type: Resource
                 resource:
                  name: memory
                  target:
                     type: Utilization
                     averageUtilization: 85
   - Set CPU request to 150m.
      to set CPU request to 150m change the below value in values.yml
         resources:
         limits:
            cpu: 200m
            memory: 200Mi
         requests:
            cpu: 150m
            memory: 100Mi
         
   - Make the application highly available.
      There are multiple factors that contribute to highly available systems:
         - In Pod level, add a liveness probe to ensure the pod is restarted when app crashes, or there is a deadlock etc
         - Ensure you have a multi node setup, and the nodes are in different avaialblity zones, so that when one node fails due to any issue, the pods are moved to a different node.
         - Always deploy application as a ReplicaSet or Deployment object, so when one pod is down/deleted a new one is spun up automatically.
   
   - Expose the application via a service to the cluster.
      - can be done by adding a service.yml with ClusterIP
   
   - Expose the application to the internet. You may use the Route53 zone that has already been created in the provided AWS account, in region eu-west
      - can be achieved by adding an A record of the Service ClusterIP in Route53
   
   - Make the application's index page show Hello {GREET_VAR} instead of "Hello world", where GREET_VAR is an environment variable. The variable should get its value from the values.yaml file.
   
   - Fix the provided Dockerfile, so it runs the application on startup.
      - can be acheived via ENTRYPOINT or CMD in dockerfile where you will provide a script or exec command that will start the application
   
   
