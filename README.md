# CNOE Workshop

## Introduction

This workshop guides you through setting up an Internal Developer Platform (IDP) using CNOE's idpBuilder. The idpBuilder is a tool that helps create a Kubernetes-based developer platform with essential services like ArgoCD and Gitea pre-configured.

## Prerequisites

Before starting the workshop, make sure you have the following:

- [Docker](https://www.docker.com/get-started) or any other container runtimes
- At least 4GB of RAM available for the container runtime
- [kubectl](https://kubernetes.io/docs/tasks/tools/) installed
- Basic understanding of Kubernetes concepts
- Terminal access

## Workshop Outline

1. Installation
2. Basic Setup
3. Exploring the Platform
4. Cleaning up the base installation
5. Adding Custom Packages
6. Advanced stuff: infrastructure provisioning
7. Deploying it in CodeSpaces

## 1. Installation

First, let's install the idpBuilder:

```bash
# Download the latest version of idpBuilder
curl -fsSL https://raw.githubusercontent.com/cnoe-io/idpbuilder/main/hack/install.sh | bash

idpbuilder version
```
See other installation options in the [idpbuilder documentation](https://cnoe.io/docs/reference-implementation/idpbuilder).

## 2. Basic Setup

Let's create a basic IDP with the core packages:

```bash
# Create a basic cluster with default settings
idpbuilder create
```

This command:
- Creates a Kind (Kubernetes in Docker) cluster
- Installs core packages (ArgoCD, Gitea)
- Sets up ingress for accessing services

Once the command completes, you can access the following UIs:
- ArgoCD: https://argocd.cnoe.localtest.me:8443/
- Gitea: https://gitea.cnoe.localtest.me:8443/

To get the login credentials:
```bash
idpbuilder get secrets
```

## 3. Exploring the Platform

Let's explore what was created:

```bash
# List the running pods
kubectl get pods -A

# List ArgoCD applications
kubectl get Applications -n argocd

# Check ingress configuration
kubectl get ingress -A
```

## 4. Cleaning up the base installation

```bash
idpbuilder delete 
```

## 5. Adding Custom Packages

You can extend your IDP with custom packages. To demonstrate this feature, let's deploy the [reference architecture](https://cnoe.io/docs/reference-implementation/idp-ref). You can find a detailed guide [here](https://cnoe.io/docs/reference-implementation/idp-ref).

```bash
# Clone the stacks repository for examples
idpbuilder create --use-path-routing \
  --package https://github.com/cnoe-io/stacks//ref-implementation
```
Keep in mind that after idpbuilder finishes, you will still need to wait around 5 minutes for all components to be up and running.

## 6. Advanced stuff: infrastructure provisioning

For this step, you will need access to an AWS account. If you don't have access to one, no worries, you can emulate this part of the workshop using localstack.

First, clone the repo with the stacks.

```bash
git clone https://github.com/cnoe-io/stacks/

```

Then modify the *./crossplane-integrations/crossplane-providers/provider-secret.yaml* file by adding your AWS access and secret keys. Ensure the key user has permissions to create S3 buckets.

Now deploy the crossplane integration:

```bash
idpbuilder create \
  --use-path-routing \
  --package https://github.com/cnoe-io/stacks//ref-implementation \
  --package ./crossplane-integrations

```

Now, if you create an AWS app from the templates (Go App with AWS resources), it will generate a bucket in S3!

If you do not have access to an AWS account, you can deploy localstack to emulate the AWS API:

```bash
idpbuilder create \
  --use-path-routing \
  --package https://github.com/cnoe-io/stacks//ref-implementation \
  --package https://github.com/cnoe-io/stacks//localstack-integration

```

You can see more details about this implementation [here](https://github.com/cnoe-io/stacks/tree/main/localstack-integration).


## 7. Deploying it in CodeSpaces

Follow [this documentation](https://github.com/cnoe-io/stacks/blob/main/ref-implementation/codespaces.md).
