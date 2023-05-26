The --dry-run flag is used to simulate the installation process without actually deploying the Helm chart. It allows you to test the installation and see the actions that would be performed, such as creating resources and applying configurations, without making any changes to your cluster. This is helpful for verifying the correctness of the command and understanding the effects it would have.
The --debug flag is used to enable debug output during the Helm operation. It provides additional information and logs that can be useful for troubleshooting any issues or understanding the internal workings of Helm. 

```
helm install -n nms \
  --set nms-hybrid.adminPasswordHash=$(openssl passwd -1 'YourPassword123#') \
  --set nms-hybrid.nmsClickhouse.persistence.storageClass=efs-sc \
  --set nms-hybrid.core.persistence.storageClass=efs-sc \
  --set nms-hybrid.dpm.persistence.storageClass=efs-sc \
  --set nms-hybrid.integrations.persistence.storageClass=efs-sc \
  --set nms-hybrid.apigw.image.repository=ausente/nms-apigw \
  --set nms-hybrid.apigw.image.tag=2.9.1 \
  --set nms-hybrid.core.image.repository=ausente/nms-core \
  --set nms-hybrid.core.image.tag=2.9.1 \
  --set nms-hybrid.dpm.image.repository=ausente/nms-dpm \
  --set nms-hybrid.dpm.image.tag=2.9.1 \
  --set nms-hybrid.ingestion.image.repository=ausente/nms-ingestion \
  --set nms-hybrid.ingestion.image.tag=2.9.1 \
  --set nms-hybrid.integrations.image.repository=ausente/nms-integrations \
  --set nms-hybrid.integrations.image.tag=2.9.1 \
  nms nginx-stable/nms \
  --create-namespace \
  --wait \
  --dry-run \
  --debug
```


Display all the available information about a Helm chart called "nms" from the repository named "nginx-stable".
```
 % helm show all nginx-stable/nms          
apiVersion: v2
appVersion: NIM 2.10.1|ACM 1.6.0|ADM 4.0.0
dependencies:
- name: nms-hybrid
  repository: https://github.com/nginxinc/helm-charts/stable
  version: ~2.10.1
- condition: global.nmsModules.nms-acm.enabled
  name: nms-acm
  repository: https://github.com/nginxinc/helm-charts/stable
  version: ~1.6.0
- condition: global.nmsModules.nms-adm.enabled
  name: nms-adm
  repository: https://github.com/nginxinc/helm-charts/stable
  version: ~4.0.0
description: A chart for installing the NGINX Management Suite
name: nms
type: application
version: 1.6.0

---
nms-hybrid:
  adminPasswordHash: 
nms-acm: {}

nameOverride: "nms"
fullnameOverride: "nms"

global:
  nmsModules:
    nms-acm:
      enabled: false
    nms-adm:
      enabled: false

```

You will notice that there's nothing much in the main nms umbrella. 
But it contains information about the dependencies of the Helm chart "nms" from the repository "nginx-stable". One of those is the below dependency:
```
    Dependency: nms-hybrid
        Repository: https://github.com/nginxinc/helm-charts/stable
        Version: ~2.10.1
```
Description: This dependency represents the nms-hybrid chart, which is used for installing the NGINX Management Suite. 
It is hosted in the "nginxinc" GitHub repository and has a version requirement of approximately 2.10.1.
   
   
```
helm show all nginx-stable/nms-hybrid | less   
helm show values nginx-stable/nms-hybrid
````
helm show all nginx-stable/nms-hybrid | less: 
This command displays all the available information about the "nms-hybrid" chart and pipes it to the less command, which allows you to scroll through the output in a paginated manner. By using this command, you can view the complete details of the chart, including its metadata, dependencies, description, and other relevant information.

helm show values nginx-stable/nms-hybrid: 
This command specifically shows the values (configuration) associated with the "nms-hybrid" chart. It displays the default values defined in the chart's values.yaml file. These values provide a way to customize the behavior and configuration of the chart during installation. By running this command, you can see the default values that will be used unless overridden.
    
Edit the values in the text editor according to your requirements. Make any necessary modifications, such as changing configuration options or updating resource limits.

```
apigw:
  name: apigw
  # To bring your own NGINX API Gateway certificates for hosting HTTPS NMS server,
  # set "tlsSecret" to an existing kubernetes secret name in the same namespace as the chart.
  # We recommend to set "tlsSecret" for production use case to manage certs.
  # By default, this helm chart creates it's own CA to self-sign HTTPS server cert key pair. These are not managed.
  # Follow our installation guide to get an example of a "tlsSecret" resource.
  tlsSecret: ""
  image:
    # if the image is on a private registry, provide imagePullSecrets
    # For using NGINX Plus, update the repository name to your NGINX Plus docker image
    repository: apigw
    tag: latest
    pullPolicy: IfNotPresent
  container:
    port:
      https: 443
  service:
    # Other supported values are NodePort, LoadBalancer.
    type: ClusterIP
```

So that is apigw.service.type
nms-hybrid.apigw.service.type=LoadBalaner

So that makes it: 
```
helm install -n nms \
  --set nms-hybrid.adminPasswordHash=$(openssl passwd -1 'YourPassword123#') \
  --set nms-hybrid.core.persistence.enabled=false \
  --set nms-hybrid.integrations.persistence.enabled=false \
  --set nms-hybrid.dpm.persistence.enabled=false \
  --set nms-hybrid.nmsClickhouse.persistence.enabled=false \
  --set nms-hybrid.apigw.image.repository=ausente/nms-apigw \
  --set nms-hybrid.apigw.image.tag=2.9.1 \
  --set nms-hybrid.core.image.repository=ausente/nms-core \
  --set nms-hybrid.core.image.tag=2.9.1 \
  --set nms-hybrid.dpm.image.repository=ausente/nms-dpm \
  --set nms-hybrid.dpm.image.tag=2.9.1 \
  --set nms-hybrid.ingestion.image.repository=ausente/nms-ingestion \
  --set nms-hybrid.ingestion.image.tag=2.9.1 \
  --set nms-hybrid.integrations.image.repository=ausente/nms-integrations \
  --set nms-hybrid.integrations.image.tag=2.9.1 \
  --set nms-hybrid.apigw.service.type=LoadBalancer \
  nms nginx-stable/nms \
  --create-namespace \
  --wait
```

```
e.ausente@C02DR4L1MD6M ~ % helm install -n nms \
  --set nms-hybrid.adminPasswordHash=$(openssl passwd -1 'YourPassword123#') \
  --set nms-hybrid.core.persistence.enabled=false \
  --set nms-hybrid.integrations.persistence.enabled=false \
  --set nms-hybrid.dpm.persistence.enabled=false \
  --set nms-hybrid.nmsClickhouse.persistence.enabled=false \
  --set nms-hybrid.apigw.image.repository=ausente/nms-apigw \
  --set nms-hybrid.apigw.image.tag=2.9.1 \
  --set nms-hybrid.core.image.repository=ausente/nms-core \
  --set nms-hybrid.core.image.tag=2.9.1 \
  --set nms-hybrid.dpm.image.repository=ausente/nms-dpm \
  --set nms-hybrid.dpm.image.tag=2.9.1 \
  --set nms-hybrid.ingestion.image.repository=ausente/nms-ingestion \
  --set nms-hybrid.ingestion.image.tag=2.9.1 \
  --set nms-hybrid.integrations.image.repository=ausente/nms-integrations \
  --set nms-hybrid.integrations.image.tag=2.9.1 \
  --set nms-hybrid.apigw.service.type=LoadBalancer \
  nms nginx-stable/nms \
  --create-namespace \
  --wait
```
