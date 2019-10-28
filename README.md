# polaris-openshift-setup (POC)
A setting up process for openshift on a private cloud environment

## Prepare the image for Private Environment
- Go into server ehich have the external network access : ssh login on root_user@696
- Add the Proxy : sudo vi /etc/sysconfig/docker
  HTTP_PROXY=http://user:password@proxy:8080
  HTTPS_PROXY=https://user:password@proxy:8080
- Restart Daemon : sudo systemctl restart docker
- Login on Quay : sudo docker login quay.io -u user -p password
- Pull Image : sudo docker pull quay.io/reactiveops/polaris
- Remove the proxy in the docker config

## Push into Openshift registry
- Add the insecure registry: sudo vi /etc/sysconfig/docker
  INSECURE_REGISTRY='--insecure-registry docker-registry-default.xxx'
- Restart Daemon : sudo systemctl restart docker
- Login on Openshift registry: sudo docker login -p token -e unused -u unused docker-registry-default.xxx
- Get image ID : sudo docker images, find the image_id for polaris
- Tag image : sudo docker tag image_id docker-registry-default.xxx/namespace/image_name:tag
- Push into Openshift private registry : sudo docker push docker-registry-default.xxx/polaris/polaris:tag

## Deploy on Openshift Platform:
  - Login on Openshift : oc login (need cluster admin)
  - Create the template : oc apply -f https://github.com/FairwindsOps/polaris/releases/latest/download/dashboard.yaml (change to private                             if need so)
  - Change the image from "quay.io/reactiveops/polaris" to "docker-registry.default.svc:5000/polaris/polaris"
  - Create the ingress : oc expose polaris-dashboard.
  
  Well done, the dashboard setting up is finished.
