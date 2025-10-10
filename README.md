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

To get the admin password

```shell
oc get secret openshift-gitops-cluster -n openshift-gitops -ojsonpath='{.data.admin\.password}' | base64 -d ; echo
```

## Deploying this Repo

To configure your cluster to this repo run

```
oc apply -k https://github.com/ryan-highley/rh-streams-keycloak-certmgr-gitops/argocd/cluster-config
```
