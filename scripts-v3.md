# Deploy version 3
Execute the following istio configurations: 

```sh
oc apply -f ./config/istio/destinationrules-all.yaml 
oc apply -f ./config/istio/virtual-services-all-v2.yaml
oc apply -f ./config/istio/virtual-service-userprofile-v1.yaml

oc new-app -f ./config/app/userprofile-build.yaml \
  -p APPLICATION_NAME=userprofile \
  -p APPLICATION_CODE_URI=https://github.com/RedHatGov/service-mesh-workshop-code.git \
  -p APPLICATION_CODE_BRANCH=workshop-feature-fix \
  -p APP_VERSION_TAG=3.0

oc start-build userprofile-3.0 -F
```

## Wait still build is done. Then:
```sh
USER_PROFILE_IMAGE_URI=$(oc get is userprofile --template='{{.status.dockerImageRepository}}')
echo $USER_PROFILE_IMAGE_URI
sed "s|%USER_PROFILE_IMAGE_URI%|$USER_PROFILE_IMAGE_URI|" ./config/app/userprofile-deploy-v3.yaml | oc create -f -
oc apply -f ./config/istio/virtual-service-userprofile-v3.yaml
```
