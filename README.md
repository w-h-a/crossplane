# README

```bash
kind create cluster
kubectl create namespace crossplane-system
helm install crossplane --namespace crossplane-system crossplane-stable/crossplane --version 1.6.2
kubectl crossplane install provider crossplane/provider-aws:v0.23.0
kubectl -n crossplane-system create secret generic aws-credentials --from-file=credentials=./secrets.conf
kubectl apply -f config.yaml
```

```bash
cd eks
kubectl crossplane build configuration
```

Create a private ECR repo and login.

```bash
kubectl crossplane push configuration REPO:TAG
kubectl crossplane install configuation REPO:TAG
kubectl apply -f compositeresourcedefinition.yaml
kubectl apply -f composition.yaml
cd ..
```

Make a file named "eks.yaml" with (and fill out cluster and workernode roles):

```yaml
---
apiVersion: eks.sarathy.io/v1beta1
kind: EKSCluster
metadata:
  name: crossplane-cluster
spec:
  parameters:
    region: us-west-2
    vpc-name: 'EKS-CROSSPLANE-VPC'
    vpc-cidrBlock: '192.168.48.0/20'

    subnet1-public-name: 'PUBLIC-WORKER-1'
    subnet1-public-cidrBlock: '192.168.48.0/24'
    subnet1-public-availabilityZone: 'us-west-2a'

    subnet2-public-name: 'PUBLIC-WORKER-2'
    subnet2-public-cidrBlock: '192.168.49.0/24'
    subnet2-public-availabilityZone: 'us-west-2b'

    subnet1-private-name: 'PRIVATE-WORKER-1'
    subnet1-private-cidrBlock: '192.168.50.0/24'
    subnet1-private-availabilityZone: 'us-west-2a'

    subnet2-private-name: 'PRIVATE-WORKER-2'
    subnet2-private-cidrBlock: '192.168.51.0/24'
    subnet2-private-availabilityZone: 'us-west-2b'

    cluster-role: arn:aws:iam::XXXX:role/EKS-Cluster-Role
    workernode-role: arn:aws:iam::XXXX:role/EKS-WorkerNode-Role

    k8s-version: '1.21'
    workload-type: 'non-gpu'
    workers-size: 2

  compositionRef:
    name: amazon-eks-cluster

  writeConnectionSecretToRef:
    namespace: eks
    name: crossplane-cluster-connection
```

```bash
kubectl apply -f eks.yaml
```

To look at various resources

```bash
kubectl get crossplane
kubectl get managed
kubectl get composite
```

Cleanup

```bash
kubectl delete -f eks.yaml
```
