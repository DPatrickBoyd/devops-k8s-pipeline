example for eks cluster forked from https://github.com/hashicorp/terraform-provider-aws/tree/main/examples/eks-getting-started and modified

manifest for elb from https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-0.32.0/deploy/static/provider/aws/deploy.yaml
( might need to update the webhook checker, was giving error -- apiVersion: admissionregistration.k8s.io/v1beta1)

rocket set up from https://ajorloo.medium.com/deploy-rocket-chat-server-using-kubernetes-2d6c4228853, reduced replicas to 2

### install aws cli
apt install awscli

### generate key in aws, then configure awl cli
aws configure
< input from csv that you get from creating keys>

### run terraform in tf main directory
terraform init
terraform plan
terraform apply 

### get context/connect for cluster

> awk eks --region update-kubeconfig <region> --name <name>


### create load balancer
kubectl apply -f infra/manifests/nlb-ingress.yaml
### Check if ingress is running ok
kubectl get svc -o wide -n ingress-nginx
(you can curl the FQDN and you should get nginx error )

### create rocket namespace
kubectl create namespace rocket
### deploy mongo stateful, add namespace into metadata
kubectl apply -f infra/manifests/mongo.yaml
