# Platform-Engineer-Fourthline
Technical task repo

For more detailed documents on worklog, steps followed,assignment details, improvement points, setup details etc refer the folder named 'docs'


Requirements:

Below are the listed requirements for this assignment to run as expected, steps to install them are listed in the next section
1. aws-cli
2. Terraform

Steps to follow to check/replicate the solution setup:

Task1: Creating an EKS cluster

1. clone this repo to your local
2. setup aws-cli if not already present using: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
3. add there configuration details after aws-cli setup at the prompt:
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
  


