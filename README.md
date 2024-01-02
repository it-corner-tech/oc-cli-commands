# oc-cli-commands

Most common Openshift CLI commands with examples.

## OC AUTO-COMPLETION

### OC AUTO-COMPLETION BASH

```bash
oc completion bash > ~/.oc_bash_completion.sh
echo "source .oc_bash_completion.sh" >> ~/.bashrc
source ~/.bashrc
```

## OC LOGIN

```bash
oc login -u user -p password https://ocp-api
```

### OC LOGIN ALIAS

```bash
cp ~/.bashrc ~/.bashrc.bak
echo 'alias ocplogin="oc login -u user -p password https://ocp-api"' >> ~/.bashrc
source ~/.bashrc
```

## OC VERSION

```bash
oc version
```

```bash
oc cluster-info
```

```bash
oc api-versions
```

## OC GET

### OC GET JSON

```bash
oc get pod my-pod -o json | jq .spec.containers[0].image
```

### OC GET JSONPATH

```bash
oc get pod my-pod -o jsonpath --template={.spec.containers[0].image}
```

```bash
oc get node master01 -o jsonpath=\
'{"Allocatable:\n"}{.status.allocatable}{"\n\n"}{"Capacity:\n"}{.status.capacity{"\n"}'
```

### OC GET GO-TEMPLATE

```bash
oc get pod my-pod -o go-template='{{(index .spec.containers).image}}'
```

### OC GET CUSTOM COLUMNS

```bash
oc get pods -o custom-columns=Pod:.metadata.name,Container:.spec.containers[].name,Phase:.status.pahse,IP:.status.podIP,Ports:.spec.containers[].ports[].containerPort
```

### OC GET YAML

```bash
oc get pods -o yaml | yq -r - 'items[*].metadata.name'
```

### OC GET TEMPLATE

```bash
oc get pod my-pod -o template --template={{.spec.containers[0].image}}
```

## OC EVENTS

```bash
oc get events --sort-by='.metadata.creationTimestamp'
```

```bash
oc get events --sort-by='.metadata.creationTimestamp' -A
```

```bash
oc get events --sort-by='.lastTimestamp'
```

```bash
oc get events -A --output-watch-events=true --watch-only
```

## OC EXPLAIN

```bash
oc explain pod.spec --recursive
```

```bash
oc explain pod.spec.securityContext --recursive
```

## OC RESOURCES

```bash
oc api-resources
```

```bash
oc api-resources --namespaced
```

```bash
oc api-resources --api-group ''
```

```bash
oc api-resources --api-group 'oauth.openshift.io'
```

## OC OPERATORS

### OC GET OPERATORS

```bash
oc get operators
```

```bash
oc get clusteroperators
```

### OC DESCRIBE OPERATOR

```bash
oc describe clusteroperators openshift-apiserver
```

## OC ADM

### OC ADM TOP

Show usage statistics of resources on the server

#### OC ADM TOP PODS

```bash
oc adm top pods -A --sum
```

```bash
oc adm top pods etcd-master01 -n openshift-etcd --containers
```

#### OC ADM TOP NODE

```bash
oc adm top node
```

### OC ADM NODE-LOGs

```bash
oc adm node-logs master01
```

```bash
oc adm node-logs master01 -u crio
```

```bash
oc adm node-logs master01 -u crio --tail 10
```

## OC DEBUG

### OC DEBUG NODE

```bash
oc debug node/master01

chroot /host
systemctl status crio
systemctl is-active crio
systemctl status kubelet
systemctl is-active kubelet
```

### OC MUST-GATHER

```bash
oc adm must-gather --dest-dir /tmp
```

### OC INSPECT

```bash
oc adm inspect clusteroperator/kube-apiserver --dest-dir /tmp/
```

```bash
oc adm inspect clusteroperator/kube-apiserver --dest-dir /tmp/ --since 5m
```
