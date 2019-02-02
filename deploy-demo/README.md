# alibaba-cloud-k8s-demo

## Set up k8s dashboard
By default, Alibaba Cloud container service will not install dashboard for you.
Refer to the K8s dashboard [installation page](https://github.com/kubernetes/dashboard/wiki/Installation) either configure SSH certificate and use

```bash
# to be honest, I never tried this one as I anyway do not plan to expose dashboard publicly.
kubectl create secret generic kubernetes-dashboard-certs --from-file=$HOME/certs -n kube-system

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
```
Or accept HTTP access by using:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/alternative/kubernetes-dashboard.yaml
```

Then install the dashboard admin privilege by using the yaml below:

```bash
kubectl create -f dashboard-admin.yaml
```

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: kubernetes-dashboard
  labels:
    k8s-app: kubernetes-dashboard
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: kubernetes-dashboard
  namespace: kube-system
```

Access the dashboard by using the Link below via proxy

```bash
kubectl proxy
```

http://localhost:8001/api/v1/namespaces/kube-system/services/http:kubernetes-dashboard:/proxy/#!/overview?namespace=default

## Below is the example how to setup MySQL with WordPress
Original Code can be found in [google demo](https://github.com/GoogleCloudPlatform/kubernetes-engine-samples).

### Prepare Disk

```bash
kubectl apply -f ./alibaba-cloud-k8s-demo/deploy-demo/mysql-pvc.yaml
#check if available by using:
kubectl describe pvc
# in case you do not know which storage class to use:
kubectl get storageclass
```

Deploy MySQL with service
```bash
# deploy app
kubectl apply -f ./alibaba-cloud-k8s-demo/deploy-demo/mysql.yaml
# deploy service
kubectl apply -f ./alibaba-cloud-k8s-demo/deploy-demo/mysql-service.yaml

```

Deploy WordPress with servcie

```bash
# deploy pvc
kubectl apply -f ./alibaba-cloud-k8s-demo/deploy-demo/wp-pvc.yaml
# deploy app
kubectl apply -f ./alibaba-cloud-k8s-demo/deploy-demo/wp.yaml
# deploy service
kubectl apply -f ./alibaba-cloud-k8s-demo/deploy-demo/wp-service.yaml

```

Scale deployment
```bash
# check current situation
kubectl get deployments
# scale up
kubectl scale --replicas=2 deployment/wordpress
# check again
kubectl describe deployments wordpress
```

How to know if MySQL and WordPress are deployed properly

```bash
#forward port and login into MySQL to check if schema/tables are generated (root/Aliyun2019)
kubectl port-forward mysql-564d978f8d-zk4rl 3306:3306
```

Use OSS as storage

```bash
# create PV from OSS
kubectl apply -f ./alibaba-cloud-k8s-demo/deploy-demo/wp-oss-pv.yaml

# create PVC from PV

```