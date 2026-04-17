Install OIDC Provider
```
eksctl utils associate-iam-oidc-provider \
    --region us-east-1 \
    --cluster roboshop1 \
    --approve

```
Download IAM Policy for AWS Load Balancer Controller
```
curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.2.1/docs/install/iam_policy.json

```
Create an IAM policy called AWSLoadBalancerControllerIAMPolicy
```
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam-policy.json
```
Create IAM role and ServiceAccount for AWS Load Balancer
```
eksctl create iamserviceaccount \
--cluster=roboshop1 \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::<account_id>:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--approve
```
Install drivers via HELM

Add EKS chart to helm
```
helm repo add eks https://aws.github.io/eks-charts
```

Install helm if using IAM roles
```
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=roboshop1 --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller
'''