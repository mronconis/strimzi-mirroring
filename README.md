# strimzi-mirroring


Login into live cluster
```bash
KUBECONFIG=~/.kube/config_live; oc login --token=<token> --server=<url_api>
```

Login into dr cluster
```bash
KUBECONFIG=~/.kube/config_dr; oc login --token=<token> --server=<url_api>
```

## Add role
oc adm policy add-role-to-user admin system:serviceaccount:openshift-gitops:openshift-gitops-argocd-application-controller -n kafka-live
oc adm policy add-role-to-user admin system:serviceaccount:openshift-gitops:openshift-gitops-argocd-application-controller -n kafka-dr


## Create MM2 secrets

Create secret for live mirroring user on dr
```bash
export LIVE_MIRRORING_USER_PASSWORD=$(KUBECONFIG=~/.kube/config_live oc get secret mirroring-user -n kafka-live -o jsonpath='{.data.password}' | base64 -d)

KUBECONFIG=~/.kube/config_dr oc create secret generic live-mirroring-user -n kafka-dr --from-literal=password=$LIVE_MIRRORING_USER_PASSWORD
```

Create secret for live mirroring user on dr
```bash
export DR_MIRRORING_USER_PASSWORD=$(KUBECONFIG=~/.kube/config_dr oc get secret mirroring-user -n kafka-live -o jsonpath='{.data.password}' | base64 -d)

KUBECONFIG=~/.kube/config_live oc create secret generic live-mirroring-user -n kafka-dr --from-literal=password=$DR_MIRRORING_USER_PASSWORD
```