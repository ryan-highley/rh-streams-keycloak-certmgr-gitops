# rh-streams-keycloak-certmgr-gitops
Demo deployment of Red Hat Streams for Apache Kafka, Red Hat build of Keycloak, cert-manager Operator for Red Hat OpenShift, and Red Hat GitOps Operator


# Openshift Cluster Config

This repo sets up OpenShift with Day 2 thingys via Red Hat OpenShift GitOps and ArgoCD


## Installing ArgoCD

First apply the `Subscription` manifest for the Red Hat OpenShift GitOps Operator

```shell
oc apply -k bootstrap
```

Wait for the pods to appear

```shell
echo -n "Still waiting" ; until [[ $(oc get pods -n openshift-gitops -o name  | wc -l) -ne 0 ]]; do echo -n "."; sleep 3; done; echo ""
```

Wait for the deployment rollout

```shell
oc rollout status deploy/openshift-gitops-server -n openshift-gitops
```

Add cluster-admin role to the ArgoCD service account

```shell
oc adm policy add-cluster-role-to-user cluster-admin -z openshift-gitops-argocd-application-controller -n openshift-gitops
```

To get the admin password

```shell
oc get secret openshift-gitops-cluster -n openshift-gitops -ojsonpath='{.data.admin\.password}' | base64 -d ; echo
```

## Deploying this Repo

To configure your cluster to this repo run `oc apply` on any available ArgoCD profile directory

```
oc apply -k https://github.com/ryan-highley/rh-streams-keycloak-certmgr-gitops/argocd/cluster-config/profiles/default
oc apply -k https://github.com/ryan-highley/rh-streams-keycloak-certmgr-gitops/argocd/app-config/profiles/default
```

## Default users and passwords

| User | Password | Permissions |
| ---- | ---- | ---- |
| admin | `r3dh4t1!` | `cluster-admin` |
| developer | `developer` | None |

Replace the base64-encoded `htpasswd` contents in `cluster-config/security/htpass-auth/Secret_htpass-secret.yaml` to change passwords and/or available users.

Replace the contents of `cluster-config/security/rbac` to change group membership and/or role bindings.
