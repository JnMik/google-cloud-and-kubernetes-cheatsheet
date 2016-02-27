# google-cloud-cheatsheet

This document take into consideration that you already have a google and have access to google cloud.

Create a new cluster

    gcloud container clusters create hello-world --num-nodes 1 --machine-type f1-micro

View the currents machines you have booted

    gcloud compute instances list
  
You should be able to use docker login at this moment
  
    docker login
  
Now you can start playing with kubectl to deploy containers and pods
  
    kubectl run wordpress --image=tutum/wordpress --port=80
  
Your container at this point is not available to the public, you can expose it by linking it to the load balancer.

    kubectl expose rc wordpress --type=LoadBalancer
  
Related documentation

    https://cloud.google.com/container-engine/docs/tutorials/hello-wordpress
