# Deploying the app on EKS

### Prerequisites:
* Docker
* kubectl
* aws account
* aws cli
* eksctl



## Configuring AWS CLI

```
export AWS_ACCESS_KEY_ID
export AWS_SECRET_ACCESS_KEY
export AWS_SESSION_TOKEN
```

## Creating docker repositories in ECR

```
aws ecr create-repository --repository-name todo-app --region us-east-1
aws ecr describe-repositories 
```

### Login to the ECR locally

```
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 220937581726.dkr.ecr.us-east-1.amazonaws.com
```

### Build docker images and push them to ECR

```
docker build -t 220937581726.dkr.ecr.us-east-1.amazonaws.com/todo-app:1.0 .
docker push 220937581726.dkr.ecr.us-east-1.amazonaws.com/todo-app:1.0
```


## Creating EKS cluster: 
Documentation: https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html

```
eksctl create cluster --name demo --region us-east-1
```
The creation command will also retrieve the kubeconfig and use it, so you can directly work on the cluster.

If you created the cluster from the management console, you can get the kubeconfig by running

```
aws eks --name demo --region us-east-1 update-kubeconfig 
```

### Create docker registry secret to pull images from ECR

```
kubectl create secret docker-registry aws-ecr \
  --docker-server=220937581726.dkr.ecr.us-east-1.amazonaws.com \
  --docker-username=AWS \
  --docker-password=$(aws ecr get-login-password)
```

### Deploy application
```
kubectl apply -f deployment.yml

kubectl get svc
kubectl get po
```

