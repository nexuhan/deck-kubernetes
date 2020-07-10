# Pods 

<details>
<summary>
<div>What�s a *Pod*</div>
</summary>
<div>A&nbsp;*Pod *is a group of one or more&nbsp;containers with shared storage/network, and a specification for how to run the containers.

*<img src="m6OgY8HdeAOI59RmeT0Ue6ortvnFbTmq9hg2duJnAKD0WYGQpN1xDCWQ9_fNi3fbYYd4c0FESi-jtoTt2B4W7gsYqaYD2mrHIqt9T8OEItsIIJfKB_5cHdhvG5ULq_TOqCI1.png">*
</div>
</details>

<details>
<summary>
Create a temp busybox pod and connect via wget to the "foo" service. Verify that each time there's a different hostname returned (deployment with 3 replicas).
</summary>
<div>Get the service ClusterIP
<i>kubectl get svc</i></div><i>kubectl run temppod --image=busybox --restart=Never --rm -it -- sh</i>

Once a service is running, you can access it via the service name (DNS)
<i>wget -O- foo:6262
wget -O- &lt;ServiceClusterIP&gt;:6262</i>
</details>

<details>
<summary>
Create busybox pod with two containers, each one will have the image busybox and will run the 'sleep 3600' command. Make both containers mount an emptyDir at '/etc/foo'.
</summary>
Best way to do this is create a YAML template (single-container pod) and duplicate the container definition (make sure to change the name of the second container).<div>
</div><div><i>kubectl run busybox --image=busybox --restart=Never -o yaml --dry-run -- /bin/sh -c 'sleep 3600' &gt; pod.yaml</i>
</div><div><i>
</i></div><div>Add the <i>spec.volumes </i>and the <i>spec.containers.volumeMounts </i>sections to the YAML (make sure to mount in both containers)

emptyDir volume looks like:
<i>spec:
&nbsp; volumes:</i></div><div><i>&nbsp; - name: myvol</i></div><div><i>&nbsp; &nbsp; emptyDir: {}
</i></div>
</details>

<details>
<summary>
You have a two-container pod with a shared volume at '/etc/foo' on both containers.
Connect to the second busybox, write the first column of '/etc/passwd' file to '/etc/foo/passwd'. Connect to the first busybox and write '/etc/foo/passwd' file to standard output.
</summary>
Connect to the second container:
<i>kubectl exec -it busybox -c busybox2 -- /bin/sh</i><div><div>/etc/passwd is colon delimited, use cut to get the first column</div><div><i>cut -f 1 -d ':' /etc/passwd &gt; /etc/foo/passwd</i></div><div><i>cat </i><i>/etc/foo/passwd</i></div><div><div><i>exit
</i>
Connect to the first container:
<i>kubectl exec -it busybox -c busybox -- /bin/sh</i></div><div>confirm the mounting</div><div><i>mount | grep foo</i></div><div><i>cat /etc/foo/passwd</i></div><div><i>exit</i>
</div></div></div>
</details>

<details>
<summary>
Create a busybox pod with 'sleep 3600' as arguments. Copy '/etc/passwd' from the pod to your local folder.
</summary>
Use the <i>kubectl cp &lt;pod&gt;:&lt;path&gt; &lt;dest&gt; </i>command.

<i>kubectl run busybox --image=busybox --restart=Never -- sleep 3600</i><div>using "." as the destination to drop it into current folder
<div><i>kubectl cp busybox:/etc/passwd .</i></div><div><i>cat passwd</i></div></div>
</details>

<details>
<summary>
A Kubernetes concept that represents the smallest unit of deployment.
</summary>
Pod
</details>

<details>
<summary>
Mounted directories that are accessible from inside containers.
</summary>
Volumes
</details>

<details>
<summary>
The command to get Pod logs in Kubernetes.
</summary>
`kubectl get logs`
</details>

<details>
<summary>
<div style="">Provide command to list all Kubernetes pods in the namespace</div>
</summary>
kubectl get pods

*<img src="FezLZbLjz3LT7Bln0xYjl6Z10-ai2OT1U9S5LkMmhNtj-0QuEfayELPDmiiVMrzhrdywH9DUsygN-GFkm89DYnmUqp7Q306g7tdiyOkXuHWd6heoI5Ojy8EIDffXyG4NxzFJ.png">*

</details>

<details>
<summary>
When annoying to have to keep typing&nbsp;kubectl get pods...&nbsp;every few seconds to see if anything�s happened.

</summary>
kubectl&nbsp;provides the&nbsp;--watch&nbsp;flag (-w&nbsp;for short)
kubectl get pods --watch

</details>

<details>
<summary>
Can we edit live pod cofiguration?
</summary>
Yes, but not recommended due to out-of-syc

<strong>kubectl edit deployments my-deployment</strong>

</details>

<details>
<summary>
How to execute Commands on Containers

</summary>
kubectl container run --image alpaine --command -- sleep 999<div>kubectl get pods</div><div>
</div><div>#NAME READY STATUS RESTARTS AGE&nbsp;</div><div>#alpine-7fd44fc4bf-7gl4n 1/1 Running 0 4s
</div><div>
</div><div>kubectl exec -it alpine-7fd44fc4bf-7gl4n /bin/sh</div><div>
</div><div>#will run the command in the first container by default, when more than one conainer present
</div><div>
</div>
</details>

<details>
<summary>
What are PodPresets? Example of PodPresets
</summary>
<div>PodPresets:<strong>
</strong></div><div>A&nbsp;Pod Preset&nbsp;is an API resource for injecting additional runtime requirements into a Pod at creation time. You use&nbsp;label selectors&nbsp;to specify the Pods to which a given Pod Preset applies.
</div><div><strong>
</strong></div><div><strong>
</strong></div><div><strong>Specific Example:</strong></div>
<div>Specific Example:

1. Database Administrator provisions a MySQL service for their cluster.
2. Database Administrator creates secrets for the cluster containing the database name, username, and password.
3. Database Administrator creates a PodPreset defining the database &nbsp;&nbsp;port as an environment variable, as well as the secrets. See Examples below for various examples.
4. Developer of an application can now label their pod with the specified Selector the Database Administrator tells them, and consume the MySQL database without needing to know any of the details from step 2 and 3.
</div>
</details>

<details>
<summary>
Can PodPresets override POD settings.

</summary>
<div>PodPresets can�t be used to override a Pod�s own configuration, only to fill in settings which the Pod itself doesn�t specify. A Pod can opt out of being modified by PodPresets altogether, by setting the annotation:</div>podpreset.admission.kubernetes.io/exclude: "true"

</details>

<details>
<summary>
Everything about running Pod

</summary>
Labels are key-value pairs that identify resources, and can be used with selectors to match a specified group of resources.

Node affinities attract or repel Pods to or from nodes with specified attributes. For example, you can specify that a Pod can only run on a node in a specified availability zone.

While hard node affinities can block a Pod from running, soft node affinities are more like suggestions to the scheduler. You can combine multiple soft affinities with different weights.

Pod affinities express a preference for Pods to be scheduled on the same node as other Pods. For example, Pods that benefit from running on the same node can express that using a Pod affinity for each other.

Pod anti-affinities repel other Pods instead of attracting. For example, an anti-affinity to replicas of the same Pod can help spread your replicas evenly across the cluster.

Taints are a way of tagging nodes with specific information; usually, about node problems or failures. By default, Pods won�t be scheduled on tainted nodes.

Tolerations allow a Pod to be scheduled on nodes with a specific taint. You can use this mechanism to run certain Pods only on dedicated nodes.

DaemonSets allow you to schedule one copy of a Pod on every node (for example, a logging agent).

StatefulSets start and stop Pod replicas in a specific numbered sequence, allowing you to address each by a predictable DNS name. This is ideal for clustered applications, such as databases.

Jobs run a Pod once (or a specified number of times) before completing. Similarly, Cronjobs run a Pod periodically at specified times.

Horizontal Pod Autoscalers watch a set of Pods, trying to optimize a given metric (such as CPU utilization). They increase or decrease the desired number of replicas to achieve the specified goal.

PodPresets can inject bits of common configuration into all selected Pods at creation time. For example, you could use a PodPreset to mount a particular Volume on all matching Pods.

Custom Resource Definitions (CRDs) allow you to create your own custom Kubernetes objects, to store any data you wish. Operators are Kubernetes client programs that can implement orchestration behavior for your specific application (for example, MySQL).

Ingress resources route requests to different services, depending on a set of rules, for example, matching parts of the request URL. They can also terminate TLS connections for your application.

Istio is a tool that provides advanced networking features for microservice applications and can be installed, like any Kubernetes application, using Helm.

Envoy provides more sophisticated load balancing features than standard cloud load balancers, as well as a service mesh facility.

</details>

<details>
<summary>
kubectl run --attach
</summary>
wait for the Pod to start running, and then attach to the Pod as if 'kubectl attach ...' were called.&nbsp;
</details>

<details>
<summary>
kubectl run --cascade
</summary>
<div>(DEFAULT)</div><div>
</div>Cascade the deletion of the resources managed by this resource (e.g. Pods created by a ReplicationController).&nbsp;
</details>

<details>
<summary>
kubectl logs POD [-c CONTAINER]
</summary>
Print the logs of a container/resource.<div>
</div><div>--follow</div><div>
</div><div>--tail</div><div><table><tbody><tr><td>Lines of recent log file to display.</td></tr><tr></tr></tbody></table></div><div>
</div><div>--since, --since-time</div><div><table><tbody><tr><td>Only return logs newer than a relative duration like 5s, 2m, or 3h, or after a specific date. Defaults to all logs.&nbsp;
</td></tr><tr></tr></tbody></table>
</div><div>--ignore-errors</div><div>If following, allow errors that occur to be non-fatal</div><div>
</div><div>
</div><div>--prefix</div><div>Prefix logs with pod and container name</div><div>
</div><div>--timestamps</div><div>Include timestamps</div>
</details>

<details>
<summary>
kubectl port-forward POD LOCALPORT:REMOTEPORT
</summary>
<div>Forward one or more local ports to a pod.&nbsp;</div><div>
</div><div>*--address&nbsp;*</div><div><table><tbody><tr><td>Localhost or IP address(es) to listen on (comma separated). 

When localhost is supplied, kubectl will try to bind on both 127.0.0.1 and ::1 and will fail if neither of these addresses are available to bind.</td></tr><tr></tr></tbody></table></div><div>
</div><div>This command requires the node to have 'socat' installed.</div>
</details>

<details>
<summary>
containers.imagePullPolicy
</summary>
Always<div>
</div><div>Never</div><div>
</div><div>IfNotPresent</div>
</details>

<details>
<summary>
containers.args
</summary>
Arguments to the entrypoint<div>
</div><div>Default: the image's CMD line</div><div>
</div><div><font color="#ff0000">CANNOT BE UPDATED</font></div>
</details>

<details>
<summary>
containers.command
</summary>
Entrypoint array<div>
</div><div>Not executed in a shell</div><div>
</div><div>Default: the image's ENTRYPOINT</div><div>
</div><div><font color="#ff0000">CANNOT BE UPDATED</font></div>
</details>

<details>
<summary>
containers.env
</summary>
List of env vars<div>
</div><div><font color="#ff0000">CANNOT BE UPDATED</font></div>
</details>

<details>
<summary>
containers.envFrom
</summary>
List of sources to populate env vars<div>
</div><div><font color="#ff0000">CANNOT BE UPDATED</font>
</div>
</details>

<details>
<summary>
containers.image
</summary>
container image url
</details>

<details>
<summary>
containers.lifecycle
</summary>
Actions to take in response to lifecycle events<div>*
*</div><div>*postStart*</div><div>Called after a container is created</div><div>
</div><div>If it fails, container restarts according to restart policy</div><div>*
*</div><div>*preStop*
<div>Called before a container is terminated (not if it crashes/exits)</div><div>
</div><div><font color="#ff0000">CANNOT BE UPDATED</font>
</div></div>
</details>

<details>
<summary>
containers.livenessProbe
</summary>
Periodic probe of container liveness<div>
</div><div>Restart container if fails</div><div>
</div><div><div>httpGet: request to perform</div><div>
</div><div>failureThreshold: failures needed to mark probe failed</div><div>
</div><div>successThreshold: successes needed to mark probe succeeded</div><div>
</div><div>initialDelaySeconds: seconds before liveness probes begin</div><div>
</div><div>periodSeconds: how often to probe</div></div>
</details>

<details>
<summary>
containers.name
</summary>
Unique name of the container
</details>

<details>
<summary>
containers.ports
</summary>
Ports to expose from container<div>
</div><div>name:</div><div>
</div><div>protocol:</div><div>
</div><div>containerPort: #</div><div>
</div><div>hostIP:</div><div>
</div>
</details>

<details>
<summary>
containers.readinessProbe
</summary>
Periodic probe of container service readiness.<div>
</div><div>Container removed from svc endpoints if probe fails</div><div>
</div><div>httpGet: request to perform</div><div>
</div><div>failureThreshold: failures needed to mark probe failed</div><div>
</div><div>successThreshold: successes needed to mark probe succeeded</div><div>
</div><div>initialDelaySeconds: seconds before liveness probes begin</div><div>
</div><div>periodSeconds: how often to probe</div>
</details>

<details>
<summary>
containers.securityContext
</summary>
Container's security config<div>
</div><div>runAsUser:</div><div>
</div><div><div><div>runAsGroup:&nbsp;</div></div></div><div>
</div><div>runAsNonRoot:</div><div>
</div><div>capabilities:
</div><div>
</div><div><div>privileged:</div></div><div>
</div><div><div>procMount:</div></div><div>
</div><div><div>seLinuxOptions:</div><div>
</div></div><div>readOnlyRootFilesystem:</div><div>
</div><div>allowPrivilegeEscalation:</div>
</details>

<details>
<summary>
containers.VolumeMount
</summary>
Mounting of a Volume in a container<div>
</div><div>name: same as volume</div><div>
</div><div>readOnly:</div><div>
</div><div>mountPath: Path in the container where the volume is mounted
</div><div>
</div><div>subPath: Path in the volume to mount in the container</div><div>
</div><div>subPathExpr:</div>
</details>

<details>
<summary>
containers.workingDir
</summary>
Container's working directory
</details>

<details>
<summary>
In terms of Docker constructs, a Pod is modelled as a group of Docker containers with shared _____ and shared _____
</summary>
namespaces<div>
</div><div>filesystem volumes</div>
</details>

<details>
<summary>
Are pods intended to survive through scheduling failures, node failures or other evictions?
</summary>
No
</details>

<details>
<summary>
How can any container in a pod enable privileged mode?
</summary>
Using the *privileged*&nbsp;flag in the security context of the container spec.
</details>

<details>
<summary>
Why would you allow a container to run in privileged mode?
</summary>
to allow Linux capabilities inside the container.<div>
</div><div>-----</div><div>
</div><div>Ex.:</div><div>
</div><div>accessing devices</div><div>network stack manipulation</div><div>writing network and volume plugins</div>
</details>

<details>
<summary>
A pod represents _____ processes running in your cluster.
</summary>
Processes
</details>

<details>
<summary>
A pod is the basic _____ unit of Kubernetes.
</summary>
execution
</details>

<details>
<summary>
A pod encapsulates an application's...
</summary>
container
<hr>
execution options
<hr>
IP address
<hr>
storage resources
</details>

<details>
<summary>
Do pods run a single container each?
</summary>
Not necessarily. They can run multiple containers.
</details>

<details>
<summary>
Are containers in a Pod automatically co-located and co-scheduled on the *same* physical or virtual machine in the cluster?
</summary>
Yes
</details>

<details>
<summary>
Can a pod's containers share resources, dependensies, communicate with each other and coordinate their lifecycle?
</summary>
Yes
</details>

<details>
<summary>
Example sidecar container pattern diagram
</summary>
<img src="pod.svg">
</details>

<details>
<summary>
Do Pods each have a unique IP address?
</summary>
Yes, for each address family
</details>

<details>
<summary>
kubectl top pod
</summary>
<div>Display Resource usage of pods.</div><div>
</div><div>--containers</div><div>Print usage of containers in a pod</div>
</details>

<details>
<summary>
Create a busybox pod (using kubectl command) that runs the command "env"
</summary>
<i>kubectl run busybox
 --image=busybox 
--restart=Never 
--command -- env</i>

arguments after -- are commands
</details>

<details>
<summary>
If a pod crashed and restarted, get logs about a previous instance
</summary>
<i>kubectl logs mypod --previous</i>
</details>

<details>
<summary>
Open a shell session in a pod
</summary>
<i>kubectl exec -it mypod -- /bin/sh</i>
</details>

<details>
<summary>
show all labels of pods
</summary>
<i>kubectl get pods --show-labels</i>
</details>

<details>
<summary>
Get a pod's YAML without cluster-specific information
</summary>
use the <i>--export </i>flag<div><i>kubectl get pod mypod -o yaml --export</i></div>
</details>

<details>
<summary>
Get only the pods with label app=v2
</summary>
<i>kubectl get pods -l app=v2</i>
or
<i>kubectl get pods -l 'app in (v2)'</i>
</details>

<details>
<summary>
kubectl attach POD -c container
</summary>
Attach to a container's process.<div>
</div><div>*--stdin*</div><div>Pass stdin to the container</div><div>
</div><div>*--tty*</div><div>stdin is a tty</div>
</details>

<details>
<summary>
Load a configmap "cmvolume" as a volume inside a pod, on the path "/etc/lala". Check this exists on the created pod.
</summary>
Edit pod YAML, need to add <i>spec.volumes </i>and <i>containers.volumeMounts </i>like for loading any volume.

<i>spec:</i><div><i>&nbsp; volumes:</i></div><div><i>&nbsp; - name: myvol
&nbsp; &nbsp; configMap:</i></div><div><i>&nbsp; &nbsp; &nbsp; name: cmvolume # name of configmap</i></div><div><i>&nbsp; containers:</i></div><div><i>&nbsp; - volumeMounts:</i></div><div><i>&nbsp; &nbsp; - name: myvol # matches name above</i></div><div><i>&nbsp; &nbsp; &nbsp; mountPath: /etc/lala</i></div><div><i>
</i></div><div>to check path exists on the pod:
<i>kubectl exec -it mypod -- ls /etc/lala</i>
or spawn an interactive session
<i>kubectl exec -it mypod -- /bin/sh</i></div><div><i>cd /etc/lala</i></div><div><i>ls</i></div><div><i>cat var8</i></div>
</details>

<details>
<summary>
Create an nginx pod with a liveness probe that just runs the command 'ls'. Run it, check its probe status, delete it.
</summary>
Add the <i>spec.containers.livenessProbe</i>&nbsp;section to the standard pod YAML:<div>
</div><div><i>spec:</i></div><div><i>&nbsp; containers</i></div><div><i>&nbsp; - image: nginx</i></div><div><i>&nbsp; &nbsp; livenessProbe:</i></div><div><i>&nbsp; &nbsp; &nbsp; exec:</i></div><div><i>&nbsp; &nbsp; &nbsp; &nbsp; command:</i></div><div><i>&nbsp; &nbsp; &nbsp; &nbsp; - ls</i></div>
</details>

<details>
<summary>
Modify the liveness probe in the nginx pod so it starts after 5 seconds and probes every 10 seconds.
</summary>
Add the <i>spec.containers.livenessProbe.initialDelaySeconds and .periodSeconds </i>values:

<i>spec:</i><div><i>&nbsp; containers:</i></div><div><i>&nbsp; - image: nginx</i></div><div><i>&nbsp; &nbsp; livenessProbe:</i></div><div><i>&nbsp; &nbsp; &nbsp; initialDelaySeconds: 5</i></div><div><i>&nbsp; &nbsp; &nbsp; periodSeconds: 10</i></div>
</details>

<details>
<summary>
Create a busybox pod that runs 'ls /notexist'. Determine if there's an error (of course there is), see it.&nbsp;
</summary>
<i>kubectl run mypod --image=busybox --restart=Never -- /bin/sh -c 'ls /notexist'</i>

show that there are errors
<i>kubectl logs mypod</i><div><i>kubectl describe pod mypod</i></div>
</details>

<details>
<summary>
Create a pod that mounts the variable "username" from secret "mysecret2" into an env variable called USER
</summary>
edit pod YAML to add the <i>spec.containers.env.valueFrom.secretKeyRef </i>section:

<i>spec:</i><div><i>&nbsp; containers:</i></div><div><i>&nbsp; - image: nginx</i></div><div><i>&nbsp; &nbsp; env:</i></div><div><i>&nbsp; &nbsp; - name: USER</i></div><div><i>&nbsp; &nbsp; &nbsp; valueFrom:</i></div><div><i>&nbsp; &nbsp; &nbsp; &nbsp; secretKeyRef:</i></div><div><i>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; name: mysecret2</i></div><div><i>&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; key: username</i></div>
</details>

<details>
<summary>
Create an nginx pod that uses 'myuser' as a service account
</summary>
<i>kubectl run mypod --image=nginx --restart=Never --serviceaccount=myuser</i>

Or edit pod YAML to add the <i>spec.serviceAccountName </i>value

<i>spec:</i><div><i>&nbsp; serviceAccountName: myuser</i></div>
</details>

<details>
<summary>
Create an nginx pod (that includes port 80) with an HTTP readinessProbe on path '/' on port 80.
</summary>
Create the pod including the <i>--port </i>flag<div><i>kubectl run mypod --image=nginx --restart=Never --port=80 --dry-run -o yaml &gt; pod.yaml</i>

Add the <i>readinessProbe.httpGet </i>section:
<i>spec:</i></div><div><i>&nbsp; containers:</i></div><div><i>&nbsp; - image: nginx</i></div><div><i>&nbsp; &nbsp; readinessProbe:</i></div><div><i>&nbsp; &nbsp; &nbsp; httpGet:</i></div><div><i>&nbsp; &nbsp; &nbsp; &nbsp; path: /</i></div><div><i>&nbsp; &nbsp; &nbsp; &nbsp; port: 80</i></div>
</details>

<details>
<summary>
Delete the pod "mypod" forcefully with no grace period
</summary>
<i>kubectl delete pod mypod --force --grace-period=0</i>
</details>

<details>
<summary>
The *phase *of a pod is...
</summary>
A high-level summary of where the pod is in its lifecycle
</details>

<details>
<summary>
*Pending* phase
</summary>
<table><tbody><tr><td>The Pod has been accepted by the Kubernetes system, but one or more of the Container images has not been created. This includes time before being scheduled as well as time spent downloading images over the network, which could take a while.</td></tr><tr></tr></tbody></table>
</details>

<details>
<summary>
*Running*&nbsp;phase
</summary>
<table><tbody><tr><td>The Pod has been bound to a node, and all of the Containers have been created. At least one Container is still running, or is in the process of starting or restarting.</td></tr><tr></tr></tbody></table>
</details>

<details>
<summary>
*Succeeded *phase
</summary>
<table><tbody><tr><td>All Containers in the Pod have terminated in success, and will not be restarted.</td></tr><tr></tr></tbody></table>
</details>

<details>
<summary>
*Failed *phase
</summary>
<table><tbody><tr><td>All Containers in the Pod have terminated, and at least one Container has terminated in failure. That is, the Container either exited with non-zero status or was terminated by the system.</td></tr><tr></tr></tbody></table>
</details>

<details>
<summary>
*Unknown *phase
</summary>
For some reason the state of the Pod could not be obtained, typically due to an error in communicating with the host of the Pod.
</details>

<details>
<summary>
List all 5 Pod *phases*
</summary>
Pending<div>
</div><div>Running</div><div>
</div><div>Succeeded</div><div>
</div><div>Failed</div><div>
</div><div>Unknown</div>
</details>

<details>
<summary>
List all six fields in a *PodCondition*
</summary>
reason<div>
</div><div>status</div><div>
</div><div>message</div><div>
</div><div>type</div><div>
</div><div>lastProbeTime</div><div>
</div><div>lastTransitionTime</div>
</details>

<details>
<summary>
The *lastProbeTime*&nbsp;condition field provides...
</summary>
A timestamp for when the Pod condition was last probed.
</details>

<details>
<summary>
The&nbsp;*lastTransitionTime*&nbsp;condition field provides...
</summary>
a timestamp for when the Pod last transitioned from one status to another.
</details>

<details>
<summary>
The *message&nbsp;*condition field provides...
</summary>
a human-readable message indicating details about the transition from one status to another.
</details>

<details>
<summary>
The *reason&nbsp;*condition field provides...
</summary>
a unique, one-word reason for the condition's last transition.
</details>

<details>
<summary>
The *status*&nbsp;condition field provides...
</summary>
<div>One of the following:</div><div>*
*</div><div>*"True"*</div><div>*
*</div><div>*"False"*</div><div>*
*</div><div>"*Unknown"*</div>
</details>

<details>
<summary>
The *type*&nbsp;condition field provides...
</summary>
One of the following:<div>
</div><div>*PodScheduled*</div><div>Pod has been scheduled to a node</div><div>*
*</div><div>*Ready*</div><div>Pod is able to serve requests</div><div>*
*</div><div>*Initialized*</div><div>All init containers have started successfully</div><div>*
*</div><div>*ContainersReady*</div><div>All containers in the pod are ready</div>
</details>

<details>
<summary>
<div>A probe can have one of three results:</div>
</summary>
*Success*<div>The Container passed the diagnostic
<div>*
*</div><div>*Failure*</div><div>The Container failed the diagnostic</div><div>*
*</div><div>*Unknown*</div></div><div>The diagnostic failed, so no action should be taken</div>
</details>

<details>
<summary>
A livenessProbe indicates whether a container is...
</summary>
running
</details>

<details>
<summary>
If a livenessProbe fails, the container...
</summary>
is killed by the kubelet, then subjected to the container's *restart policy*.
</details>

<details>
<summary>
A container does not provide a livenessProbe, a readinessProbe nor a startupProbe<div>
</div><div>What will be the state of each probe of the container?</div>
</summary>
*Success *on all of them
</details>

<details>
<summary>
readinessProbe indicates whether...
</summary>
a container is ready to service requests.
</details>

<details>
<summary>
If the readinessProbe fails, what happens?
</summary>
The *endpoints controlller* removes the *Pod's IP address* from the endpoints of all *Services *that match the Pod
</details>

<details>
<summary>
A container's default readiness state before the initial delay is...
</summary>
Failure
</details>

<details>
<summary>
startupProbe indicates whether...
</summary>
the application in the container has started.
</details>

<details>
<summary>
A startupProbe is provided to a container<div>
</div><div>What happens to the other probes?</div>
</summary>
All other probes are disabled until startupProbe succeeds.
</details>

<details>
<summary>
If the startupProbe fails, the container...
</summary>
is killed by the kubelet, then subjected to the container's *restart policy*.
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">A process in your Container is able to crash on its own whenever it encounters an issue or becomes unhealthy.&nbsp;</span><div><span style="color: rgb(34, 34, 34);">
</span></div><div><span style="color: rgb(34, 34, 34);">Do you still need a livenessProbe?</span></div>
</summary>
Not necessarily.&nbsp;<div>
</div><div><span style="color: rgb(34, 34, 34);">The kubelet will automatically perform the correct action in accordance with the Pod's&nbsp;</span><code>restartPolicy</code><span style="color: rgb(34, 34, 34);">.</span>
</div>
</details>

<details>
<summary>
A container should be killed or restarted if a probe fails. What can be done to achieve this?
</summary>
1. Specify a *livenessProbe&nbsp;*<div>
</div><div>2. Add a *restartPolicy *of *Always *or *OnFailure*</div>
</details>

<details>
<summary>
A Pod should only be sent traffic when a probe succeeds. What can achieve this?
</summary>
readinessProbe
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">A Container should be able to take itself down for maintenance. What can achieve this?</span>
</summary>
A *readinessProbe *that checks an endpoints specific to readiness that is different from the liveness probe.
</details>

<details>
<summary>
The three possible states of containers are...
</summary>
Waiting<div>
</div><div>Running</div><div>
</div><div>Terminated</div>
</details>

<details>
<summary>
A container is *Waiting *when...
</summary>
It is neither *Running *or *Terminated*<div>
</div><div>A *Waiting *container still runs operations like pulling images, applying Secrets etc.</div>
</details>

<details>
<summary>
How to tell why a container is in&nbsp;*Waiting *state?
</summary>
Check the state's&nbsp;*Reason *field
</details>

<details>
<summary>
A container is in the *Running *state when...
</summary>
It is executing without issues.
</details>

<details>
<summary>
Which hook is executed prior to a container entering its *Running *state?
</summary>
postStart
</details>

<details>
<summary>
A container is in the *Terminated *state when...
</summary>
It has successfully or unsuccessfully completed execution.
</details>

<details>
<summary>
How to tell why a container is in *Terminated *state?
</summary>
Check the state's *Reason *and *Exit Code* fields.
</details>

<details>
<summary>
Which hook is executed before a container enters Terminated state?
</summary>
preStop
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">Inject extra feedback or signals into PodStatus via...</span>
</summary>
*Pod readiness*
</details>

<details>
<summary>
You can add *Pod readiness* into *PodStatus *by...
</summary>
*readinessGates*<div>
</div><div>Add it into PodSpec to specify a list of extra conditions for the kubelet to evaluate</div><div>
</div><div>Ex.:</div><div>
</div><div><pre><code><span style="color: rgb(170, 34, 255); font-weight: 700;">kind</span>:<span style="color: rgb(187, 187, 187);"> </span>Pod<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);"></span>...<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);"></span><span style="color: rgb(170, 34, 255); font-weight: 700;">spec</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">readinessGates</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span>- <span style="color: rgb(170, 34, 255); font-weight: 700;">conditionType</span>:<span style="color: rgb(187, 187, 187);"> </span><span style="color: rgb(187, 68, 68);">"www.example.com/feature-1"</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);"></span><span style="color: rgb(170, 34, 255); font-weight: 700;">status</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">conditions</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span>- <span style="color: rgb(170, 34, 255); font-weight: 700;">type</span>:<span style="color: rgb(187, 187, 187);"> </span>Ready<span style="color: rgb(187, 187, 187);">                              </span><span style="color: rgb(0, 136, 0); font-style: italic;"># a built in PodCondition</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">      </span><span style="color: rgb(170, 34, 255); font-weight: 700;">status</span>:<span style="color: rgb(187, 187, 187);"> </span><span style="color: rgb(187, 68, 68);">"False"</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">      </span><span style="color: rgb(170, 34, 255); font-weight: 700;">lastProbeTime</span>:<span style="color: rgb(187, 187, 187);"> </span><span style="color: rgb(170, 34, 255); font-weight: 700;">null</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">      </span><span style="color: rgb(170, 34, 255); font-weight: 700;">lastTransitionTime</span>:<span style="color: rgb(187, 187, 187);"> </span>2018-01-01T00:00:00Z<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span>- <span style="color: rgb(170, 34, 255); font-weight: 700;">type</span>:<span style="color: rgb(187, 187, 187);"> </span><span style="color: rgb(187, 68, 68);">"www.example.com/feature-1"</span><span style="color: rgb(187, 187, 187);">        </span><span style="color: rgb(0, 136, 0); font-style: italic;"># an extra PodCondition</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">      </span><span style="color: rgb(170, 34, 255); font-weight: 700;">status</span>:<span style="color: rgb(187, 187, 187);"> </span><span style="color: rgb(187, 68, 68);">"False"</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">      </span><span style="color: rgb(170, 34, 255); font-weight: 700;">lastProbeTime</span>:<span style="color: rgb(187, 187, 187);"> </span><span style="color: rgb(170, 34, 255); font-weight: 700;">null</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">      </span><span style="color: rgb(170, 34, 255); font-weight: 700;">lastTransitionTime</span>:<span style="color: rgb(187, 187, 187);"> </span>2018-01-01T00:00:00Z<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">containerStatuses</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span>- <span style="color: rgb(170, 34, 255); font-weight: 700;">containerID</span>:<span style="color: rgb(187, 187, 187);"> </span>docker://abcd...<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">      </span><span style="color: rgb(170, 34, 255); font-weight: 700;">ready</span>:<span style="color: rgb(187, 187, 187);"> </span><span style="color: rgb(170, 34, 255); font-weight: 700;">true</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);"></span>...</code></pre></div>
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">Readiness gates are determined by the current state of...</span>
</summary>
*status.condition* fields for the Pod<div>
</div><div>If such a field isn't found, the status of the condition defaults to *"False"*</div>
</details>

<details>
<summary>
*restartPolicy *possible values are...
</summary>
Always<div>
</div><div>Never</div><div>
</div><div>OnFailure</div>
</details>

<details>
<summary>
Default restartPolicy is...
</summary>
Always
</details>

<details>
<summary>
Does a Pod's *restartPolicy *apply to all its containers?
</summary>
Yes
</details>

<details>
<summary>
Once bound to a node, will a Pod ever rebound to another node?
</summary>
No
</details>

<details>
<summary>
Does<code> restartPolicy</code><span style="color: rgb(34, 34, 34);">&nbsp;only refer to restarts of the Containers by the kubelet on the same node?</span>
</summary>
Yes
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">Exited Containers that are restarted by the kubelet are restarted with an _____ delay capped at _____ and is reset after ten minutes of successful execution.</span>
</summary>
exponential back-off<div>
</div><div>5 minutes</div>
</details>

<details>
<summary>
Example livenessProbe spec
</summary>
<pre><code><span style="color: rgb(170, 34, 255); font-weight: 700;">apiVersion</span>:<span style="color: rgb(187, 187, 187);"> </span>v1<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);"></span><span style="color: rgb(170, 34, 255); font-weight: 700;">kind</span>:<span style="color: rgb(187, 187, 187);"> </span>Pod<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);"></span><span style="color: rgb(170, 34, 255); font-weight: 700;">metadata</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">labels</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">test</span>:<span style="color: rgb(187, 187, 187);"> </span>liveness<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">name</span>:<span style="color: rgb(187, 187, 187);"> </span>liveness-http<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);"></span><span style="color: rgb(170, 34, 255); font-weight: 700;">spec</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">containers</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span>- <span style="color: rgb(170, 34, 255); font-weight: 700;">args</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span>- /server<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">image</span>:<span style="color: rgb(187, 187, 187);"> </span>k8s.gcr.io/liveness<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">livenessProbe</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">      </span><span style="color: rgb(170, 34, 255); font-weight: 700;">httpGet</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">        </span><span style="color: rgb(0, 136, 0); font-style: italic;"># when "host" is not defined, "PodIP" will be used</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">        </span><span style="color: rgb(0, 136, 0); font-style: italic;"># host: my-host</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">        </span><span style="color: rgb(0, 136, 0); font-style: italic;"># when "scheme" is not defined, "HTTP" scheme will be used. Only "HTTP" and "HTTPS" are allowed</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">        </span><span style="color: rgb(0, 136, 0); font-style: italic;"># scheme: HTTPS</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">        </span><span style="color: rgb(170, 34, 255); font-weight: 700;">path</span>:<span style="color: rgb(187, 187, 187);"> </span>/healthz<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">        </span><span style="color: rgb(170, 34, 255); font-weight: 700;">port</span>:<span style="color: rgb(187, 187, 187);"> </span><span style="color: rgb(102, 102, 102);">8080</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">        </span><span style="color: rgb(170, 34, 255); font-weight: 700;">httpHeaders</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">        </span>- <span style="color: rgb(170, 34, 255); font-weight: 700;">name</span>:<span style="color: rgb(187, 187, 187);"> </span>X-Custom-Header<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">          </span><span style="color: rgb(170, 34, 255); font-weight: 700;">value</span>:<span style="color: rgb(187, 187, 187);"> </span>Awesome<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">      </span><span style="color: rgb(170, 34, 255); font-weight: 700;">initialDelaySeconds</span>:<span style="color: rgb(187, 187, 187);"> </span><span style="color: rgb(102, 102, 102);">15</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">      </span><span style="color: rgb(170, 34, 255); font-weight: 700;">timeoutSeconds</span>:<span style="color: rgb(187, 187, 187);"> </span><span style="color: rgb(102, 102, 102);">1</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">name</span>:<span style="color: rgb(187, 187, 187);"> </span>liveness</code></pre>
</details>

<details>
<summary>
A pod has one container. The container exits with *success*.<div>
</div><div>What happens depending on each possible *restartPolicy*?</div>
</summary>
<ul><li>Always: Restart Container; Pod&nbsp;<code>phase</code>&nbsp;stays Running.</li><li>OnFailure: Pod&nbsp;<code>phase</code>&nbsp;becomes Succeeded.</li><li>Never: Pod&nbsp;<code>phase</code>&nbsp;becomes Succeeded.</li></ul>
</details>

<details>
<summary>
A pod has one container. The container exits with&nbsp;*failure*.<div>
</div><div>What happens depending on each possible&nbsp;*restartPolicy*?</div>
</summary>
<ul><li>Always: Restart Container; Pod&nbsp;<code>phase</code>&nbsp;stays Running.</li><li>OnFailure: Restart Container; Pod&nbsp;<code>phase</code>&nbsp;stays Running.</li><li>Never: Pod&nbsp;<code>phase</code>&nbsp;becomes Failed.</li></ul>
</details>

<details>
<summary>
<div><div>Pod is running and has two Containers. Container 1 exits with *failure*.</div></div><div>
</div><div>What happens depending on each possible&nbsp;*restartPolicy*?</div>
</summary>
<ul><li>Always: Restart Container; Pod&nbsp;<code>phase</code>&nbsp;stays Running.</li><li>OnFailure: Restart Container; Pod&nbsp;<code>phase</code>&nbsp;stays Running.</li><li>Never: Do not restart Container; Pod&nbsp;<code>phase</code>&nbsp;stays Running.</li></ul><div><span style="color: rgb(34, 34, 34);">If Container 1 is not running, and Container 2 exits:</span></div><div><ul><li>Always: Restart Container; Pod&nbsp;<code>phase</code>&nbsp;stays Running.</li><li>OnFailure: Restart Container; Pod&nbsp;<code>phase</code>&nbsp;stays Running.</li><li>Never: Pod&nbsp;<code>phase</code>&nbsp;becomes Failed.</li></ul></div>
</details>

<details>
<summary>
<div><div><div>Pod is running and has one Container. Container runs *out of memory *(and terminates in failure)</div></div></div><div>
</div><div>What happens depending on each possible&nbsp;*restartPolicy*?</div>
</summary>
<ul><li>Always: Restart Container; Pod&nbsp;<code>phase</code>&nbsp;stays Running.</li><li>OnFailure: Restart Container; Pod&nbsp;<code>phase</code>&nbsp;stays Running.</li><li>Never: Log failure event; Pod&nbsp;<code>phase</code>&nbsp;becomes Failed.</li></ul>
</details>

<details>
<summary>
<div><div><div><div>Pod is running, and a disk dies.</div></div></div></div><div>
</div><div>What happens?</div>
</summary>
<ul><li>Kill all Containers.</li><li>Log appropriate event.</li><li>Pod&nbsp;<code>phase</code>&nbsp;becomes Failed.</li><li>If running under a controller, Pod is recreated elsewhere.</li></ul>
</details>

<details>
<summary>
<div><div><div><div><div>Pod is running, and its node is segmented out.</div></div></div></div></div><div>
</div><div>What happens?</div>
</summary>
<ul><li>Node controller waits for timeout.</li><li>Node controller sets Pod&nbsp;<code>phase</code>&nbsp;to Failed.</li><li>If running under a controller, Pod is recreated elsewhere.</li></ul>
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">A PodPreset allows pod template authors to not have to explicitly provide all information for every pod.&nbsp;</span><span style="color: rgb(34, 34, 34);">PodPresets are objects for injecting&nbsp;</span><span style="color: rgb(34, 34, 34);">additional runtime requirements&nbsp;</span><span style="color: rgb(34, 34, 34);">into pods at _____</span>
</summary>
creation time
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">You use _____</span><span style="color: rgb(34, 34, 34);">&nbsp;to specify the Pods to which a given PodPreset applies.</span>
</summary>
label selectors
</details>

<details>
<summary>
What applies Pod Presets to incoming pod creation requests?
</summary>
PodPreset admission controller
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">When a pod creation request occurs, the system does the following:</span>
</summary>
<ol><li>Retrieve all&nbsp;<code>PodPresets</code>&nbsp;available for use.</li><li>Check if the label selectors of any&nbsp;<code>PodPreset</code>&nbsp;matches the labels on the pod being created.</li><li>Attempt to merge the various resources defined by the&nbsp;<code>PodPreset</code>&nbsp;into the Pod being created.</li><li>On error, throw an event documenting the merge error on the pod, and create the pod&nbsp;<em>without</em>&nbsp;any injected resources from the&nbsp;<code>PodPreset</code>.</li><li>Annotate the resulting modified Pod spec to indicate that it has been modified by a&nbsp;<code>PodPreset</code>. The annotation is of the form&nbsp;<code>podpreset.admission.kubernetes.io/podpreset-&lt;pod-preset name&gt;: "&lt;resource version&gt;"</code>.</li></ol>
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">&nbsp;When a&nbsp;</span><code>PodPreset</code><span style="color: rgb(34, 34, 34);">&nbsp;is applied to one or more Pods, Kubernetes modifies the _____</span>
</summary>
<span style="color: rgb(34, 34, 34);">PodSpec</span>
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">For changes to&nbsp;</span><code>Env</code><span style="color: rgb(34, 34, 34);">,&nbsp;</span><code>EnvFrom</code><span style="color: rgb(34, 34, 34);">, and&nbsp;</span><code>VolumeMounts</code><span style="color: rgb(34, 34, 34);">, Kubernetes modifies _____</span>
</summary>
<span style="color: rgb(34, 34, 34);">The pod's individual container specs</span>
</details>

<details>
<summary>
How to&nbsp;disable Pod Preset for a Specific Pod
</summary>
<span style="color: rgb(34, 34, 34); background-color: rgba(0, 0, 0, 0.05);">podpreset.admission.kubernetes.io/exclude: "true"</span><div><span style="color: rgb(34, 34, 34); background-color: rgba(0, 0, 0, 0.05);">
</span></div><div>Add the above annotation in the Pod Spec<span style="color: rgb(34, 34, 34); background-color: rgba(0, 0, 0, 0.05);">
</span></div>
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">You can use&nbsp;</span><em>topology spread constraints</em><span style="color: rgb(34, 34, 34);">&nbsp;to control...</span>
</summary>
<span style="color: rgb(34, 34, 34);">how Pods</span><span style="color: rgb(34, 34, 34);">&nbsp;are spread across your cluster among failure-domains.&nbsp;</span><div><span style="color: rgb(34, 34, 34);">(regions, zones, nodes or user-defined domains)</span></div>
</details>

<details>
<summary>
<div><div>You have a 4-node cluster with the following labels:</div><pre><code>NAME    STATUS   ROLES    AGE     VERSION   LABELS
node1   Ready    &lt;none&gt;   4m26s   v1.16.0   node=node1,zone=zoneA
node2   Ready    &lt;none&gt;   3m58s   v1.16.0   node=node2,zone=zoneA
node3   Ready    &lt;none&gt;   3m17s   v1.16.0   node=node3,zone=zoneB
node4   Ready    &lt;none&gt;   2m43s   v1.16.0   node=node4,zone=zoneB</code></pre></div><div>What would the cluster logically look like?</div>
</summary>
<pre><code>+---------------+---------------+
|     zoneA     |     zoneB     |
+-------+-------+-------+-------+
| node1 | node2 | node3 | node4 |
+-------+-------+-------+-------+</code></pre>
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">Topology spread constraints rely on _____ to&nbsp;</span><span style="color: rgb(34, 34, 34);">identify the topology domain(s) that each Node is in.</span>
</summary>
node labels
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">You can define one or multiple _____&nbsp;</span>to instruct the kube-scheduler how to place each incoming Pod in relation to the existing Pods across your cluster.
</summary>
<code>topologySpreadConstraint</code>
</details>

<details>
<summary>
*topologySpreadConstraints *syntax
</summary>
<pre><code>apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  topologySpreadConstraints:
    - maxSkew: &lt;integer&gt;
      topologyKey: &lt;string&gt;
      whenUnsatisfiable: &lt;string&gt;
      labelSelector: &lt;object&gt;</code></pre>
</details>

<details>
<summary>
<strong>maxSkew</strong><span style="color: rgb(34, 34, 34);">&nbsp;describes...</span>
</summary>
the degree to which Pods may be unevenly distributed.<div>
</div><div><span style="color: rgb(34, 34, 34);">It's the maximum permitted difference between the number of matching Pods in any two topology domains of a given topology type.</span>
</div><div><span style="color: rgb(34, 34, 34);">
</span></div><div><span style="color: rgb(34, 34, 34);">It must be greater than zero.</span><span style="color: rgb(34, 34, 34);">
</span></div>
</details>

<details>
<summary>
<strong>topologyKey</strong><span style="color: rgb(34, 34, 34);">&nbsp;is...</span>
</summary>
<span style="color: rgb(34, 34, 34);">The key of node labels.&nbsp;</span>
</details>

<details>
<summary>
<div><div><span style="color: rgb(34, 34, 34);">If two Nodes are labelled with one *topologyKey *and have identical values for that label, the scheduler treats both Nodes as _____</span></div></div><div>
</div><div>
</div>
</summary>
<span style="color: rgb(34, 34, 34);">Being in the same topology.&nbsp;</span><div><span style="color: rgb(34, 34, 34);">
</span></div><div><span style="color: rgb(34, 34, 34);">The scheduler tries to place a balanced number of Pods into each topology domain</span><span style="color: rgb(34, 34, 34);">
</span></div>
</details>

<details>
<summary>
<strong>whenUnsatisfiable</strong><span style="color: rgb(34, 34, 34);">&nbsp;indicates...</span>
</summary>
<span style="color: rgb(34, 34, 34);">How to deal with a Pod if it doesn't satisfy the spread constraint</span>
</details>

<details>
<summary>
*whenUnsatisfiable *possible values:
</summary>
<div>*<code>DoNotSchedule</code><span style="color: rgb(34, 34, 34);">&nbsp;</span><span style="color: rgb(34, 34, 34);">
</span>*</div><div><span style="color: rgb(34, 34, 34);">tells the scheduler not to schedule it.</span><span style="color: rgb(34, 34, 34);">
</span></div><div><span style="color: rgb(34, 34, 34);">
</span></div><div>*<code>ScheduleAnyway</code><span style="color: rgb(34, 34, 34);">&nbsp;</span>*<span style="color: rgb(34, 34, 34);">
</span></div><div><span style="color: rgb(34, 34, 34);">tells the scheduler to still schedule it while prioritizing nodes that minimize the skew</span></div>
</details>

<details>
<summary>
<div><h3>Example: One TopologySpreadConstraint</h3></div><div>Suppose you have a 4-node cluster where 3 Pods labeled&nbsp;<code>foo:bar</code>&nbsp;are located in node1, node2 and node3 respectively (<code>P</code>&nbsp;represents Pod):</div><pre><code>+---------------+---------------+
|     zoneA     |     zoneB     |
+-------+-------+-------+-------+
| node1 | node2 | node3 | node4 |
+-------+-------+-------+-------+
|   P   |   P   |   P   |       |
+-------+-------+-------+-------+
</code></pre><div>If we want an incoming Pod to be evenly spread with existing Pods across zones, the spec can be given as:</div>
</summary>
<pre><code><span style="color: rgb(170, 34, 255); font-weight: 700;">kind</span>:<span style="color: rgb(187, 187, 187);"> </span>Pod<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);"></span><span style="color: rgb(170, 34, 255); font-weight: 700;">apiVersion</span>:<span style="color: rgb(187, 187, 187);"> </span>v1<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);"></span><span style="color: rgb(170, 34, 255); font-weight: 700;">metadata</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">name</span>:<span style="color: rgb(187, 187, 187);"> </span>mypod<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">labels</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">foo</span>:<span style="color: rgb(187, 187, 187);"> </span>bar<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);"></span><span style="color: rgb(170, 34, 255); font-weight: 700;">spec</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">topologySpreadConstraints</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span>- <span style="color: rgb(170, 34, 255); font-weight: 700;">maxSkew</span>:<span style="color: rgb(187, 187, 187);"> </span><span style="color: rgb(102, 102, 102);">1</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">topologyKey</span>:<span style="color: rgb(187, 187, 187);"> </span>zone<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">whenUnsatisfiable</span>:<span style="color: rgb(187, 187, 187);"> </span>DoNotSchedule<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">labelSelector</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">      </span><span style="color: rgb(170, 34, 255); font-weight: 700;">matchLabels</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">        </span><span style="color: rgb(170, 34, 255); font-weight: 700;">foo</span>:<span style="color: rgb(187, 187, 187);"> </span>bar<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">containers</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span>- <span style="color: rgb(170, 34, 255); font-weight: 700;">name</span>:<span style="color: rgb(187, 187, 187);"> </span>pause<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">image</span>:<span style="color: rgb(187, 187, 187);"> </span>k8s.gcr.io/pause:<span style="color: rgb(102, 102, 102);">3.1</span></code></pre><pre><code><div><code>topologyKey: zone</code>&nbsp;implies the even distribution will only be applied to the nodes which have label pair "zone:&lt;any value&gt;" present.&nbsp;<code>whenUnsatisfiable: DoNotSchedule</code>&nbsp;tells the scheduler to let it stay pending if the incoming Pod can�t satisfy the constraint.</div><div>If the scheduler placed this incoming Pod into "zoneA", the Pods distribution would become [3, 1], hence the actual skew is 2 (3 - 1) - which violates&nbsp;<code>maxSkew: 1</code>. In this example, the incoming Pod can only be placed onto "zoneB":</div><pre><code>+---------------+---------------+      +---------------+---------------+
|     zoneA     |     zoneB     |      |     zoneA     |     zoneB     |
+-------+-------+-------+-------+      +-------+-------+-------+-------+
| node1 | node2 | node3 | node4 |  OR  | node1 | node2 | node3 | node4 |
+-------+-------+-------+-------+      +-------+-------+-------+-------+
|   P   |   P   |   P   |   P   |      |   P   |   P   |  P P  |       |
+-------+-------+-------+-------+      +-------+-------+-------+-------+
</code></pre><div>You can tweak the Pod spec to meet various kinds of requirements:</div><ul><li>Change&nbsp;<code>maxSkew</code>&nbsp;to a bigger value like "2" so that the incoming Pod can be placed onto "zoneA" as well.</li><li>Change&nbsp;<code>topologyKey</code>&nbsp;to "node" so as to distribute the Pods evenly across nodes instead of zones. In the above example, if&nbsp;<code>maxSkew</code>&nbsp;remains "1", the incoming Pod can only be placed onto "node4".</li><li>Change&nbsp;<code>whenUnsatisfiable: DoNotSchedule</code>&nbsp;to&nbsp;<code>whenUnsatisfiable: ScheduleAnyway</code>&nbsp;to ensure the incoming Pod to be always schedulable (suppose other scheduling APIs are satisfied). However, it�s preferred to be placed onto the topology domain which has fewer matching Pods. (Be aware that this preferability is jointly normalized with other internal scheduling priorities like resource usage ratio, etc.)<code>topologyKey: zone</code><span style="font-family: Arial;">&nbsp;</span><span style="font-family: Arial;">implies the even distribution will only be applied to the nodes which have label pair "zone:&lt;any value&gt;" present.</span><span style="font-family: Arial;">&nbsp;</span><code>whenUnsatisfiable: DoNotSchedule</code><span style="font-family: Arial;">&nbsp;</span><span style="font-family: Arial;">tells the scheduler to let it stay pending if the incoming Pod can�t satisfy the constraint.</span><div>If the scheduler placed this incoming Pod into "zoneA", the Pods distribution would become [3, 1], hence the actual skew is 2 (3 - 1) - which violates&nbsp;<code>maxSkew: 1</code>. In this example, the incoming Pod can only be placed onto "zoneB":</div><pre><code>+---------------+---------------+      +---------------+---------------+
|     zoneA     |     zoneB     |      |     zoneA     |     zoneB     |
+-------+-------+-------+-------+      +-------+-------+-------+-------+
| node1 | node2 | node3 | node4 |  OR  | node1 | node2 | node3 | node4 |
+-------+-------+-------+-------+      +-------+-------+-------+-------+
|   P   |   P   |   P   |   P   |      |   P   |   P   |  P P  |       |
+-------+-------+-------+-------+      +-------+-------+-------+-------+
</code></pre><div>You can tweak the Pod spec to meet various kinds of requirements:</div><ul><li>Change&nbsp;<code>maxSkew</code>&nbsp;to a bigger value like "2" so that the incoming Pod can be placed onto "zoneA" as well.</li><li>Change&nbsp;<code>topologyKey</code>&nbsp;to "node" so as to distribute the Pods evenly across nodes instead of zones. In the above example, if&nbsp;<code>maxSkew</code>&nbsp;remains "1", the incoming Pod can only be placed onto "node4".</li><li>Change&nbsp;<code>whenUnsatisfiable: DoNotSchedule</code>&nbsp;to&nbsp;<code>whenUnsatisfiable: ScheduleAnyway</code>&nbsp;to ensure the incoming Pod to be always schedulable (suppose other scheduling APIs are satisfied). However, it�s preferred to be placed onto the topology domain which has fewer matching Pods. (Be aware that this preferability is jointly normalized with other internal scheduling priorities like resource usage ratio, etc.)</li></ul></li></ul></code></pre><pre><code><span style="color: rgb(102, 102, 102);">
</span></code></pre>
</details>

<details>
<summary>
<h3>Example: Multiple TopologySpreadConstraints<a href="https://kubernetes.io/docs/concepts/workloads/pods/pod-topology-spread-constraints/#example-multiple-topologyspreadconstraints"></a></h3><div>&nbsp;Suppose you have a 4-node cluster where 3 Pods labeled&nbsp;<code>foo:bar</code>&nbsp;are located in node1, node2 and node3 respectively (<code>P</code>&nbsp;represents Pod):</div><pre><code>+---------------+---------------+
|     zoneA     |     zoneB     |
+-------+-------+-------+-------+
| node1 | node2 | node3 | node4 |
+-------+-------+-------+-------+
|   P   |   P   |   P   |       |
+-------+-------+-------+-------+
</code></pre><div>You can use 2 TopologySpreadConstraints to control the Pods spreading on both zone and node:</div>
</summary>
<pre><code><span style="color: rgb(170, 34, 255); font-weight: 700;">kind</span>:<span style="color: rgb(187, 187, 187);"> </span>Pod<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);"></span><span style="color: rgb(170, 34, 255); font-weight: 700;">apiVersion</span>:<span style="color: rgb(187, 187, 187);"> </span>v1<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);"></span><span style="color: rgb(170, 34, 255); font-weight: 700;">metadata</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">name</span>:<span style="color: rgb(187, 187, 187);"> </span>mypod<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">labels</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">foo</span>:<span style="color: rgb(187, 187, 187);"> </span>bar<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);"></span><span style="color: rgb(170, 34, 255); font-weight: 700;">spec</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">topologySpreadConstraints</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span>- <span style="color: rgb(170, 34, 255); font-weight: 700;">maxSkew</span>:<span style="color: rgb(187, 187, 187);"> </span><span style="color: rgb(102, 102, 102);">1</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">topologyKey</span>:<span style="color: rgb(187, 187, 187);"> </span>zone<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">whenUnsatisfiable</span>:<span style="color: rgb(187, 187, 187);"> </span>DoNotSchedule<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">labelSelector</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">      </span><span style="color: rgb(170, 34, 255); font-weight: 700;">matchLabels</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">        </span><span style="color: rgb(170, 34, 255); font-weight: 700;">foo</span>:<span style="color: rgb(187, 187, 187);"> </span>bar<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span>- <span style="color: rgb(170, 34, 255); font-weight: 700;">maxSkew</span>:<span style="color: rgb(187, 187, 187);"> </span><span style="color: rgb(102, 102, 102);">1</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">topologyKey</span>:<span style="color: rgb(187, 187, 187);"> </span>node<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">whenUnsatisfiable</span>:<span style="color: rgb(187, 187, 187);"> </span>DoNotSchedule<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">labelSelector</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">      </span><span style="color: rgb(170, 34, 255); font-weight: 700;">matchLabels</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">        </span><span style="color: rgb(170, 34, 255); font-weight: 700;">foo</span>:<span style="color: rgb(187, 187, 187);"> </span>bar<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">containers</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span>- <span style="color: rgb(170, 34, 255); font-weight: 700;">name</span>:<span style="color: rgb(187, 187, 187);"> </span>pause<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">image</span>:<span style="color: rgb(187, 187, 187);"> </span>k8s.gcr.io/pause:<span style="color: rgb(102, 102, 102);">3.1</span></code></pre><pre><code><div>In this case, to match the first constraint, the incoming Pod can only be placed onto "zoneB"; while in terms of the second constraint, the incoming Pod can only be placed onto "node4". Then the results of 2 constraints are ANDed, so the only viable option is to place on "node4".</div><div>Multiple constraints can lead to conflicts. Suppose you have a 3-node cluster across 2 zones:</div><pre><code>+---------------+-------+
|     zoneA     | zoneB |
+-------+-------+-------+
| node1 | node2 | node3 |
+-------+-------+-------+
|  P P  |   P   |  P P  |
+-------+-------+-------+
</code></pre><div>If you apply "two-constraints.yaml" to this cluster, you will notice "mypod" stays in&nbsp;<code>Pending</code>&nbsp;state. This is because: to satisfy the first constraint, "mypod" can only be put to "zoneB"; while in terms of the second constraint, "mypod" can only put to "node2". Then a joint result of "zoneB" and "node2" returns nothing.</div><div>To overcome this situation, you can either increase the&nbsp;<code>maxSkew</code>&nbsp;or modify one of the constraints to use&nbsp;<code>whenUnsatisfiable: ScheduleAnyway</code></div></code></pre><pre><code><span style="color: rgb(102, 102, 102);">
</span></code></pre>
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">Only the Pods holding the same _____ as the incoming Pod can be matching candidates</span>
</summary>
namespace
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">Nodes without&nbsp;</span><code>topologySpreadConstraints[*].topologyKey</code><span style="color: rgb(34, 34, 34);">&nbsp;present will be...</span>
</summary>
*bypassed*<div>*
*</div><div>This implies that:
<div><ol><li>the Pods located on those nodes do not impact&nbsp;<code>maxSkew</code>&nbsp;calculation - in the above example, suppose "node1" does not have label "zone", then the 2 Pods will be disregarded, hence the incomingPod will be scheduled into "zoneA".</li><li>the incoming Pod has no chances to be scheduled onto this kind of nodes - in the above example, suppose a "node5" carrying label&nbsp;<code>{zone-typo: zoneC}</code>&nbsp;joins the cluster, it will be bypassed due to the absence of label key "zone".</li></ol></div></div>
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">If the incoming Pod has&nbsp;</span><code>spec.nodeSelector</code><span style="color: rgb(34, 34, 34);">&nbsp;or&nbsp;</span><code>spec.affinity.nodeAffinity</code><span style="color: rgb(34, 34, 34);">&nbsp;defined, nodes not matching them will be...</span>
</summary>
bypassed
</details>

<details>
<summary>
<div>Suppose you have a 5-node cluster ranging from zoneA to zoneC:</div><pre><code>+---------------+---------------+-------+
|     zoneA     |     zoneB     | zoneC |
+-------+-------+-------+-------+-------+
| node1 | node2 | node3 | node4 | node5 |
+-------+-------+-------+-------+-------+
|   P   |   P   |   P   |       |       |
+-------+-------+-------+-------+-------+
</code></pre><div>and you know that "zoneC" must be excluded. In this case, you can compose the yaml as below, so that "mypod" will be placed onto "zoneB" instead of "zoneC". Similarly&nbsp;<code>spec.nodeSelector</code>&nbsp;is also respected.</div>
</summary>
<pre><code><span style="color: rgb(170, 34, 255); font-weight: 700;">kind</span>:<span style="color: rgb(187, 187, 187);"> </span>Pod<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);"></span><span style="color: rgb(170, 34, 255); font-weight: 700;">apiVersion</span>:<span style="color: rgb(187, 187, 187);"> </span>v1<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);"></span><span style="color: rgb(170, 34, 255); font-weight: 700;">metadata</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">name</span>:<span style="color: rgb(187, 187, 187);"> </span>mypod<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">labels</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">foo</span>:<span style="color: rgb(187, 187, 187);"> </span>bar<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);"></span><span style="color: rgb(170, 34, 255); font-weight: 700;">spec</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">topologySpreadConstraints</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span>- <span style="color: rgb(170, 34, 255); font-weight: 700;">maxSkew</span>:<span style="color: rgb(187, 187, 187);"> </span><span style="color: rgb(102, 102, 102);">1</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">topologyKey</span>:<span style="color: rgb(187, 187, 187);"> </span>zone<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">whenUnsatisfiable</span>:<span style="color: rgb(187, 187, 187);"> </span>DoNotSchedule<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">labelSelector</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">      </span><span style="color: rgb(170, 34, 255); font-weight: 700;">matchLabels</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">        </span><span style="color: rgb(170, 34, 255); font-weight: 700;">foo</span>:<span style="color: rgb(187, 187, 187);"> </span>bar<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">affinity</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">nodeAffinity</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">      </span><span style="color: rgb(170, 34, 255); font-weight: 700;">requiredDuringSchedulingIgnoredDuringExecution</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">        </span><span style="color: rgb(170, 34, 255); font-weight: 700;">nodeSelectorTerms</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">        </span>- <span style="color: rgb(170, 34, 255); font-weight: 700;">matchExpressions</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">          </span>- <span style="color: rgb(170, 34, 255); font-weight: 700;">key</span>:<span style="color: rgb(187, 187, 187);"> </span>zone<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">            </span><span style="color: rgb(170, 34, 255); font-weight: 700;">operator</span>:<span style="color: rgb(187, 187, 187);"> </span>NotIn<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">            </span><span style="color: rgb(170, 34, 255); font-weight: 700;">values</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">            </span>- zoneC<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">containers</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span>- <span style="color: rgb(170, 34, 255); font-weight: 700;">name</span>:<span style="color: rgb(187, 187, 187);"> </span>pause<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span><span style="color: rgb(170, 34, 255); font-weight: 700;">image</span>:<span style="color: rgb(187, 187, 187);"> </span>k8s.gcr.io/pause:<span style="color: rgb(102, 102, 102);">3.1</span></code></pre>
</details>

<details>
<summary>
<h2>Comparison of Topology with PodAffinity/PodAntiAffinity</h2><div><div>In Kubernetes, directives related to "Affinity" control how Pods are scheduled - more packed or more scattered.</div></div><div>
</div><div><ul><li>For <font face="monospace">_____</font>, you can try to pack any number of Pods into qualifying topology domain(s)</li><li>For&nbsp;<font face="monospace">_____</font>, only one Pod can be scheduled into a single topology domain.</li></ul></div>
</summary>
PodAffinity<div>
</div><div>PodAntiAffinity
</div>
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">It is possible to set default topology spread constraints for a cluster.&nbsp;</span><div><span style="color: rgb(34, 34, 34);">
</span></div><div><span style="color: rgb(34, 34, 34);">Default topology spread constraints are applied to a Pod if, and only if:</span></div>
</summary>
<ul><li>It doesn't define any constraints in its&nbsp;<code>.spec.topologySpreadConstraints</code>.</li><li>It belongs to a service, replication controller, replica set or stateful set.</li></ul>
</details>

<details>
<summary>
<div>An example *KubeSchedulerConfiguration *configuration for cluster-level default constraints might look like...</div>
</summary>
<pre><code><span style="color: rgb(170, 34, 255); font-weight: 700;">apiVersion</span>:<span style="color: rgb(187, 187, 187);"> </span>kubescheduler.config.k8s.io/v1alpha2<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);"></span><span style="color: rgb(170, 34, 255); font-weight: 700;">kind</span>:<span style="color: rgb(187, 187, 187);"> </span>KubeSchedulerConfiguration<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);"></span><span style="color: rgb(170, 34, 255); font-weight: 700;">profiles</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">  </span><span style="color: rgb(170, 34, 255); font-weight: 700;">pluginConfig</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">    </span>- <span style="color: rgb(170, 34, 255); font-weight: 700;">name</span>:<span style="color: rgb(187, 187, 187);"> </span>PodTopologySpread<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">      </span><span style="color: rgb(170, 34, 255); font-weight: 700;">args</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">        </span><span style="color: rgb(170, 34, 255); font-weight: 700;">defaultConstraints</span>:<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">          </span>- <span style="color: rgb(170, 34, 255); font-weight: 700;">maxSkew</span>:<span style="color: rgb(187, 187, 187);"> </span><span style="color: rgb(102, 102, 102);">1</span><span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">            </span><span style="color: rgb(170, 34, 255); font-weight: 700;">topologyKey</span>:<span style="color: rgb(187, 187, 187);"> </span>failure-domain.beta.kubernetes.io/zone<span style="color: rgb(187, 187, 187);">
</span><span style="color: rgb(187, 187, 187);">            </span><span style="color: rgb(170, 34, 255); font-weight: 700;">whenUnsatisfiable</span>:<span style="color: rgb(187, 187, 187);"> </span>ScheduleAnyway</code></pre>
</details>

<details>
<summary>
Does deleting deployments or pods bypass Pod Disruption Budgets?

</summary>
Yes
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">A _____ limits the number of pods of a replicated application that are down simultaneously from voluntary disruptions.</span><div><span style="color: rgb(34, 34, 34);">
</span></div><div><span style="color: rgb(34, 34, 34);">Ex.:</span></div><div><span style="color: rgb(34, 34, 34);">A quorum-based application would like to ensure that the number of replicas running is never brought below the number needed for a quorum.&nbsp;</span></div><div><span style="color: rgb(34, 34, 34);">
</span></div><div><span style="color: rgb(34, 34, 34);">A web front end might want to ensure that the number of replicas serving load never falls below a certain percentage of the total.</span><span style="color: rgb(34, 34, 34);">
</span></div>
</summary>
PodDisruptionBudget
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">Cluster managers and hosting providers should use tools which respect Pod Disruption Budgets by calling _____&nbsp;</span><span style="color: rgb(34, 34, 34);">instead of directly deleting pods or deployments</span><div><span style="color: rgb(34, 34, 34);">
</span></div><div><span style="color: rgb(34, 34, 34);">An example is the _____ command.</span></div>
</summary>
the Eviction API<div>
</div><div>kubectl drain</div>
</details>

<details>
<summary>
A PodDisruptionBudget&nbsp;<span style="color: rgb(34, 34, 34);">specifies the number of _____ that an application can tolerate having, relative to how many it is intended to have.</span>
</summary>
replicas
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">ADeployment has a&nbsp;</span><code>.spec.replicas: 5.&nbsp;</code><span style="color: rgb(34, 34, 34);">It is supposed to have 5 pods at any given time.&nbsp;</span><div><span style="color: rgb(34, 34, 34);">
</span></div><div><span style="color: rgb(34, 34, 34);">If its PDB allows for there to be 4 at a time, then the Eviction API will allow voluntary disruption of how many pods at a time?</span></div>
</summary>
<span style="color: rgb(34, 34, 34);">One</span>
</details>

<details>
<summary>
PodDisruptionBudgets cannot prevent involuntary disruptions from occurring.&nbsp;<div>
</div><div>Do involuntary disruptions count against the budget?</div>
</summary>
No
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">Do Pods which are deleted or unavailable due to a rolling upgrade to an application count against the disruption budget?</span>
</summary>
Yes
</details>

<details>
<summary>
Are controllers (like deployment or statefulset) limited by PDBs when doing rolling updates?
</summary>
*No*<div>
</div><div><span style="color: rgb(34, 34, 34);">The handling of failures during application updates is configured in the controller spec. (Learn about&nbsp;</span><a href="https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment">updating a deployment</a><span style="color: rgb(34, 34, 34);">.)</span></div>
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">When a pod is evicted using the eviction API, is it gracefully terminated?</span>
</summary>
Yes
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">An ephemeral container&nbsp; runs temporarily in an existing Pod&nbsp;</span><span style="color: rgb(34, 34, 34);">to accomplish...</span>
</summary>
<div>*user-initiated actions*&nbsp;</div><div>Such as troubleshooting and inspecting services</div>
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">Sometimes it's necessary to inspect the state of an existing Pod, however, for example to troubleshoot a hard-to-reproduce bug. In these cases you can run _____&nbsp;</span><span style="color: rgb(34, 34, 34);">in an existing Pod to _____</span>
</summary>
<span style="color: rgb(34, 34, 34);">an ephemeral container&nbsp;</span><div><span style="color: rgb(34, 34, 34);">
</span></div><div><span style="color: rgb(34, 34, 34);">inspect its state and run arbitrary commands</span>
</div>
</details>

<details>
<summary>
Will an ephemeral container ever be automatically restarted?
</summary>
No
</details>

<details>
<summary>
Do ephemeral containers have guaranteed resources?
</summary>
No
</details>

<details>
<summary>
Do ephemeral containers guarantee execution?
</summary>
No
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">Ephemeral containers are described using...</span>
</summary>
<span style="color: rgb(34, 34, 34);">the same&nbsp;</span><code>ContainerSpec</code><span style="color: rgb(34, 34, 34);">&nbsp;as regular containers</span><div><span style="color: rgb(34, 34, 34);">
</span></div><div><span style="color: rgb(34, 34, 34);">However, many fields are incompatible and disallowed</span></div>
</details>

<details>
<summary>
<div>You are trying to interactively troubleshoot a container.&nbsp;</div><div>
</div><div><code>kubectl exec</code>&nbsp;was insufficient because the container has crashed</div><div>
</div><div>The container image is *minimal *and&nbsp;*distroless *and doesn't include debugging utilities.</div>
<div>What can you use to troubleshoot?</div>
</summary>
Ephemeral containers
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">When using ephemeral containers, it's helpful to enable _____&nbsp;</span>so you can view processes in other containers.

</summary>
process namespace sharing
</details>

<details>
<summary>
<span style="color: rgb(34, 34, 34);">Ephemeral containers are created using the&nbsp;</span><code>ephemeralcontainers</code><span style="color: rgb(34, 34, 34);">&nbsp;</span><span style="color: rgb(34, 34, 34);">&nbsp;</span><span style="color: rgb(34, 34, 34);">subresource of Pod,&nbsp;</span><span style="color: rgb(34, 34, 34);">which can be demonstrated using&nbsp;</span><code>kubectl --raw.</code><div><code>
</code></div><div>First describe the ephemeral container to add as an EphemeralContainers list:<code>
</code></div>
</summary>
<div>{
</div><div><pre><code>    <span style="color: green; font-weight: 700;">"apiVersion"</span>: <span style="color: rgb(187, 68, 68);">"v1"</span>,
    <span style="color: green; font-weight: 700;">"kind"</span>: <span style="color: rgb(187, 68, 68);">"EphemeralContainers"</span>,
    <span style="color: green; font-weight: 700;">"metadata"</span>: {
            <span style="color: green; font-weight: 700;">"name"</span>: <span style="color: rgb(187, 68, 68);">"example-pod"</span>
    },
    <span style="color: green; font-weight: 700;">"ephemeralContainers"</span>: [{
        <span style="color: green; font-weight: 700;">"command"</span>: [
            <span style="color: rgb(187, 68, 68);">"sh"</span>
        ],
        <span style="color: green; font-weight: 700;">"image"</span>: <span style="color: rgb(187, 68, 68);">"busybox"</span>,
        <span style="color: green; font-weight: 700;">"imagePullPolicy"</span>: <span style="color: rgb(187, 68, 68);">"IfNotPresent"</span>,
        <span style="color: green; font-weight: 700;">"name"</span>: <span style="color: rgb(187, 68, 68);">"debugger"</span>,
        <span style="color: green; font-weight: 700;">"stdin"</span>: <span style="color: rgb(170, 34, 255); font-weight: 700;">true</span>,
        <span style="color: green; font-weight: 700;">"tty"</span>: <span style="color: rgb(170, 34, 255); font-weight: 700;">true</span>,
        <span style="color: green; font-weight: 700;">"terminationMessagePolicy"</span>: <span style="color: rgb(187, 68, 68);">"File"</span>
    }]
}</code></pre></div>
</details>
