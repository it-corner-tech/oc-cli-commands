# oc-cli-commands

Most common Openshift CLI commands with examples.

## OC AUTO-COMPLETION

### OC AUTO-COMPLETION BASH

```bash
oc completion bash > ~/.oc_bash_completion.sh
echo "source ~/.oc_bash_completion.sh" >> ~/.bashrc
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

### OC GET SHOW LABELS

```bash
oc get pods --show-lables
```

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
oc get pods -o custom-columns=Pod:.metadata.name,Container:.spec.containers[].name,\
Phase:.status.phase,IP:.status.podIP,Ports:.spec.containers[].ports[].containerPort
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
oc run -it my-app --image registry.access.redhat.com/ubi9/ubi\
 --restart Never --command -- /bin/bash
```

Auto delete pod:

```bash
oc run -it my-app --rm --image registry.access.redhat.com/ubi9/ubi\
 --restart Never --command -- /bin/bash
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

## OC PATCH

Update fields of a resource

```bash
oc patch pod valid-pod --type='json' \
-p='[{"op": "replace", "path": "/spec/containers/0/image", \
"value":"http://registry.access.redhat.com/ubi8/httpd-24"}]'
```

## OC CP

Copy files to and from Containers

```bash
oc cp pod:pod/dir /local/dir
```

## OC PORT-FORWARD

Remove container access

```bash
oc port-forward podname EXTERNAL_PORT:CONTAINER_POD
```

## OC RSH

Connect to running container

```bash
oc rsh pod-name
```

```bash
oc rsh deployment/my-deployment
```

## OC IMAGE

You can use this command to inspect, configure, and retrieve information about container images.

### OC IMAGE INFO

Inspects and retrieves information about a container image:

```bash
oc image info registry.access.redhat.com/ubi9/httpd-24:1-233 --filter-by-os amd64
```

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

## OC IMAGE STREAMS

### OC CREASE IS

```bash
oc create is my-is
```

### OC CREATE ISTAG

```bash
oc create istag my-is:v1.0 \
 --from-image myremote-repo/my-remote-image:tag
```

### OC SET IMAGE-LOOKUP

Enable image stream resolution for the my-is image stream so that Kubernetes resources in the current project can use it.

```bash
oc set image-lookup my-is
```

```bash
oc set image-lookup
```

### OC SET TRIGGERS

Detected changes in IS

```bash
oc set triggers deployment/my-depl \
  --from-image my-is:1 --containers my-container
```

```bash
oc set triggers deployment/my-depl
```

```bash
oc get deployment my-depl \
  -o jsonpath='{.metadata.annotations.image\.openshift\.io/triggers}' | jq .
```

## OC TAG

Update Image stream tag

```bash
oc tag myregitry/myimage:new-image-tag existing-is:existing-istag
```

Move alias to another istag

```bash
oc tag --help new-image-stream-tag existing-alias
```

## OC NEW-APP

Create a new application by specifying source code, templates, and/or images.

### OC NEW-APP FROM TEMPLATE

```bash
oc new-app -l team=red --template mysql-persistent \
  -p MYSQL_USER=developer \
  -p MYSQL_PASSWORD=developer
```

### OC NEW-APP FROM IMAGE

```bash
oc new-app --name db-image -l team=blue \
  --image registry.ocp4.example.com:8443/rhel9/mysql-80:1 \
  -e MYSQL_USER=developer \
  -e MYSQL_PASSWORD=developer \
  -e MYSQL_ROOT_PASSWORD=redhat
```

## OC CREATE

### OC CREATE JOB

```bash
oc create job \
  date-job \
  --image registry.access.redhat.com/ubi8/ubi \
  -- /bin/bash -c "date" 
```

### OC CREATE CRONJOB

```bash
oc create cronjob date-cronjob \
    --image registry.access.redhat.com/ubi8/ubi \
    --schedule "*/1 * * * *" \
    -- date
```

### OC CREATE DEPLOYMENT

```bash
oc create deployment \
    my-deployment \
    --image registry.access.redhat.com/ubi8/ubi \
    --replicas
```

### OC CREATE SECRET

From literal

```bash
oc create secret generic my_secret_name\
 --from-literal key1=value1 --from-literal key2=value2
```

From file

```bash
oc create secret generic my_secret_name\
 --from-file key1=/path/to/file
```

TLS secret

```bash
oc create secret tls my_secret_name\
 --cert /path/to/cert --key /path/to/key
```

### OC CREATE CONFIG MAPS

From literal

```bash
oc create cm my-config --from-literal key1=value1
```

## OC SET

Configure application resources. This commands help you make changes to existing application resources.

### OC SET ENV

```bash
oc set env deployment/my-db MYSQL_USER=developer \
 MYSQL_PASSWORD=developer \
 MYSQL_DATABASE=samepledb
```

From secret

```bash
oc set env deployment/my-deployment --from secret/my-secret
```

### OC SET VOLUME

Type secret

```bash
oc set volume deployment/mydeployment --add\
 --type secret --secret-name my-secret --mount-path /app-secret
```

Type configmap

```bash
oc set volume deployment/mydeployment --add\
 --type configmap --configmap-name my-configmap --mount-path /app-configs
```

Type PersistentVolumeClaim

```bash
oc set volume deployment/mydeployment --add \
 --name my-volume \
 --type persistentVolumeClaim \
 --claim-mode rwo \
 --claim-size 15Gi \
 --mount-path /var/mydata \
 --claim-class storage-class \
 --claim-name my-pvc
```

### OC SET RESOURCES

Specify compute resource requirements (cpu, memory) for any resource that defines a pod template.

```bash
oc set resources deployment my-dep --requests cpu=10m,memory=1gi
```

### OC SET PROBE

Set or remove a liveness, readiness or startup probe from a pod or pod template.

```bash
oc set prove deployment/my-deployment --readiness \
 --initial-delay-seconds 7\
 --get-url http://:8080/health
```

### OC SET IMAGE

```bash
oc set image deployment/mydeployment my-container-name-in-pod=my-image
```

## OC EXPOSE

Expose containers internally as services or externally via routes

```bash
oc expose deployment/db --port 8080
```

```bash
oc expose service nginx
```

```bash
oc expose service nginx --hostname api.aps.acme.com
```

## OC SCALE

Set a new size for a deployment, replica set, replication controller, or stateful set.

```bash
oc scale deployment test --replicas 2
```

## OC AUTOSCALE

Creates an autoscaler that automatically chooses and sets the number of pods that run in a Kubernetes cluster.

```bash
oc autoscale deployment/my-deployment --min 1 --max 8 --cpu-percent 70 --memory-percent 95
```

## OC ROLLOUT

Start a new rollout, view its status or history, rollback to a previous revision of your app.

```bash
oc rollout pause deployment/myapp
```

```bash
oc rollout resume deployment/myapp
```

```bash
oc rollout undo deployment/myapp  --to-revision 1
```

```bash
oc rollout status deployment/myapp
```

```bash
oc rollout history deployment/myapp --revision 1
```

> Note:
>  
> The CHANGE-CAUSE column provides a user-defined message that describes the revision. You can store the message in the kubernetes.io/change-cause deployment annotation after every rollout.

```bash
oc rollout history deployment/myapp --revision 1
```

## OC ANNOTATE

Update the annotations on one or more resources.

```bash
# Enable sticky session on a route
oc annotate route test router.openshift.io/cookie_name="my-sticky-session"
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
crictl ps --name my-container-name # filters the containers by name
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

## Skopeo

### List images tags

```bash
skopeo list-tags docker://repo/image
```

### Inspect image

```bash
skopeo inspect --config docker://reg/image:tag
```
