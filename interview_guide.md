# DevOps & Kubernetes Interview Preparation Guide

## Table of Contents
1. [DevOps Interview Questions](#devops-interview-questions)
2. [Kubernetes Interview Questions](#kubernetes-interview-questions)
   - [Beginner Level](#beginner-level)
   - [Intermediate Level](#intermediate-level)
   - [Advanced Level](#advanced-level)
3. [Key Commands Reference](#key-commands-reference)

---

## DevOps Interview Questions

### Q1. Explain the CI/CD workflow you follow and the kind of pipeline you use. How do you define and invoke pipelines in Jenkins?

**Answer:**
- **CI/CD Workflow:** Code pushed → Webhook triggers Jenkins pipeline → Build → Unit tests → Docker image build → Push to registry → Deploy to K8s/EC2
- **Pipeline types:** Declarative (preferred, written in Jenkinsfile) or Scripted
- **Invocation:** Pipelines are triggered via webhooks (GitHub/GitLab), manually, or via cron jobs

### Q2. What are shared libraries in Jenkins, and how are they written and defined?

**Answer:**
- Shared libraries are reusable code blocks used across multiple Jenkins pipelines
- They are stored in a Git repo and defined in `src/` (Groovy classes) and `vars/` (pipeline functions)
- Added in Jenkins global configuration under Manage Jenkins → Configure System → Global Pipeline Libraries

### Q3. What kind of applications do you deploy using Jenkins pipelines, and what deployment tools do you use?

**Answer:**
- **Applications:** Microservices, Java (Spring Boot), Node.js, Python apps, etc.
- **Deployment tools:** Kubernetes (kubectl, Helm), Ansible, Docker Swarm, AWS ECS/EKS

### Q4. If the Jenkins pipeline runs but the build doesn't happen, what possible issues could be causing it?

**Answer:**
- Incorrect build commands
- Missing dependencies in Jenkins agent
- SCM checkout failure
- Misconfigured Jenkinsfile
- Insufficient permissions

### Q5. What is the purpose of a webhook, and how is it used in a CI/CD pipeline?

**Answer:**
- A webhook is an HTTP callback triggered by events (e.g., Git push)
- In CI/CD, it triggers Jenkins to start a pipeline automatically when code is pushed

### Q6. How do you create and manage Kubernetes clusters (using tools like Terraform), and what are the master and worker nodes?

**Answer:**
- Using Terraform modules for cloud providers (AWS EKS, GKE, AKS)
- **Master node:** runs control plane (API server, etcd, scheduler, controller)
- **Worker nodes:** run workloads (Pods) managed by kubelet and kube-proxy

### Q7. What are common Kubernetes errors you've faced (like CrashLoopBackOff, ImagePullError), and how did you resolve them?

**Answer:**
- **CrashLoopBackOff:** Bad app config, fixed by checking logs (`kubectl logs`) and readiness probes
- **ImagePullError:** Wrong image name/tag or missing registry credentials. Fixed by correcting image name and creating imagePullSecret

### Q8. What is the command to access a pod and how can you define or create a Kubernetes class or object?

**Answer:**
- **Access Pod:** `kubectl exec -it <pod-name> -- /bin/sh`
- **Define Kubernetes object:** Write YAML manifest (apiVersion, kind, metadata, spec) and apply it using: `kubectl apply -f pod.yaml`

### Q9. Explain the folder structure of a basic Helm chart. What commands do you use to deploy with Helm?

**Answer:**
```
mychart/
├── Chart.yaml
├── values.yaml
├── templates/
│   ├── deployment.yaml
│   ├── service.yaml
```

**Commands:**
- `helm create mychart`
- `helm install myapp ./mychart`
- `helm upgrade myapp ./mychart`
- `helm uninstall myapp`

### Q10. What are the stages in a Docker image build? Why do we use ENTRYPOINT and CMD instructions?

**Answer:**
- **Stages:** Pull base image → Copy code → Install dependencies → Run build/test → Expose ports → Set entrypoint/cmd
- **ENTRYPOINT:** Defines the main process (fixed)
- **CMD:** Provides default arguments to ENTRYPOINT

### Q11. How do you manage and connect services like DBs, EC2, EKS, or ECS? Include the command to connect to ECS.

**Answer:**
- Managed using Terraform/Ansible + AWS CLI
- **Connect to ECS:** `aws ecs execute-command --cluster myCluster --task myTask --container myContainer --command "/bin/sh"`

### Q12. Which container registry do you use for storing Docker images?

**Answer:**
Docker Hub, AWS ECR, GCP Artifact Registry, or Azure ACR.

**Example with AWS ECR:**
```bash
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin <account-id>.dkr.ecr.us-east-1.amazonaws.com
docker build -t myapp .
docker tag myapp:latest <account-id>.dkr.ecr.us-east-1.amazonaws.com/myapp:latest
docker push <account-id>.dkr.ecr.us-east-1.amazonaws.com/myapp:latest
```

---

## Kubernetes Interview Questions

### Beginner Level

#### Q1. What is Kubernetes?
**Answer:** Kubernetes is an open-source container orchestration platform that automates deployment, scaling, and management of containerized applications.

#### Q2. What is a Pod in Kubernetes?
**Answer:** A Pod is the smallest deployable unit in Kubernetes. It represents one or more containers that run together on the same node, sharing network and storage.

#### Q3. What is the difference between a Pod and a Container?
**Answer:**
- A container is a single instance of an application (Docker image runtime)
- A Pod wraps one or more containers with shared network/storage

#### Q4. What is a Deployment?
**Answer:** A Deployment is a higher-level object that manages Pods. It ensures a desired number of replicas are running and supports rolling updates and rollbacks.

#### Q5. What is a ReplicaSet?
**Answer:** A ReplicaSet ensures that a specified number of Pod replicas are running at any given time. A Deployment typically manages ReplicaSets.

#### Q6. What is a Service in Kubernetes?
**Answer:** A Service provides stable network access to Pods. It abstracts Pod IPs and gives a single DNS name or IP.

**Types:**
- ClusterIP (default, internal access)
- NodePort (exposes via node IP and port)
- LoadBalancer (cloud load balancer)
- ExternalName (maps to DNS)

#### Q7. What is the difference between NodePort and LoadBalancer Service?
**Answer:**
- **NodePort:** Exposes the Service on a static port across all nodes
- **LoadBalancer:** Provisions an external load balancer (via cloud provider) to route traffic to the Service

#### Q8. What is a Namespace in Kubernetes?
**Answer:** Namespaces are logical partitions in a cluster. They allow resource isolation and management for different teams/projects.

#### Q9. What is etcd in Kubernetes?
**Answer:** etcd is a distributed key-value store used by Kubernetes to store all cluster data, including configurations, secrets, and state.

#### Q10. What is kube-apiserver?
**Answer:** The API server is the central management component in Kubernetes. It exposes the Kubernetes API, validates requests, and updates etcd.

### Intermediate Level

#### Q11. What is kubelet?
**Answer:** kubelet runs on each worker node and ensures that the containers in Pods are running as expected.

#### Q12. What is kube-proxy?
**Answer:** kube-proxy runs on each node and maintains network rules to allow communication to Services and load balances traffic to Pods.

#### Q13. What are Endpoints in Kubernetes?
**Answer:** Endpoints are objects that store Pod IPs linked to a Service. They tell Services which Pods to forward traffic to.

#### Q14. What is EndpointSlice?
**Answer:** EndpointSlice is the scalable replacement for Endpoints. It splits large numbers of endpoints into smaller objects for efficiency.

#### Q15. What is a Headless Service?
**Answer:** A Service with `clusterIP: None`. Instead of load balancing, it returns Pod IPs directly via DNS. Useful for StatefulSets and databases.

#### Q16. What are ConfigMaps and Secrets?
**Answer:**
- **ConfigMap:** Stores non-confidential config data
- **Secret:** Stores confidential data (passwords, tokens) in base64-encoded form

#### Q17. What is a StatefulSet?
**Answer:** A StatefulSet manages stateful applications. It provides stable network identity and persistent storage for Pods. Used in databases like MySQL, Cassandra.

#### Q18. What is a DaemonSet?
**Answer:** A DaemonSet ensures that a copy of a Pod runs on every node in the cluster. Common for logging, monitoring agents, etc.

#### Q19. What is a Job in Kubernetes?
**Answer:** A Job runs a Pod until it completes successfully. It's used for batch processing tasks.

#### Q20. What is a CronJob?
**Answer:** A CronJob runs Jobs on a scheduled basis, similar to Linux cron.

#### Q21. What are taints and tolerations?
**Answer:**
- **Taint:** Applied to nodes to repel Pods
- **Toleration:** Applied to Pods to allow them to run on tainted nodes

#### Q22. What are affinity and anti-affinity?
**Answer:**
- **Affinity:** Ensures Pods are scheduled together
- **Anti-affinity:** Ensures Pods are spread across nodes

#### Q23. What is Horizontal Pod Autoscaler (HPA)?
**Answer:** HPA automatically scales the number of Pod replicas based on metrics (CPU, memory, custom metrics).

#### Q24. What is Vertical Pod Autoscaler (VPA)?
**Answer:** VPA adjusts the CPU and memory requests/limits of Pods dynamically.

#### Q25. What is Cluster Autoscaler?
**Answer:** Cluster Autoscaler automatically adjusts the number of nodes in the cluster based on workload demands.

#### Q26. What is the difference between Requests and Limits in Kubernetes?
**Answer:**
- **Request:** Minimum amount of CPU/memory guaranteed to the Pod
- **Limit:** Maximum amount a Pod can use

#### Q27. How does Kubernetes do rolling updates?
**Answer:** A Deployment updates Pods gradually, replacing old Pods with new ones, while keeping the application available.

#### Q28. What is a liveness probe?
**Answer:** A liveness probe checks if a container is still running. If it fails, Kubernetes restarts the container.

#### Q29. What is a readiness probe?
**Answer:** A readiness probe checks if a container is ready to serve traffic. If it fails, the Pod is removed from Service endpoints.

#### Q30. What is an init container?
**Answer:** An init container runs before the main app container starts. Used for setup tasks like waiting for dependencies.

### Advanced Level

#### Q31. How does Kubernetes handle networking between Pods?
**Answer:** Kubernetes uses a flat networking model. Every Pod gets its own IP, and all Pods can communicate directly without NAT. CNI plugins (Calico, Flannel, Weave) implement this.

#### Q32. What is CNI in Kubernetes?
**Answer:** CNI (Container Network Interface) is a standard that defines how networking plugins should integrate with Kubernetes.

#### Q33. What are Network Policies?
**Answer:** Network Policies control traffic between Pods and/or external endpoints. They work like firewalls inside the cluster.

#### Q34. What is RBAC in Kubernetes?
**Answer:** RBAC (Role-Based Access Control) defines permissions for users and service accounts.

#### Q35. What are Service Accounts in Kubernetes?
**Answer:** Service Accounts are identities used by Pods to access the Kubernetes API or external services.

#### Q36. What is Helm in Kubernetes?
**Answer:** Helm is a package manager for Kubernetes. It uses charts (templates + configs) to deploy applications easily.

#### Q37. What is the difference between StatefulSet and Deployment?
**Answer:**
- **Deployment:** For stateless applications. Pods are interchangeable
- **StatefulSet:** For stateful apps. Provides stable network identity and persistent storage

#### Q38. What is Ingress in Kubernetes?
**Answer:** Ingress manages external HTTP/HTTPS access to Services. It provides routing, SSL termination, and load balancing.

#### Q39. What are PV and PVC in Kubernetes?
**Answer:**
- **Persistent Volume (PV):** A storage resource in the cluster
- **Persistent Volume Claim (PVC):** A request for storage by a Pod

#### Q40. What is CSI in Kubernetes?
**Answer:** CSI (Container Storage Interface) is a standard for integrating storage systems with Kubernetes.

#### Q41. What is the difference between configMap and env variables in Pods?
**Answer:** ConfigMaps can inject environment variables, mount as volumes, or be read at runtime. Env vars are static values defined in Pod specs.

#### Q42. How does Kubernetes ensure high availability of the control plane?
**Answer:** By running multiple replicas of control plane components (API server, etcd, controller-manager, scheduler) across nodes.

#### Q43. What is a sidecar container?
**Answer:** A sidecar container runs alongside the main app container in the same Pod, adding features like logging, monitoring, or proxying.

#### Q44. What is a service mesh?
**Answer:** A service mesh (e.g., Istio, Linkerd) manages service-to-service communication, traffic routing, security, and observability.

#### Q45. How does Kubernetes handle secrets security?
**Answer:** Secrets are base64-encoded and stored in etcd. For better security, you can enable encryption at rest and integrate with external secret stores (Vault, AWS KMS).

#### Q46. What is the difference between ClusterIP, NodePort, and LoadBalancer?
**Answer:**
- **ClusterIP:** Internal access only
- **NodePort:** Exposes service on each node's IP and a static port
- **LoadBalancer:** Integrates with cloud LB to expose service externally

#### Q47. How does Kubernetes perform pod scheduling?
**Answer:** Scheduler assigns Pods to nodes based on:
- Resource requests/limits
- Affinity/anti-affinity
- Taints and tolerations
- Custom scheduling policies

#### Q48. What is kubectl and what are some common commands?
**Answer:** kubectl is the CLI to interact with Kubernetes API.

**Common commands:**
- `kubectl get pods` → List Pods
- `kubectl describe pod <pod>` → Pod details
- `kubectl logs <pod>` → View logs
- `kubectl exec -it <pod> -- sh` → Enter Pod

#### Q49. What is the difference between declarative and imperative in Kubernetes?
**Answer:**
- **Imperative:** Direct commands (`kubectl run`, `kubectl expose`)
- **Declarative:** YAML manifests applied via `kubectl apply`

#### Q50. How do you upgrade a Kubernetes cluster?
**Answer:**
1. Upgrade control plane components
2. Upgrade kubelet and kube-proxy on nodes
3. Use tools like kubeadm or managed services (EKS, GKE, AKS) for upgrades

---

## Key Commands Reference

### Kubernetes Commands
```bash
# Pod Management
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl exec -it <pod-name> -- /bin/sh
kubectl delete pod <pod-name>

# Deployment Management
kubectl get deployments
kubectl create deployment <name> --image=<image>
kubectl scale deployment <name> --replicas=3
kubectl rollout status deployment <name>
kubectl rollout history deployment <name>

# Service Management
kubectl get services
kubectl expose deployment <name> --type=LoadBalancer --port=80
kubectl port-forward service/<name> 8080:80

# Configuration
kubectl apply -f <file.yaml>
kubectl get configmaps
kubectl get secrets
```

### Docker Commands
```bash
# Build and Push
docker build -t <image-name> .
docker tag <image> <registry>/<image>:<tag>
docker push <registry>/<image>:<tag>

# Container Management
docker run -d --name <container> <image>
docker ps
docker logs <container>
docker exec -it <container> /bin/sh
```

### Helm Commands
```bash
# Chart Management
helm create <chart-name>
helm install <release-name> <chart-path>
helm upgrade <release-name> <chart-path>
helm uninstall <release-name>
helm list
```

### AWS CLI Commands
```bash
# ECR
aws ecr get-login-password --region <region> | docker login --username AWS --password-stdin <account>.dkr.ecr.<region>.amazonaws.com

# EKS
aws eks update-kubeconfig --region <region> --name <cluster-name>

# ECS
aws ecs execute-command --cluster <cluster> --task <task> --container <container> --command "/bin/sh"
```

---

**Good luck with your interview! 🚀**