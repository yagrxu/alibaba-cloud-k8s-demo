# alibaba-cloud-k8s-demo

Alibaba Cloud is able to host your docker images within container registry service
Below is the example code for the complete process.

## start a small docker

``` docker
from mysql:5.6
```

Then you need to build your own docker image, push to cloud

```bash
docker build . -t [image-name]
sudo docker login --username=[RAM-USER]@[UID] registry-intl.eu-central-1.aliyuncs.com
sudo docker tag [image-name]:[tag] registry-intl.eu-central-1.aliyuncs.com/[namespace]/[image-name]:[tag]
sudo docker push registry-intl.eu-central-1.aliyuncs.com/[namespace]/[image-name]:[tag]
```

How to make sure it can be used within K8s?

1, You should be able to see it in the container registry (via browser)
2, You should be able to deploy it in the k8s cluster.

```bash
kubectl create secret docker-registry regcred --docker-server=<your-registry-server-internal-url> --docker-username=<your-name> --docker-password=<your-pword> --docker-email=<your-email>
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
spec:
  containers:
  - name: hello-world
    image: test:v1
  imagePullSecrets:
  - name: regcred

```