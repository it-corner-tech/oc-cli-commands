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

## OC PROJECT

Create new project:

```bash
oc new-project test
```

Fetch project info like UID and GID:

```bash
oc describe project test
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
oc get pods -o custom-columns=Pod:.metadata.name,Container:.spec.containers[].name,Phase:.status.phase,IP:.status.podIP,Ports:.spec.containers[].ports[].containerPort
```

### OC GET YAML

```bash
oc get pods -o yaml | yq -r - 'items[*].metadata.name'
```

### OC GET TEMPLATE

```bash
oc get pod my-pod -o template --template={{.spec.containers[0].image}}
```

## OC RUN

Run a pod and attach a session:

```bash
oc run -it my-app --image registry.access.redhat.com/ubi9/ubi --command -- /bin/bash
```

Add restart option:

```bash
oc run -it my-app --image registry.access.redhat.com/ubi9/ubi --restart Never --command -- /bin/bash
```

Auto delete pod:

```bash
oc run -it my-app --rm --image registry.access.redhat.com/ubi9/ubi --restart Never --command -- /bin/bash
```

Add environment variables:

```bash
oc run -it my-app \
--env MY_VAR=myenvvariable \
--rm \
--image registry.access.redhat.com/ubi9/ubi \
--restart Never \
--command -- /bin/bash
```

## OC EXEC

Execute a command in a running container:

```bash
oc exec my-app -- date
```

Select the container if the POD has multiple containers

```bash
oc exec my-app -c contaier1 -- date
```

Attach a session to a running container

```bash
oc exec my-app -c contaier1 -it -- bash
```

## OC LOGS

Retrieve the logs of a container:

```bash
oc logs my-app --tail=10
```

Retrieve the logs of the previous container instance if exists:

```bash
oc logs my-app --tail=10 -p
```

Follow the logs

```bash
oc logs my-app --tail=10 -f
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
crictl ps # list containers on the node
crictl ps --name  # filters the containers by name
crictl ps --name my-container-name -o json | jq .containers[0].id # Gets the contaier ID <e.g 27943ae4f3024>
crictl inspect -o json 27943ae4f3024 | jq .info.pid # Gets the container PID <e.g 43453>
crictl inspect 27943ae4f3024 | grep pid # Gets the container PID as well <e.g 43453>
lsns -p 43453 # lists the system namespaces of a container.
nsenter -t 43453 -p -r ps -ef # executes the ps -ef command within the process namespace of a running container.
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
