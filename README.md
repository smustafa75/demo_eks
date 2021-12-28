# Demo EKS Cluster
The purpose of this repo is to provison a fully functions EKS clutser in Amazon.
Terraform is heart of this implementation.

Thanks to **Hashcop** learning for this awesome content.

## Important post creation commands

aws eks --region $(terraform output -raw region) update-kubeconfig --name $(terraform output -raw cluster_name)

Note:- if you get error 'NoneType', delete the config file located in .kube folder and run command again

### Check Cluster Status
aws eks --region REGION describe-cluster --name CLUSTERNAME --query cluster.status

### Download Dashboard

wget -O v0.3.6.tar.gz https://codeload.github.com/kubernetes-sigs/metrics-server/tar.gz/v0.3.6 && tar -xzf v0.3.6.tar.gz

kubectl apply -f /PATH_TO_UNZIP_FILE/deploy/1.8+/

kubectl get deployment metrics-server -n kube-system
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta8/aio/deploy/recommended.yaml

### Launch Dashboard locally

kubectl proxy

In browser type below URL  
http://127.0.0.1:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

Note:- The proxy command shall keep running until u press Ctrl+C

### How to Login
Launch a separate window
kubectl apply -f https://raw.githubusercontent.com/hashicorp/learn-terraform-provision-eks-cluster/main/kubernetes-dashboard-admin.rbac.yaml
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep service-controller-token | awk '{print $1}')

- Click on **Token** and paste the token to login into dashboard

**Update** 28 Dec, 2021
- Added FLUEINTBIT.SH, once cluster is up execute it to get metrics in CloudWatch. Be sure to replace clustername and region.
- By default, the IAM Role for EKS does not have logging enabled in the policy so you have to do it manually. In addition do add CloudwatchFullAccess policy.
- Attach CloudWatchFullAccess policy to the IAM role created for EC2 instances as well.

**Testing fun**
- Add two cron jobs and see the fun on CloudWatch Insights

a. kubectl create cronjob hello --image=busybox   --schedule="*/1 * * * *" -- echo "hello World"
b. kubectl create cronjob bye --image=busybox   --schedule="*/2 * * * *" -- echo "Bye World"