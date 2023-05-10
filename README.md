# nms-on-k8s-via-helm

Follow the steps in the guide to deploy NGINX Management Suite to Kubernetes using a Helm chart.

This repository provides instructions for deploying NGINX Management Suite (NMS) version 2.9.1 on Kubernetes using a Helm chart. NMS is a set of monitoring, management, and analytics tools for NGINX and NGINX Plus. With NMS, you can monitor the health and performance of your NGINX instances, troubleshoot issues, and optimize your configuration.
Assumptions

This guide assumes the following:

    You are deploying version 2.9.1 of NMS.
    Persistence is disabled for all NMS components.
    
More information on:
https://docs.nginx.com/nginx-management-suite/admin-guides/installation/kubernetes/nms-helm/

Clone this repository:
```
git clone https://github.com/ericausente/nms-on-k8s-via-helm.git
```

Navigate to the cloned repository:
```
cd nms-on-k8s-via-helm/
```

Install NMS using Helm, specifying your desired configuration options. For example:
```
helm install -n nms --set nms-hybrid.adminPasswordHash=$(openssl passwd -1 'YourPassword123#') --set nms-hybrid.core.persistence.enabled=false --set nms-hybrid.integrations.persistence.enabled=false --set nms-hybrid.dpm.persistence.enabled=false --set nms-hybrid.nmsClickhouse.persistence.enabled=false --set nms-hybrid.apigw.image.repository=ausente/nms-apigw --set nms-hybrid.apigw.image.tag=2.9.1 --set  nms-hybrid.core.image.repository=ausente/nms-core --set nms-hybrid.core.image.tag=2.9.1 --set nms-hybrid.dpm.image.repository=ausente/nms-dpm --set nms-hybrid.dpm.image.tag=2.9.1 --set nms-hybrid.ingestion.image.repository=ausente/nms-ingestion --set nms-hybrid.ingestion.image.tag=2.9.1 --set nms-hybrid.integrations.image.repository=ausente/nms-integrations --set nms-hybrid.integrations.image.tag=2.9.1 nms nginx-stable/nms --create-namespace --wait <img width="6243" alt="image" src="https://github.com/ericausente/nms-on-k8s-via-helm/assets/17806308/6ac34166-0990-4cf8-9253-639d880147ee">
```
This command will deploy NMS with the specified configuration options. Note that the adminPasswordHash option specifies the hashed password for the NMS administrator account.


Verify that NMS is running by checking the status of the NMS pods:
```
% kubectl get pods -n nms
NAME                            READY   STATUS    RESTARTS   AGE
apigw-59ccb99d78-x87d2          1/1     Running   0          81s
clickhouse-8677677b7-zbj6b      1/1     Running   0          81s
core-5d75c9964-hrdjx            1/1     Running   0          81s
dpm-8fd849694-4w7nj             1/1     Running   0          81s
ingestion-74b7dcd98-zjvz7       1/1     Running   0          81s
integrations-687c69f6d6-c7cnp   1/1     Running   0          81s
```

A LoadBalancer service for the NMS API gateway was created and since I am running this on EKS, 
my LoadBalancer service has an external IP address: which you can use to access the NMS web interface from the internet.
```
$ kubectl get svc -n nms
NAME           TYPE           CLUSTER-IP       EXTERNAL-IP                                                                    PORT(S)                      AGE
apigw          LoadBalancer   10.100.237.5     ********************************-1464760227.ap-southeast-1.elb.amazonaws.com   443:31390/TCP                32h
clickhouse     ClusterIP      10.100.24.210    <none>                                                                         9000/TCP,8123/TCP            32h
core           ClusterIP      10.100.249.157   <none>                                                                         8033/TCP,8038/TCP            32h
dpm            ClusterIP      10.100.159.28    <none>                                                                         8034/TCP,8036/TCP,9100/TCP   32h
ingestion      ClusterIP      10.100.255.99    <none>                                                                         8035/TCP                     32h
integrations   ClusterIP      10.100.103.39    <none>                                                                         8037/TCP                     32h
```

