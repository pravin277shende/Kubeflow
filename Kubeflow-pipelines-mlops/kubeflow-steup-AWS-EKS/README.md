# Psitron Kubeflow pipelines 

# Deploy Kubeflow on AWS EKS

## Prerequisites:

## Docker
Create a Ubuntu environment using Docker. 

* Pull the latest version of the Ubuntu image of your choice.
``docker pull ubuntu:18.04``
* Connect to localhost from your container:
``docker container run -it -p 127.0.0.1:8080:8080 ubuntu:18.04``
* Download the latest package information:
``apt update``
* Install the necessary tools:
``apt install git curl unzip tar make sudo vim wget -y``

## Clone repository
Clone the ``awslabs/kubeflow-manifests`` and the ``kubeflow/manifests`` repositories and check out the release branches of your choosing.

Substitute the value for ``KUBEFLOW_RELEASE_VERSION``(e.g. v1.7.0) and ``AWS_RELEASE_VERSION``(e.g. v1.7.0-aws-b1.0.3) with the tag or branch you want to use below. Read more about releases and versioning if you are unsure about what these values should be.

```
export KUBEFLOW_RELEASE_VERSION=v1.7.0
export AWS_RELEASE_VERSION=v1.7.0-aws-b1.0.3
git clone https://github.com/awslabs/kubeflow-manifests.git && cd kubeflow-manifests
git checkout ${AWS_RELEASE_VERSION}
git clone --branch ${KUBEFLOW_RELEASE_VERSION} https://github.com/kubeflow/manifests.git upstream
```
## Install necessary tools 
Install the necessary tools with the following command:

``make install-tools``

```
#NOTE: If you have other versions of python installed 
#then make sure the default is set to python3.8
alias python=python3.8
```

The `make` command above installs the following tools:

* AWS CLI - A command line tool for interacting with AWS services.
* eksctl - A command line tool for working with EKS clusters.
* kubectl - A command line tool for working with Kubernetes clusters.
* yq - A command line tool for YAML processing. (For Linux environments, use the wget plain binary installation)
* jq - A command line tool for processing JSON.
* kustomize version 5.0.1 - A command line tool to customize Kubernetes objects through a kustomization file.
* python 3.8+ - A programming language used for automated installation scripts.
* pip - A package installer for python.
* terraform - An infrastructure as code tool that lets you develop cloud and on-prem resources.
* helm - A package manager for Kubernetes

## Configure AWS Credentials and Region for Deployment 
To access AWS services, you need an AWS account and setup IAM credentials. Follow AWS CLI Configure Quickstart documentation to setup your IAM credentials.

Your IAM user/role needs the necessary privileges to create and manage your cluster and dependencies. You might want to grant `Administrative Privileges` as it will require access to multiple services.

Run the following command to configure AWS CLI:

```
aws configure --profile=kubeflow
# AWS Access Key ID [None]: <enter access key id>
# AWS Secret Access Key [None]: <enter secret access key>
# Default region name [None]: <AWS region>
# Default output format [None]: json

# Set the AWS_PROFILE variable with the profile above
export AWS_PROFILE=kubeflow

```

Once your configuration is complete, run `aws sts get-caller-identity` to verify that AWS CLI has access to your IAM credentials.

## Cluster setup

Change this according to your usecase
* **Cluster Name**: kubeflow-srvr
* **Region**: us-east-1

`eksctl create cluster --name kubeflow-srvr --region us-east-1 --ssh-access --ssh-public-key=kubeflow-server --node-type=t2.2xlarge --nodes=2 --node-volume-size=100 --node-volume-type=gp2 --version=1.23`

EBS CSI setup link here. Create IAM OpenID connect provider:

`eksctl utils associate-iam-oidc-provider --cluster kubeflow-srvr --approve`

Create the Amazon EBS CSI driver IAM role for service account

```
eksctl create iamserviceaccount \
  --name ebs-csi-controller-sa \
  --namespace kube-system \
  --cluster kubeflow-srvr \
  --attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy \
  --approve \
  --role-only \
  --role-name AmazonEKS_EBS_CSI_DriverRole
```
Add the Amazon EBS CSI add-on

* **Chnage your AWS account ID**

`eksctl create addon --name aws-ebs-csi-driver --cluster kubeflow-srvr --service-account-role-arn arn:aws:iam::[aws-account-id]:role/AmazonEKS_EBS_CSI_DriverRole --force`

* (optional)Deploy sample application to check whether cluster working properls

```
git clone https://github.com/kubernetes-sigs/aws-ebs-csi-driver.git
cd aws-ebs-csi-driver/examples/kubernetes/dynamic-provisioning/
kubectl apply -f manifests/
kubectl get pods --watch
kubectl get pv
```
Remove sample application

`kubectl delete -f manifests/`

Expose your cluster & Region

```
export CLUSTER_NAME=kubeflow-srvr
export CLUSTER_REGION=us-east-1
```

Now go directory `kubeflow-manifests` in you root 
`cd kubeflow-manifests`

## Vanilla Installation for Kubeflow on EKS

**Install with a single command**
kustomize method 

`make deploy-kubeflow INSTALLATION_OPTION=kustomize DEPLOYMENT_OPTION=vanilla`

**Connect to your Kubeflow cluster**

```
kubectl get pods -n cert-manager
kubectl get pods -n istio-system
kubectl get pods -n auth
kubectl get pods -n knative-eventing
kubectl get pods -n knative-serving
kubectl get pods -n kubeflow
kubectl get pods -n kubeflow-user-example-com

```

**Connect to your Kubeflow Dashboard**
You can now start experimenting and running your end-to-end ML workflows with Kubeflow on AWS!

For information on connecting to your Kubeflow dashboard depending on your deployment environment, see Connect to your Kubeflow Dashboard.

## Using Docker to connect 
Run the following command:

`make port-forward IP_ADDRESS=0.0.0.0`

You can then open the browser and go to http://localhost:8080/.

Perfect now you can access your Kubeflow dashboard



<!-- license -->
## License
Distributed under the MIT License. See ``LICENSE.md`` for more information.

