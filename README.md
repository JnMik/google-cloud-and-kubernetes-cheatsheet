# google-cloud-cheatsheet

This document take into consideration that you already have a google and have access to google cloud.

Create a new cluster

    gcloud container clusters create hello-world --num-nodes 1 --machine-type f1-micro
    

Delete a cluster:

    gcloud container clusters delete hello-world

View the currents machines you have booted

    gcloud compute instances list
  
You should be able to use docker login at this moment
  
    docker login
  
Now you can start playing with kubectl to deploy containers through pods
  
    kubectl run wordpress --image=tutum/wordpress --port=80
  
Delete the running pods with:

    kubectl delete rc wordpress
  
Your container at this point is not available to the public, you can expose it by linking it to the load balancer (this create a service).

    kubectl expose rc wordpress --type=LoadBalancer
    
How to delete the service, which also deletes your external load balancer:

    kubectl delete services wordpress
    
It might takes few minutes before the services is creatd and exposed, you can use the describe command to have more details on the service. Once you see the external IP there, it should be available, but I notice a little delay even after the public IP is shown there.

    kubectl describe services wordpress
  
Related documentation

    https://cloud.google.com/container-engine/docs/tutorials/hello-wordpress
