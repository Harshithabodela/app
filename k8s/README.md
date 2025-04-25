
# ArgoCD Setup on Minikube - Unique Presentation

Welcome to this guide where we will walk through the steps of setting up ArgoCD on Minikube and deploying applications using GitOps.

## 🚀 Prerequisites

To get started, ensure that the following tools are installed on your system:

- **Minikube**: Run a Kubernetes cluster locally.
- **kubectl**: Command-line tool to manage Kubernetes clusters.
- **ArgoCD CLI**: Command-line interface to interact with ArgoCD.
- **Docker**: Required for Minikube when using the Docker driver.
- **Git**: For managing your Kubernetes manifests.

## 🛠 Step 1: Start Minikube

First, let's spin up a Kubernetes cluster using Minikube. Run the following command:

```bash
minikube start
```

This will start a Kubernetes cluster locally using the Docker driver. It may take a few minutes.

## 🧰 Step 2: Set kubectl to Use Minikube's Kubernetes Cluster

Now, configure kubectl to use Minikube's cluster:

```bash
kubectl config use-context minikube
```

Verify everything is working fine by checking the cluster information:

```bash
kubectl cluster-info
```

## 💡 Step 3: Install ArgoCD

Install ArgoCD on your Minikube cluster by applying the manifest:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

This will deploy ArgoCD and its resources on your cluster.

## 🔐 Step 4: Expose ArgoCD API Server

To access ArgoCD's web UI, forward the port to your localhost:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Once it's running, you can access the ArgoCD UI at `https://localhost:8080`.

## 🔑 Step 5: Log in to ArgoCD

Use the ArgoCD CLI to log in:

```bash
argocd login localhost:8080
```

The default username is `admin`. To get the password, use:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath='{.data.password}' | base64 -d
```

## 📝 Step 6: Create an Application in ArgoCD

Now that you're logged in, create an application in ArgoCD using either the UI or the CLI. Here’s how to do it using the CLI:

```bash
argocd app create demo-app   --repo <repository-url>   --path <path-to-kubernetes-manifests>   --dest-server https://kubernetes.default.svc   --dest-namespace default
```

Make sure to replace `<repository-url>` and `<path-to-kubernetes-manifests>` with the correct values for your setup.

## 🔄 Step 7: Sync the Application

Sync the application to deploy your Kubernetes resources:

```bash
argocd app sync demo-app
```

Check the status of your application:

```bash
argocd app get demo-app
```

## 🔧 Step 8: Monitor and Update the Application

Monitor the deployment using the ArgoCD UI or CLI. If changes are made to the Git repository, ArgoCD will auto-sync by default.

Manually trigger a sync whenever you want:

```bash
argocd app sync demo-app
```

## 🧹 Step 9: Clean Up

If you want to remove ArgoCD and all related resources from your Minikube cluster, delete the ArgoCD namespace:

```bash
kubectl delete namespace argocd
```

---

## 🎉 Conclusion

You’ve successfully set up ArgoCD on Minikube and deployed applications using GitOps! Now, you can easily manage and monitor your applications directly from a Git repository.

## 🔗 Helpful Links

- [ArgoCD Documentation](https://argo-cd.readthedocs.io/)
- [Minikube Documentation](https://minikube.sigs.k8s.io/docs/)
