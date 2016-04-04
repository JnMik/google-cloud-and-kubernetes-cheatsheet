# Google Cloud and Kubernetes - Cheatsheet

This document take into consideration that you already have a google account and have access to google cloud.
Hop straight into the Google cloud shell.

## Useful gcloud and kubectl commands

### Create a new cluster (view pricing doc at the bottom to see price for f1-micro or other machines)

    gcloud container clusters create hello-world --num-nodes 1 --machine-type f1-micro
    
### Delete a cluster:

    gcloud container clusters delete hello-world

### View the currents machines you have booted

    gcloud compute instances list
  
### Authenticate to your docker repositories
  
    docker login

At this point, if you want to deploy your containers easily without having to connect to each node and docker login / docker pull on these, you will use kubectl tool to deploy. kubectl need to be aware of your credentials for your docker repositories. For that, you will need to create a yaml file to generate a "secret". Learn more about this here, and read the comments because Kubernetes documentation is outdated.
    
    http://stackoverflow.com/questions/34290528/kubernetes-pullimageerror-using-docker-hub-with-a-private-image

    Big picture is, you must base64encode the content of ~/.docker/config.json, but you must remove the auth: {} in it first
    because it's somehow buggy. Then you can take this piece of code and put it in the secret file (see my secret.sample.yaml file)

### Authenticate to the container cluster you have launched

    gcloud container clusters list
    gcloud container clusters get-credentials <your-cluster-name> --zone=<your-cluster-zone>
    
### Now you can start playing with kubectl to deploy containers through pods and replication controllers
  
    Deploy a single container (Omit the tag and it will use latest) : 
        kubectl run wordpress --image=tutum/wordpress --port=80
    If you want to deploy a pod with multiple containers in it :
        https://cloud.google.com/container-engine/docs/replicationcontrollers/operations

### Rolling update

  Kubernetes allows you to release new version of your application without down time.
  This must be done with the rolling-update tool. The tool will launched the new containers, and down
  the previous, one at a time. Here's the command line :

  kubectl rolling-update <rc-controller-name-to-down> -f new-rc-controller-config.json

  Be careful tho, you *must* specify version number if your RC config, or rolling update WILL crash,
  and your application will be down. See my rc.sample.json for reference, version number
  must be specify at multiple place.

### Delete the running pods with:

    kubectl delete rc wordpress
  
### Your container at this point is not available to the public, you can expose it by linking it to the load balancer (this create a service).

    kubectl expose rc wordpress --type=LoadBalancer
    
### How to delete the service, which also deletes your external load balancer:

    kubectl delete services wordpress
    
It might takes few minutes before the services is creatd and exposed, you can use the describe command to have more details on the service. Once you see the external IP there, it should be available, but I notice a little delay even after the public IP is shown there.

    kubectl describe services wordpress
    
### Enter bash in a container

    kubectl exec <pod_id> -it -- bash -il
    
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

### Ad more nodes to your container cluster (might take few minutes for the node to be up)

    gcloud container clusters resize <cluster name> --size=<node_count> --zone=<zone>

### More commands are also available to you if you need more specifics features

    kubectl --help
    
### Monitoring on compute engine
At the end of each VM rows in the VM instances section, there is a SSH button that would popup a shell inside the VM. It may be pretty hard to see on a small screen. Once in the VM, you should be able to install New Relic Agent following the debian documentation.

### AdmissionController

I suggest you take the time to read this documentation about admission controllers

    http://kubernetes.io/v1.0/docs/admin/admission-controllers.html
    
Enabled admission controllers can be managed here

    /etc/kubernetes/apiserver
    
If you edit the admission controllers, don't forget to restart the service

    systemctl restart kube-apiserver.service

## Debugging

If you have issues like PullImageError and stuff like that, you can get more details with this commands

        kubectl describe pod <pod_name>
        
If you still have issues with PullImageError, make sure .docker/config.json file is the same on all your docker nodes.
If the file mismatch from a server to another, some of these will mismatch with the secret you generated, it will cause some issues.

While having random issues, I also managed to get some very good hints from these commands

    kubelet logs
    kubectl get events
    tail -f /var/log/messages
    kubectl logs <pod_name>
    
If you having issue mounting files in your pods, with "Permission denied message", make sure your docker nodes authorized the files in SELinux.

    chcon -Rt svirt_sandbox_file_t /path/to/file.ext
  
## Related documentation

    Pricing sheet : https://cloud.google.com/compute/pricing?hl=fr&_ga=1.190239275.1094250114.1456595331
    Google cloud shell : https://cloud.google.com/shell/docs/
    Boot your first app : https://cloud.google.com/container-engine/docs/tutorials/hello-wordpress
    New Relic : https://docs.newrelic.com/docs/servers/new-relic-servers-linux/installation-configuration/servers-installation-ubuntu-debian
    To install your own Kubernetes clusters http://severalnines.com/blog/installing-kubernetes-cluster-minions-centos7-manage-pods-services)
