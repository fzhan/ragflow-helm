# RAGFlow Helm Chart - Quick HOWTO

This guide provides quick instructions for common operations with the RAGFlow Helm chart.

## Initial Setup

```bash
# Clone repository
git clone <your-repo-url>
cd <repo-directory>/ragflow

# Create charts directory
mkdir -p charts

# Update dependencies (downloads Milvus chart)
helm dependency update
```

## Basic Operations

### Installation

```bash
# Basic installation
helm install ragflow .

# With custom values file
helm install ragflow . -f my-values.yaml
```

### Check Status

```bash
# Check deployment status
helm status ragflow

# List all releases
helm list

# Get RAGFlow pods
kubectl get pods -l app.kubernetes.io/instance=ragflow
```

### Upgrading

```bash
# Update configuration
helm upgrade ragflow . --set replicaCount=2

# Upgrade with new values file
helm upgrade ragflow . -f new-values.yaml
```

### Rollback

```bash
# List revisions
helm history ragflow

# Rollback to previous revision
helm rollback ragflow 1
```

### Uninstall

```bash
# Remove RAGFlow deployment
helm uninstall ragflow
```

## Common Configurations

### Use Local LLM

```bash
helm install ragflow . \
  --set llm.provider=local \
  --set llm.model="llama2-7b" \
  --set resources.limits.memory=8Gi
```

### Configure OpenAI

```bash
helm install ragflow . \
  --set llm.provider=openai \
  --set llm.model=gpt-4 \
  --set-string llm.apiKey=your-api-key
```

### Expose with Ingress

```bash
helm install ragflow . \
  --set ingress.enabled=true \
  --set ingress.hosts[0].host=ragflow.example.com \
  --set "ingress.annotations.kubernetes\.io/ingress\.class=nginx"
```

### Use External Milvus

```bash
helm install ragflow . \
  --set milvus.enabled=false \
  --set config.milvusHost=milvus-service \
  --set config.milvusPort=19530
```

## Access RAGFlow

### Port Forward (ClusterIP service)

```bash
kubectl port-forward svc/ragflow 8000:8000
```

Now visit: http://localhost:8000

### Get LoadBalancer IP (if applicable)

```bash
kubectl get svc ragflow -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

## Troubleshooting

### View Logs

```bash
# Get pod name
kubectl get pods -l app.kubernetes.io/name=ragflow

# View logs
kubectl logs <pod-name>

# Stream logs
kubectl logs -f <pod-name>
```

### Debug Configuration

```bash
# View applied values
helm get values ragflow

# View all values (includes defaults)
helm get values ragflow -a
```

### Check ConfigMap and Secrets

```bash
kubectl get configmap ragflow-config -o yaml
kubectl get secret ragflow-secrets -o yaml
```
