# Google Cloud - Cheatsheet

This document take into consideration that you already have a google account and have access to google cloud.
Hop straight into the Google cloud shell.

## Useful gcloud and kubectl commands

### Create a new cluster (view pricing doc at the bottom to see price for f1-micro or other machines)

    gcloud container clusters create hello-world --num-nodes 1 --machine-type f1-micro
    
### Delete a cluster:

    gcloud container clusters delete hello-world

### View the currents machines you have booted

    gcloud compute instances list
  
### You should be able to use docker login at this moment, this will give you access to your dockerhub images
  
    docker login
    
### Authenticate to the container cluster you have launched

    gcloud container clusters list
    gcloud container clusters get-credentials <your-cluster-name> --zone=<your-cluster-zone>
    
### Now you can start playing with kubectl to deploy containers through pods and replication controllers
  
    kubectl run wordpress --image=tutum/wordpress --port=80
  
### Delete the running pods with:

    kubectl delete rc wordpress
  
### Your container at this point is not available to the public, you can expose it by linking it to the load balancer (this create a service).

    kubectl expose rc wordpress --type=LoadBalancer
    
### How to delete the service, which also deletes your external load balancer:

    kubectl delete services wordpress
    
It might takes few minutes before the services is creatd and exposed, you can use the describe command to have more details on the service. Once you see the external IP there, it should be available, but I notice a little delay even after the public IP is shown there.

    kubectl describe services wordpress
    
### Lisiting commands

    kubectl get pods
    kubectl get services
    kubectl get rc (replication controllers)
    kubectl get pod <pod name> -o wide  (Check on which docker node the pod is released)

### Run command inside a pod
    
    kubectl exec wordpress-pphc3 service apache2 reload
 
### View logs of pod

    kubectl logs wordpress-pphc3
    
### Add more instances of containers via kubectl scale

    kubectl scale --replicas=2 replicationcontrollers redis-container

### More commands are also available to you if you need more specifics features

    kubectl --help
    
## Monitoring on compute engine
At the end of each VM rows in the VM instances section, there is a SSH button that would popup a shell inside the VM. It may be pretty hard to see on a small screen. Once in the VM, you should be able to install New Relic Agent following the debian documentation.

  
## Related documentation

    Pricing sheet : https://cloud.google.com/compute/pricing?hl=fr&_ga=1.190239275.1094250114.1456595331
    Google cloud shell : https://cloud.google.com/shell/docs/
    Boot your first app : https://cloud.google.com/container-engine/docs/tutorials/hello-wordpress
    New Relic : https://docs.newrelic.com/docs/servers/new-relic-servers-linux/installation-configuration/servers-installation-ubuntu-debian
