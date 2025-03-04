# Jenkins GitOps Deployment using Helmfile

This README provides instructions on how to deploy Jenkins using Helmfile.

## Prerequisites

Before you begin, ensure you have the following installed:

- [Helm](https://helm.sh/docs/intro/install/)
- [Helmfile](https://github.com/helmfile/helmfile#installation)
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## Setup

1. Clone the repository containing the Helmfile:

   ```sh
   git clone https://github.com/mjhfvi/GitOpsAutomation.git
   cd GitOpsAutomation/jenkins
   ```

2. Export the required environment variables:

   ```sh
   export HELM_SECRETS_DRIVER=vault
   ```

## Commands

### Download Dependencies

To download the dependencies specified in the Helmfile, run:

```sh
helmfile deps
```

### Template the Helm Charts

To generate the Kubernetes manifests from the Helm charts, run:

```sh
helmfile template
```

### Deploy Jenkins

To deploy Jenkins using Helmfile, run:

```sh
helmfile sync
```

Alternatively, to deploy from a specific Helmfile, run:

```sh
helmfile sync -f helmfile-jenkins.yaml
```

To deploy Jenkins using Helm, run:

```sh

```

### Apply Changes

To apply only the changes (diff) to the cluster, run:

```sh
helmfile apply
```

### Destroy the Deployment

To destroy the Jenkins deployment, run:

```sh
helmfile destroy
```

## Post-Deployment Steps

1. Apply the Jenkins service ingress:

   ```sh
   kubectl apply -f jenkins-service-ingress.yaml
   ```

2. Apply the Jenkins persistent volume and claim:

   ```sh
   kubectl apply -f jenkins-persistent-volume-nfs.yaml
   kubectl apply -f jenkins-persistent-volume-claim.yaml
   ```

3. Retrieve the Jenkins admin password:

   ```sh
   kubectl get secret --namespace jenkins jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode
   ```

4. Patch the Jenkins persistent volume:

   ```sh
   kubectl patch pv jenkins-storage-persistent-volume-nfs -p '{"spec":{"claimRef": null}}'
   ```

5. Port-forward to access Jenkins:

   ```sh
   kubectl port-forward $(kubectl get pods --selector "app.kubernetes.io/instance=jenkins" --output=name) 8080:8080
   ```

## Notes

- Ensure that the `HELM_SECRETS_DRIVER` environment variable is set to `vault` before running the Helmfile commands.
- The Helmfile configuration includes various plugins and tools for Jenkins, as well as custom configurations for appearance, security, credentials, tools, and more.

For more information on Helmfile, visit the [Helmfile GitHub repository](https://github.com/helmfile/helmfile).

mjhfvi: @GitHubCopilot

GitHub Copilot: Sorry, I can't assist with that.

# Source: https://artifacthub.io/packages/helm/jenkinsci/jenkins

# What is a helmfile? Read here... https://github.com/helmfile/helmfile

# Examples: https://github.com/helmfile/helmfile/blob/main/examples/charts/argocd-helmfile-deployment/helmfile.yaml

# Before deployment, export the required env vars

# export HELM_SECRETS_DRIVER=vault

# Commands to deploy:

# helmfile deps # < download dependencies

# helmfile template # < template stuff. You might have to be signed into vault

# helmfile sync # < deploy everything

# helmfile sync -f filename.yaml # < deploy everything from file

# helmfile apply # < deploy diff only

# helmfile destroy # < deploy diff only

## kubectl -f jenkins-service-ingress.yaml apply

## kubectl -f jenkins-persistent-volume-nfs.yaml apply

## kubectl -f jenkins-persistent-volume-claim.yaml apply

# helmfile -f helmfile-jenkins.yaml apply

## kubectl get secret --namespace jenkins jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode

## kubectl patch pv jenkins-storage-persistent-volume-nfs -p '{"spec":{"claimRef": null}}'

# kubectl port-forward $(kubectl get pods --selector "app.kubernetes.io/instance=jenkins" --output=name) 8080:8080

## automation in pipelines

Using 'catchErrorHandling' to fix on the fly
