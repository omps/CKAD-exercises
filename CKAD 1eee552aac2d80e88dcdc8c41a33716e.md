# CKAD

### 🔧 **Application Development and Deployment**

1. **Create a multi-container Pod** with a sidecar logging container that tails logs from the main app container.
    
    multi container pod can be created only by usingYAML or JSON formatted configuration file.
    
    ```bash
    kubectl create -f FILE
    ```
    
    YAML file to be create, below is the template for the YAML file
    
    ```yaml
    apVersion: v1 
    kind: Pod                           # Kind is always pod
    metadata:                           # An object containing
    	name: ""                          # Required if generateName is not specified. The name of this pod, should be compaitable with RFC1035, and unique value within namespce
    	labels:                           # optional: arbitary key:value pairs , can be used by deployment and services for grouping and tageting pods
    		name: ""                        # same validation rule as name
    	namespace: ""                     # Required as the namespace of the pod
    	annotations: []                   # A map of string keys and values, can be used by extrnal tooling to store and retrieve arbitary metadata about projects
    	generateName: ""
    spec:                               # The pod specification as per the spec schema
    ? "//See 'The speck schema' for details."
    : ~
    ```
    
    Below is the contents of the spec block
    
    ```yaml
    spec:
    	containers:                   # This is an object and must contain the following
    	-                             
    		args:                       # A command array containing arguments to the entrypoint. THe docker images's cmd is used if this is not provided. Cannot be updated
    			- ""
    		command:                    # The entrypoint array. Commands are not executed within a shell. THe docker's image entrypoint is used if this is not provided. This cannot be updated
    			- ""
    		env:                        # A list of environment variables in key:value format to set in the container, cannot be updated
    			- 
    			name: ""                  # The name of the environment variable; must be a C_IDENTIFIER
    			value: ""                 # The vaule of the environment variable. Defaults to empty string
    		image:                      # Docker image name
    		imagePullPolicy: ""         # The image pull policy, accepted vaules are: Always, Never; IfNotPresent defaults to Always if ':latest' tag is specified, or IfNotPresent otherwise. Cannot be updated
    		name: ""                    # Name of the container. It must be a DNS_LABEL and unique withing the pod. Cannot be updated.
    		ports:                      # A list of ports to expose to and from the container
    			-
    				containerPort: 0        # The port no. to expose on the POD ip
    				name: """               # The name for the port, which can be referred by services. Must be a DNS_LABEL and be unique without the pod.
    				protocol: ""            # Protocol for the port. Must be UDP or TCP. Default: TCP
    		resources:                  # The compute resources require by this container. Containe CPU and Memory
    			cpu: ""                   # CPU to reserce for each container, default is whole CPUs; scale suffixes (e.g. 100m for one hundered milli-CPUs) are supported. If the host does not have enough available resources your pod will not be scheduled.
    			memory: ""                # Memory to reserve for each container. Default is bytes; binary sale suffices (e.g. 100Mi for onr hundered mebibytes) are supported. if the host does not have enough resources, your pod will not will scheduled. Cannot be updated.
    	restartPolicy: ""             # Restart policy for all the containers in the Pod, options are: Always; OnFailure; Never
    	volumes:                      # List of volumes which can be mounted on the pod. Specify name and source for each vlume. Container *must* contain VolumeMount with matching name. Source is one of
    		-
    			emptyDir:                 # A temporary directory that shares a pod's lifetime. Contains:
    				medium: ""              # The type of storage used to back the volume. Must be an empty string (default) or Memory
    			name: ""                  # must be a DNS_LABEL and unique within the pod
    			secret:                   # Secret to populate volume. Secrets are used to hold sensitive information, such as passwords, OAuth tokens, and SSH keys. Contains
    			secretName: ""            # The name of secret in the podnamespace
    			hostpath: ""              # A pre-existing host file or directory. This is genereally used for privileged system daemons or other agents tied to the host. contains
    				path: ""                # The path of the directory on the host.
    ```
    
    The below configuration should create 2 containers; one redis key-value store image and a django frontend image
    
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
    	name: redis-django
    	labels:
    	app: web
    spec:
    	containers:
    	- name: key-value-store
    		image: redis
    		ports:
    			- containerPort: 6379
    	- name: frontend
    		image: django
    		ports:
    			- containerPort: 8000
    ```
    
    all code is at https://github.com/omps/django-k8s-demo
    
    The code does create 3 containers in the pod with one sidecar container, this is visible with
    
    `kubectl describe pods -n webapp`
    
    I am not able to see the logs from the command
    
    `kubectl logs -f django-app -c log-sidecar -n webapp`
    
    However the app is still not working.
    
    Few command learnt during trial and error
    
    `kubectl get events -n webapp` #to get the events for the pods in namespace
    
    `kubectl get pods -w` (-w switch watches the pod)
    
    `source <(kubectl completion zsh)` # for zsh tab completion
    
2. **Create a Deployment** that hosts a web app and auto-scales based on CPU usage (use `HorizontalPodAutoscaler`).
3. **Perform a rolling update** to change the app version with zero downtime.
4. **Rollback** to a previous Deployment version after a bad image is deployed.
5. **Set environment variables** from a `ConfigMap` and `Secret` into a Pod definition.
6. **Configure a readiness probe** that checks a specific path and a liveness probe that restarts the container on crash.
7. **Create a Job** that runs a batch import process and exits upon completion. Repeat using a **CronJob**.

---

### 🌐 **Networking and Ingress**

1. **Create a Service** for a set of backend Pods and expose it internally using ClusterIP.
2. **Expose the same service using Ingress** with path-based routing for two different apps.
3. **Secure the Ingress using TLS** and custom hostname.
4. **Use a NetworkPolicy** to restrict Pod communication to same namespace only.
5. **Simulate a real-world failure** where a misconfigured Ingress routes traffic incorrectly. Fix the Ingress.

---

### 🔐 **Security Context and RBAC**

1. **Create a ServiceAccount** and bind it using RBAC to allow read-only access to Pods.
2. **Launch a Pod with a restrictive security context** (no root, read-only root filesystem, limited capabilities).
3. **Enforce pod-level access control** with a custom Role and RoleBinding scoped to a namespace.
4. **Use a Secret** to inject credentials into a container via environment variable.

---

### 📦 **Volumes and Persistence**

1. **Create a PersistentVolumeClaim** using a dynamic storage class and mount it to a Pod.
2. **Simulate a Pod restart** and ensure that the data written to volume remains persistent.
3. **Use an `emptyDir` volume** for sharing cache between two containers in a Pod.
4. **Create an init container** that downloads a config file into a shared volume for the main container.

---

### 🛠️ **Advanced Scheduling and Resource Management**

1. **Deploy an application with resource limits and requests**, and verify scheduler behavior under pressure.
2. **Use node affinity and taints/tolerations** to schedule workloads to specific nodes.
3. **Simulate node failure** and verify how Deployment recovers Pods on healthy nodes.

---

### 📊 **Logging and Monitoring Scenarios**

1. **Mount a volume** and configure log files for sidecar collection.
2. **Deploy a DaemonSet** to simulate a log collector running on each node.
3. **Expose Prometheus metrics endpoint** from an application Pod and test it with curl.

---

### 🧪 **Realistic Troubleshooting Scenarios**

1. **Fix a broken Deployment** (e.g., image not found, failed health checks).
2. **Investigate a CrashLoopBackOff** error and recover from it.
3. **Use `kubectl debug`** to troubleshoot a failing container.
4. **Simulate service DNS resolution issues** and resolve them.
5. **Audit a cluster config** to fix Pod stuck in `Pending` due to unbound PVC.

---

### 💡 **Cross-Domain/Integration Tasks**

1. **Create an app with external configuration** pulled from Git using an init container.
2. **Integrate an app using a headless service** for internal DNS-based discovery.
3. **Deploy a replicated stateful service** (e.g., Redis) using `StatefulSet` with stable network identities.
4. **Simulate a full blue-green deployment** using two separate Deployments and switch traffic using Services.