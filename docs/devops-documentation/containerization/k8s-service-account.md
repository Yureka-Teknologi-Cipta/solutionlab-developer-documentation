---
sidebar_position: 1
---

# Service-Account in Kubernetes

## Overview
ServiceAccount objects in Kubernetes. This page introduces the ServiceAccount object in Kubernetes, providing information about how service accounts work, use cases, limitations, alternatives, and links to resources for additional guidance.

### Whats are Service Accounts?
A service account is a type of non-human account that, in Kubernetes, provides a distinct identity in a Kubernetes cluster. Application Pods, system components, and entities inside and outside the cluster can use a specific ServiceAccount's credentials to identify as that ServiceAccount. This identity is useful in various situations, including authenticating to the API server or implementing identity-based security policies.

[Read More](https://kubernetes.io/docs/concepts/security/service-accounts/)

## Step 1: Create a Service Account

You can create a service account using a YAML manifest. Create a file called `service-account.yaml` with the following content:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: <name-serviceaccount>
  namespace: <namespace>
```

Apply the YAML file to create the service account:

```sh
kubectl apply -f service-account.yaml -n <namespace>
```

## Step 2: Verify the Service Account

To verify that the service account has been created, run:

```sh
kubectl get serviceaccount <name-serviceaccount> -n <namespace>
```

## Step 3: Create a Role and RoleBinding (Optional, for Permissions)

A service account alone does not have permissions. You need to create a `Role` and `RoleBinding` to grant permissions. 

Create a `role.yaml` file:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: my-role
  namespace: default
rules:
  - apiGroups:
      - ""
      - extensions
      - apps
      - batch
      - networking.k8s.io
      - autoscaling
    resources:
      - deployments
      - services
      - horizontalpodautoscalers
    verbs:
      - get
      - list
      - watch
      - create
      - update
      - patch
      - delete
```

Apply the role:

```sh
kubectl apply -f role.yaml
```

Now, create a `rolebinding.yaml` file:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: name-rolebinding
  namespace: default
subjects:
  - kind: ServiceAccount
    name: name-serviceaccount
    namespace: default
roleRef:
  kind: Role
  name: name-role
  apiGroup: rbac.authorization.k8s.io
```

Apply the role binding:

```sh
kubectl apply -f rolebinding.yaml
```

Create Service Account called `serviceaccount.yml` and then apply <br />
```sh
kubectl apply -f serviceaccount.yml -n <namespace>
```

```yaml
# serviceaccount.yml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: {{ serviceaccount-name }}
  namespace: {{ namespace }}
secrets:
  - name: {{ token-name }}
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: {{ namespace }}
  name: read-write
rules:
  - apiGroups:
      - ""
      - extensions
      - apps
      - batch
      - networking.k8s.io
      - autoscaling
    resources:
      - deployments
      - services
      - horizontalpodautoscalers
    verbs:
      - get
      - list
      - watch
      - create
      - update
      - patch
      - delete
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: {{ serviceaccount-name }}
  namespace: {{ namespace }}
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: read-write
subjects:
- kind: ServiceAccount
  name: {{ serviceaccount-name }}
  namespace: {{ namespace }}
---
apiVersion: v1
kind: Secret
metadata:
  name: {{ token-name }}
  annotations:
    kubernetes.io/service-account.name: {{ sa-name }}
type: kubernetes.io/service-account-token
```

create account deployer for deploy new image from cicd to cluster

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: deployer
  namespace: yureka-dev
secrets:
  - name: deployer-secrets
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: yureka-dev
  name: read-write
rules:
  - apiGroups:
      - ""
      - extensions
      - apps
      - batch
      - networking.k8s.io
      - autoscaling
    resources:
      - deployments
      - services
      - horizontalpodautoscalers
    verbs:
      - get
      - list
      - watch
      - create
      - update
      - patch
      - delete
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: deployer
  namespace: yureka-dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: read-write
subjects:
- kind: ServiceAccount
  name: deployer
  namespace: yureka-dev
---
apiVersion: v1
kind: Secret
metadata:
  name: deployer-secrets
  annotations:
    kubernetes.io/service-account.name: deployer
type: kubernetes.io/service-account-token
```

Create a kubeconfig file from a ServiceAccount for developer access (not for deployer).
Save the file as config in the $HOME/.kube/ directory.

To retrieve specific content, run the following command.

```bash
TOKEN=$(kubectl get secret developer-akbar-secrets -n joinsistem-dev -o jsonpath="{.data.token}")

SERVER=$(kubectl config view --minify -o jsonpath='{.clusters[0].cluster.server}')

CA_CERT=$(kubectl get secret developer-akbar-secrets -n joinsistem-dev -o jsonpath="{.data.ca\.crt}")
```

```bash
echo $TOKEN
echo $SERVER
echo $CA_CERT
```

Save it to this file.

```yaml
#$HOME./kube/config
#config
apiVersion: v1
kind: Config
clusters:
- name: yureka-cluster
  cluster:
    server: $SERVER
    certificate-authority-data: $CA_CERT
users:
- name: developer-johndoe
  user:
    token: $TOKEN
contexts:
- name: yureka-cluster
  context:
    cluster: yureka-cluster
    user: developer-johndoe
    namespace: yureka-dev
current-context: yureka-cluster
```

To test the configuration, use the command below.

```bash
kubectl get deployment -n yureka-dev
```