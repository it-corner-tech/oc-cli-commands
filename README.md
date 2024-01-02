# oc-cli-commands

Openshift CLI genearl commands with examples and cheat sheet

## OC AUTO-COMPLETION

### OC AUTO-COMPLETION BASH

```bash
oc completion bash > ~/.oc_bash_completion.sh
echo "source .oc_bash_completion.sh" >> ~/.bashrc
source ~/.bashrc
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

## OC ADM

### OC ADM TOP

Show usage statistics of resources on the server

#### OC ADM TOP PODS

```bash
oc adm top pods -A --sum
```

#### OC ADM TOP NODE

```bash
oc adm top node
```
