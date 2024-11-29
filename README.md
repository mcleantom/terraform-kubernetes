Initialize aws infrastructure:

```
terraform init
terraform apply
```

Sign into aws eks:
```
aws eks --region $(terraform output -raw region) update-kubeconfig \
    --name $(terraform output -raw cluster_name)
```

Install helm chart:
```
helm install hello-world ./hello-world
```

Get the deployed api endpoint:
```
kubectl get --namespace default svc -w hello-world
```

Which should output something like:
```
mclea@ARL-145:~/src/terraform-kubernetes$ kubectl get --namespace default svc -w hello-world
NAME          TYPE           CLUSTER-IP       EXTERNAL-IP                                                              PORT(S)        AGE
hello-world   LoadBalancer   172.20.150.106   a492d57021e4549b699962a2fdffc5b2-292250870.us-east-2.elb.amazonaws.com   80:31127/TCP   5m26s
```
Then you can make a request:
```
export SERVICE_IP=$(kubectl get svc --namespace default hello-world --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")
echo $SERVICE_IP
curl $SERVICE_IP
```