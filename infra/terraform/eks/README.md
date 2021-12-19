NOTE: This full configuration utilizes the [Terraform http provider](https://www.terraform.io/docs/providers/http/index.html) to call out to icanhazip.com to determine your local workstation external IP for easily configuring EC2 Security Group access to the Kubernetes servers. Feel free to replace this as necessary.


This needs to be updated to include more stringent security for kubectl access and locking down ports.


Change the nodes in eks-worker-nodes depending on workload. 