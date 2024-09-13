# Kubernetes: Up and Running (Dive into the future of Infrastructure)" by Brendan Burns, Joe Beda & Kelsey Hightower

## 1. Introduction
Kubernetes is an open source orchestrator for deploying containerized applications developed by Google(2014). Is  the standard API for building cloud-native applications. The main reasons to use Kubernetes are:
- Velocity: Today, all users expect constant uptime, even if the software they are running is changing constantly, so no downtime is allowed. Velocity is measured not in terms of the raw number of features you
can ship per hour or day, but rather in terms of the number of things you can ship while maintaining a highly available service. Kubernetes provides: 
    - Immutability: once an artifact is created in the system it does not change via user modifications. In mutable infrastructure changes are applied as incremental updates to an existing system, an example of this is apt-get which runs sequentially. In an immutable system, rather than a series of incremental updates and changes, an entirely new, complete image is built, where the update simply replaces the entire image with the newer image in a single operation. Building a new image rather than modifying an existing one means the old image is still around, and can quickly be used for a rollback if an error occurs
    - Declarative configuration: Everything in Kubernetes is a declarative configuration object that represents the desired state of the system
    - Online self-healing systems: When it receives a desired state configuration, it does not simply take a set of actions to make the current state match the desired state a single time. It continuously takes actions to ensure that the current state matches the desired state and it will guard it against any failures. A more traditional operator repair involves a manual series of mitigation steps, requires an on-call operator, is slower, less reliable. Kubernetes allows reliable repairs more quickly. For instance, if you say that your Kubernetes must have 3 replicas, not only creates 3 creates, assures that there always 3 replicas running, if manualy another one is created, one of them will be destroyed to assure there are 3

- Scaling (of both software and teams): Kubernetes achieves scalability by favoring decoupled architectures.
    - Decoupling: each component is separated from other components by defined APIs and service load balancers. Decoupling components via load balancers makes it easy to scale the programs that make up your service. Decoupling servers via APIs makes it easier to scale the development teams because each team can focus on a single, smaller microservice
    - Easy Scaling for Applications and Clusters: your containers are immutable, and the number of replicas is merely a number in a declarative config or you can set up autoscaling and let Kubernetes take care of it for you but this assumes there are resources in the cluster, so sometimes you also need to scale up the cluster and kubernetes can help forecasting future compute costs.
    - Scaling Development Teams with Microservices: imagine that each team takes care of a microservice or a set of microservices, aggregating all of them can be tricky. Kubernetes provides numerous abstractions and APIs that facilitate this task.
        - Pods, or groups of containers, can group together container images developed by different teams into a single deployable unit.
        - Kubernetes services provide load balancing, naming, and discovery to isolate one microservice from another.
        - Namespaces provide isolation and access control, so that each microservice can control the degree to which other services interact with it.
        - Ingress objects provide an easy-to-use frontend that can combine multiple micro services into a single externalized API surface area.
        
        Decoupling the application container image and machine means that different microservices can colocate on the same machine without interfering with one another, reducing the overhead and cost of microservice architectures. Health-checking and rollout features of Kubernetes guarantee a consistent approach to application rollout and reliability that ensures that a proliferation of microservice teams does not also result in a proliferation of different approaches to service production lifecycle and operations.
    - Separation of Concerns for Consistency and Scaling: Kubernetes stack lead to significantly greater consistency for the lower levels of your infrastructure, enabling you to scale infrastructure operations to manage many machines with a single small, focused team. The container orchestration API becomes a crisp contract that separates the responsibilities of the application operator from the cluster orchestration operator, application developer relies on the SLA delivered by the container orchestration API and the reliability engineer doesn't need to know anything about the application. A small team can manage multiple Kubernetes clusters, each with hundreds or even thousands of teams running applications within that cluster, for this Kubernetes-as-a-Service (KaaS) provided by a public cloud provider is a great option

- Abstracting your infrastructure: The goal of the public cloud is to provide easy-to-use, self-service infrastructure for developers to consume but too often they mirror the infrastructure IT expects like VMs instead of Applications. The move to application-oriented container APIs like Kubernetes has two concrete benefits: separates developers from specific machines and enables a high degree of portability, you just need to send the Kubernetes declarative config file to the right cluster, to make this work you need to abstract from cloud-managed services (AWS DynamoDb, Azure CosmoDB, etc) you need to go for open source solutions like mongodb or mysql.

- Efficiency: Multiple applications  can be colocated int he same machines. Efficiency can be measured by the ratio of the useful work performed by a machine or process the total amount of energy doing so. Kubernetes provides tools that automate the distribution of applications across a cluster of machines, ensuring higher levels of utilization than are possible with traditional tooling. A developer’s test environment can be quickly and cheaply created as a set of containers running in a personal view of a shared Kubernetes cluster (using a feature called namespaces). It's a common practice to have a test cluster where all developers deploy their feature branches and they can be validated.


## 2. Creating and running containers

Applications typically have dependencies on libraries that are part of the OS, we can only assure that an application works if the server has exactly the same applications and versions that the machine where it was developed. For distribued systems this can be extremely difficult. Container images (like Docker images) bundle a program and its dependencies into a single artifact under a root filesystem

**The Docker Image Format**: is the most used container image format and is made of system layers, each layer adds/removes/modifies the preceeding layer. For example we can create our own image by taking an existing one and adding redis, rails or whatever other tool we need. We can have *System containers* which are not very used today and *Application containers* typically jsut run a single program, our application.

**Building Application Images with Docker**: A Dockerfile can be used to automate the creation of a Docker container image.

```Dockerfile
# Start from a Node.js 10 (LTS) image
FROM node:10
# Specify the directory inside the image in which all commands will run
WORKDIR /usr/src/app

# Copy package files and install dependencies
COPY package*.json ./
RUN npm install
# Copy all of the app files into the image
COPY . .
# The default command to run when starting the container
CMD [ "npm", "start" ]
```
Now you can build the image `docker build -t simple-node`
And run the image ` docker run --rm -p 3000:3000 simple-node`

## 3. Deploying a Kubernetes Cluster

The Kubernetes Client: `kubectl` is a command-line tool for interacting with the Kubernetes API

```bash
# Checking Cluster Status
 kubectl version

#  get a simple diagnostic for the cluster
 ubectl get componentstatuses

# Listing Kubernetes Worker Nodes
kubectl get nodes

#  get more information about a specific node
kubectl describe nodes node-1
```

**Kubernetes cluster components**: many of the components that make up the Kubernetes cluster are actually deployed using Kubernetes itself
    - Kubernetes Proxy: is responsible for routing network traffic to load-balanced services in the Kubernetes cluster. The proxy must be present on every node in the cluster
    - Kubernetes DNS: provides naming and discovery for the services that are defined in the cluster. DNS server also runs as a replicated service on the cluster
    - Kubernetes UI: The UI is run as a single replica (not always installed)


## 4. Common kubctl Commands

**Namespaces**: are used  to organize objects in the cluster, think of each namespace as a folder that holds a set of objects. By default kubectl`` interacts with the default namespace.Example: `kubectl --namespace=mystuff` references objects in the mystuff namespace

**Contexts**: are used to change the default namespace more permanent. This gets recorded in a kubectl configuration file, usually located at `$HOME/.kube/config`. This file also stores how to find an atuhenticate to the cluster.

```bash
#create a context with a different default namespace
kubectl config set-context my-context --namespace=mystuff

# your new context has been created, to start using it you run
kubectl config use-context my-context
```

**Viewing Kubernetes API Objects**: Everything contained in Kubernetes is represented by a RESTful resource, is named a *Kubernetes object* an exists at a unique HTTP path. Example: https://your-k8s.com/api/v1/name‐spaces/default/pods/my-pod leads to the representation of a Pod in the default namespace named my-pod. The kubectl command makes HTTP requests to these URLs to access the Kubernetes objects that reside at these paths.
```bash
#  get a listing of all resources in the current namespace
kubectl get <resource-name>

# to get a specific resource
kubectl get <resource-name> <obj-name>

# to get the response in json/yaml format, otherwise will be human readable
kubectl get pods my-pod -o json

# uses the JSONPath query language to extract and print the IP address of the specified Pod
kubectl get pods my-pod -o jsonpath --template={.status.podIP}

# to get more detailed information about a particular object
kubectl describe <resource-name> <obj-name>
```
**Creating, Updating, and Destroying Kubernetes Objects**: Objects in the Kubernetes API are represented as JSON or YAML files
```bash
#  create or update an  object stored in obj.yaml
# use parameters --dry-run to print the objects to the terminal without actually sending them to the server
kubectl apply -f obj.yaml

kubectl apply -f obj.yaml

# to delete an object. NO PROMPT requesting confirmation
kubectl delete -f obj.yaml
```

**Labeling and Annotating Objects**: Labels and annotations are tags for your objects. Example: `kubectl label pods bar color=red`

## 5. Pods

In real-world deployments of containerized applications you will often want to colocate multiple applications into a single atomic unit, scheduled onto a single machine. Kubernetes groups multiple containers into a single atomic unit called a Pod

**Pods in Kubernetes**: A Pod represents a collection of application containers and volumes running in the same execution environment. Pods, not containers, are the smallest deployable artifact in a Kubernetes cluster. This means all of the containers in a Pod always land on the same machine. Applications running in the same Pod share the same IP address and port space, same hostname. Applications in different Pods have different IP addresses, different hostnames,... 

**“What should I put in a Pod?”**: Does it make sense to put WOrdpress and Mysql in the same Pod? If you group the WordPress and MySQL containers together in a single Pod, you are forced to use the same scaling strategy for both containers, which doesn’t fit well. In general, the right question to ask yourself when designing Pods is, “Will these containers work correctly if they land on different machines?” If the answer is “no,” a Pod is the correct grouping for the containers. If the answer is “yes,” multiple Pods is probably the correct solution

**The Pod Manifest**: The Pod manifest is just a text-file representation of the Kubernetes API object. The Kubernetes API server accepts and processes Pod manifests before storing them in persistent storage (etcd). The scheduler also uses the Kubernetes API to find Pods that haven’t been scheduled to a node. The scheduler then places the Pods onto nodes depending on the resources and other constraints expressed in the Pod manifests. Multiple Pods can be placed on the same machine if there are enough resources. Once scheduled to a node, Pods don’t move and must be explicitly destroyed and rescheduled.

```bash
# Create a pod
kubectl run kuard --generator=run-pod/v1 \ --image=gcr.io/kuar-demo/kuard-amd64:blue

# see the status of a pod
kubectl get pods

# delete a pod
kubectl delete pods/kuard
```
Creating a Pod Manifest: can be written using YAML or JSON, but YAML is generally preferred because it is slightly more human-editable and has the ability to add comments. Example kuard-pod.yaml``
```yaml
apiVersion: v1
kind: Pod
metadata:
    name: kuard
spec:
    volumes:
        - name: "kuard-data"
          nfs:
            server: my.nfs.server.local
            path: "/exports"
    containers:
        - image: gcr.io/kuar-demo/kuard-amd64:blue
          name: kuard
          ports:
            - containerPort: 8080
              name: http
              protocol: TCP
          resources:
            requests:
                cpu: "500m"
                memory: "128Mi"
            limits:
                cpu: "1000m"
                memory: "256Mi"
          volumeMounts:
            - mountPath: "/data"
              name: "kuard-data"
          livenessProbe:
            httpGet:
                path: /healthy
                port: 8080
            initialDelaySeconds: 5
            timeoutSeconds: 1
            periodSeconds: 10
            failureThreshold: 3
          readinessProbe:
            httpGet:
                path: /ready
                port: 8080
            initialDelaySeconds: 30
            timeoutSeconds: 1
            periodSeconds: 10
            failureThreshold: 3
```

```bash
# run a pod 
 kubectl apply -f kuard-pod.yaml

# list pods
kubectl get pods

# see pod details
kubectl describe pods kuard

#delete a pod using the name
kubectl delete pods/kuard

#delete a pod using the manifest
kubectl delete -f kuard-pod.yaml
```

**Accessing Your Pod**: Now that the Pod is running, we want to access it

.... To be continued

## 6. Labels and Annotations

Labels and annotations let you work in sets of things that map to how you think about your application. You can organize, mark, and cross-index all of your resources to represent the groups that make the most sense for your application.

**Labels** In addition to enabling users to organize their infrastructure, labels play a critical role in linking various related Kubernetes objects. There is no hierarchy and all components operate independently. However, in many cases objects need to relate to one another, and these relationships are defined by labels and label selectors. Labels have simple syntax:
  - They are key/value pairs, where both the key and value are represented by strings.
  - Label keys can be broken down into two parts separated by a slash: 
    - Prefix (optional): if specified, must be a DNS subdomain with a 253-character limit
    - Name: is required and must be shorter than 63 characters. Names must also start and end with an alphanumeric character and permit the use of dashes (-), underscores (_), and dots (.) between characters
  - When domain names are used in labels and annotations they are expected to be aligned to that particular entity in some way. For example  to identify the various stages of application deployment (e.g., staging, canary, production).


**Annotations**:  provide a place to store additional metadata for Kubernetes objects with the sole purpose of assisting tools and libraries. They are a way for other programs driving Kubernetes via an API to store some opaque data with an object. While labels are used to identify and group objects, annotations are used to provide extra information about where an object came from, how to use it, or policy around that object. Are primarily used for rolling deployments, to keep track of rollouts and rollback if needed. Are good for small bits of data but should not be used as a general-purpose database


... To be continued

## 7. Service Discovery

Servicediscovery tools help solve the problem of finding which processes are listening at which addresses for which services. A good service-discovery system will enable users to resolve this information quickly, reliably and with low-latency. Real service discovery in Kubernetes starts with a Service object. 
  
... To be continued

## 8. HTTP Load Balancing with Ingress

## 9. ReplicaSets

## 10. Deployments

## 11. DaemonSets

## 12. Jobs

## 13. ConfigMaps and secrets

## 14. Role-Based Access Control for Kubernetes

## 15. Integrating Storage Solutions and Kubernetes

## 16. Extending Kuebernetes

## 17. Deploying Real-World Applications

## 18. Organizing your Application
