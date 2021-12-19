example for eks cluster forked from https://github.com/hashicorp/terraform-provider-aws/tree/main/examples/eks-getting-started and modified

manifest for elb from https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-0.32.0/deploy/static/provider/aws/deploy.yaml
( might need to update the webhook checker, was giving error -- apiVersion: admissionregistration.k8s.io/v1beta1)

rocket set up from https://ajorloo.medium.com/deploy-rocket-chat-server-using-kubernetes-2d6c4228853, reduced replicas to 2


idea for webhook service:
https://github.com/galexrt/presentation-gitlab-k8s

### install aws cli, using choco, apt, whatever
$ apt install awscli

### generate key in aws, then configure awl cli
$ aws configure
< input from csv that you get from creating keys>

### run terraform in tf main directory
$ terraform init
$ terraform plan
$ terraform apply 

### get context/connect for cluster

$ aws eks --region update-kubeconfig <region> --name <name>


### create load balancer
$ kubectl apply -f infra/manifests/nlb-ingress.yaml
### Check if ingress is running ok
$ kubectl get svc -o wide -n ingress-nginx
(you can curl the FQDN and you should get nginx error )

### create rocket namespace
$ kubectl create namespace rocket
### deploy mongo stateful, add rocket namespace into metadata, also deploys a headless server for dns resolution between stateless pods
$ kubectl apply -f infra/manifests/rocket-chat/mongo.yaml


### set up db primary/replicas
#optional:
Add something like this to mongo and change script to set up primary/replicas
"""
 ---
apiVersion: batch/v1
kind: Job
metadata:
  name: mongo-rsinit
spec:
  template:
    spec:
      containers:
      - name:  mongo-rsinit
        image: mongo:4.0
        command: 
        - "/bin/bash"
        - "-c"
        - sleep 10 ;echo "rs.initiate()">init.js ; mongo --host rocketchat-db-0.rocketchat-db.default.svc.cluster.local:27017 init.js
      restartPolicy: OnFailure
  backoffLimit: 6
"""

$ kubectl exec -it rocketmongo-0 -- mongo

> rs.initiate()
> var config = rs.conf()
> config.members[0].host="rocketmongo-0:27017"
> rs.reconfig(config)
> rs.add("rocketmongo-1.rocketmongo:27017")

check status:
> rs.status()
you should see two dbs listed at the bottom

*note* this will work for non-custom domains, such as the ingress FDQN for the alb, but if you want to do a custom domain, you will need to register a domain, get certs, and add tsl to the ingress manifest. you can get rocket to work by using https://, but it won't be trusted