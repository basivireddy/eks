


kubectl get nodes --kubeconfig ~/.kube/config


1. To see the configuration of your AWS CLI user or role, run the following command:
```
$ aws sts get-caller-identity
```

The output returns the Amazon Resource Name (ARN) of the AWS Identity and Access Management (IAM) user or role. For example:

```
{
    "UserId": "XXXXXXXXXXXXXXXXXXXXX",
    "Account": "XXXXXXXXXXXX",
    "Arn": "arn:aws:iam::XXXXXXXXXXXX:user/testuser"
}
```

2.    Confirm that the ARN matches the cluster creator.

3.    Update or generate the kubeconfig file using one of the following commands.

As the IAM user, run the following command:

```
aws eks --region us-west-2 update-kubeconfig --name eg-test-eks-cluster
```

4. To edit aws-auth ConfigMap in a text editor, the cluster owner or admin must run the following command:

```
$ kubectl edit configmap aws-auth --namespace kube-system
```

5. To add an IAM user or IAM role, complete one of the following steps.

Add the IAM user to mapUsers. For example:

```
mapUsers: |
  - userarn: arn:aws:iam::XXXXXXXXXXXX:user/testuser
    username: testuser
    groups:
      - system:masters

```

### Create a cluster role and cluster role binding, or a role and role binding

1.    Download the manifest file:
```
https://s3.us-west-2.amazonaws.com/amazon-eks/docs/eks-console-full-access.yaml
```
2.    Deploy the manifest file:
```
$ kubectl apply -f eks-console-full-access.yaml
```
3.    Update your aws-auth ConfigMap with the new group eks-console-dashboard-full-access-group for your IAM entity:
```
$ kubectl edit configmap aws-auth -n kube-system
```

4.    Add the IAM user to mapUsers. For example:
```
mapUsers: |
  - userarn: arn:aws:iam::XXXXXXXXXXXX:user/testuser
    username: testuser
    groups:
    - system:bootstrappers
    - system:nodes
    - eks-console-dashboard-full-access-group
```
5.    Add the IAM role to mapRoles. For example:
```
mapRoles: |
  - rolearn: arn:aws:iam::XXXXXXXXXXXX:role/testrole
    username: testrole    
    groups:
    - system:bootstrappers
    - system:nodes
    - eks-console-dashboard-full-access-group
```

### Reference links:

1. https://aws.amazon.com/premiumsupport/knowledge-center/eks-kubernetes-object-access-error/
2. https://aws.amazon.com/premiumsupport/knowledge-center/eks-api-server-unauthorized-error/

