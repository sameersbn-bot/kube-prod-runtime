# Installer for Production Runtime

Usage:

```sh
# Find supported platform
kubeprod list-platforms

# Install to cluster given by current kubectl context
kubeprod install --platform=$platform
```

## Development

Requires a typical golang development environment.  To build:

```sh
go get github.com/bitnami/kube-prod-runtime/kubeprod
```

For local development against minikube:

```sh
minikube start --kubernetes-version=v1.9.0

./kubeprod -v install --platform=minikube-0.25+k8s-1.9 --manifests=../manifests
```

For deployment against Azure Kubernetes Service (AKS):

```sh
AKS_CLUSTER_NAME="<your Kubernetes cluster name>"       # e.g. "k8s"
AZURE_RESOURCE_GROUP_NAME="<your resource group name>"  # e.g. $USER
AZURE_DNS_ZONE="<your delegated DNS zone>"              # e.g. "my.example.com"

# First, create the AKS cluster (only if it does not exist yet) ...
az aks create --resource-group "${AZURE_RESOURCE_GROUP_NAME}" --name "${AKS_CLUSTER_NAME}" --node-count 3 --node-vm-size Standard_DS2_v2 --ssh-key-value ~/.ssh/id_rsa.pub --kubernetes-version 1.9.6
# ...and populate ~/.kube/config (required to use the Kubernetes API)
az aks get-credentials --resource-group "${AZURE_RESOURCE_GROUP_NAME}" --name "${AKS_CLUSTER_NAME}"

# Run "kubeprod" to populate the AKS cluster
cd ${GOPATH:-$HOME/go}/src/github.com/bitnami/kube-prod-runtime/kubeprod
make
./bin/kubeprod install \
    --email "<your e-mail address>" \
    --manifests ../manifests \
    --platform aks+k8s-1.9 \
    --dns-zone "${AZURE_DNS_ZONE}" \
    --dns-resource-group "${AZURE_RESOURCE_GROUP_NAME}"
```