Note: This is running in EKS Cluster

```
helm install -n nms --set nms-hybrid.adminPasswordHash=$(openssl passwd -1 'YourPassword123#') nms nginx-stable/nms --create-namespace -f values.yaml --wait

```

```
% kubectl get svc -n nms    
NAME           TYPE           CLUSTER-IP       EXTERNAL-IP                                                                   PORT(S)                      AGE
acm            ClusterIP      10.100.12.12     <none>                                                                        8037/TCP                     60m
apigw          LoadBalancer   10.100.38.64     **********-747054010.ap-southeast-1.elb.amazonaws.com   443:31402/TCP                60m
clickhouse     ClusterIP      10.100.225.51    <none>                                                                        9000/TCP,8123/TCP            60m
core           ClusterIP      10.100.249.102   <none>                                                                        8033/TCP,8038/TCP            60m
dpm            ClusterIP      10.100.87.43     <none>                                                                        8034/TCP,8036/TCP,9100/TCP   60m
ingestion      ClusterIP      10.100.122.24    <none>                                                                        8035/TCP                     60m
integrations   ClusterIP      10.100.74.74     <none>                                                                        8037/TCP                     60m
```

Browser for the IP and enter your password.
Load the license. 

Create an environment and 2 types of cluster (one for the actual api cluster and one for the Developer Portal) 


==Infrastructure==

Create Workspace
team-sentence > Create

Create Environment
sentence-env 
non-prod
APIGW Hostname
api.kushikimi.xyz
Proxy-Cluster-Name: api-cluster
> Create

Install NJS and Metrics in your dataplane. 
https://docs.nginx.com/nginx-management-suite/nginx-agent/install-nginx-plus-advanced-metrics/

curl --insecure https://nmsacm.kushikimi.xyz/install/nginx-plus-module-metrics | sudo sh

--- Please do follow including the CONFIGURE NGINX AGENT TO USE ADCANCED METRICS

Then run the curl/wget command


--

---

==Services==

Create Workspace
sentence-app
(Back to workspaces) 

Click the workspace name  > API Doc > Upload Swagger > Save
Go to API Proxy > Publish to Proxy


Click on API Proxies tab, and Publish to Proxy
Backend Service
Name : sentence-svc 
Service Target Hostname : a9fa871c21fb7424b9633c7c14f3d094-140401031.ap-southeast-1.elb.amazonaws.com (this is the K8s on EKS Cluster Loadbalancer IP Address) 

Service Target Transport Protocol : HTTP 
Service Target Port : 30511 (if K8S Node Port) 

API Proxy
Name : sentence-api 
Use an OpenAPI Spec ? -> YES 
API Spec : api-sentence-generator-v1 


Gateway Proxy Hostname : api.kushikimi.xyz


--Back to workspace---
Actions > Edit Proxy

Append rule is none. 





