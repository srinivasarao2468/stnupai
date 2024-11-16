#### To download IAM policy for AWS loadbancer controller to access AWS APIs
```
curl -o iam_policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.6.1/docs/install/iam_policy.json
```
#### Create IAM policy with previously downloaded file
```
aws iam create-policy \
--policy-name AWSLoadBalancerControllerIAMPolicy \
--policy-document file://iam_policy.json
```
#### Create service account for AWS loadbancer controller
```
eksctl create iamserviceaccount \    
--cluster=realtime-project \  
--namespace=kube-system \  
--name=aws-load-balancer-controller \  
--attach-policy-arn=arn:aws:iam::886436952292:policy/AWSLoadBalancerControllerIAMPolicy \  
--override-existing-serviceaccounts \  
--approve
--region us-west-2
```

#### Run one of the following commands to verify that the new service role is created:
```
kubectl get serviceaccount aws-load-balancer-controller --namespace kube-system
```
### Install the AWS Load Balancer Controller with Helm
#### To add the Amazon EKS chart to Helm, run the following command:
```
helm repo add eks https://aws.github.io/eks-charts
```
#### Update the repo to pull the latest chart:
```
helm repo update eks 
```
#### Run following command to install the Helm chart . Note: Replace clusterName, region and vpcId with your values:
```
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \      
--set clusterName=realtime-project \  
--set serviceAccount.create=false \  
--set region=us-west-2 \  
--set vpcId=vpc-0c92581c35d4f7087 \  
--set serviceAccount.name=aws-load-balancer-controller \  
-n kube-system
```

#### Verify that the controller is installed successfully:
```
kubectl get deployment -n kube-system aws-load-balancer-controller
```

#### To test ingress run the below command
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.6.1/docs/examples/2048/2048_full.yaml
```