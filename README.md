# RAGFlow Helm Chart

This Helm chart deploys [RAGFlow](https://github.com/infiniflow/ragflow), an open-source RAG (Retrieval-Augmented Generation) engine based on deep document understanding, which is licensed under the [Apache License 2.0](https://github.com/infiniflow/ragflow/blob/main/LICENSE).

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- PV provisioner support in the underlying infrastructure (if persistence is enabled)

## Installing the Chart

### 1. Clone this repository

```bash
git clone <your-repo-url>
cd <repo-directory>/ragflow
```

### 2. Update dependencies

Before installing the chart, you need to update the dependencies:

```bash
# Create charts directory if it doesn't exist
mkdir -p charts

# Update dependencies
helm dependency update
```

This will download the Milvus chart into the `charts/` directory.

### 3. Install the chart

```bash
# Install with default configuration
helm install ragflow .

# Or with custom values file
helm install ragflow . -f values.yaml

# Or override specific values
helm install ragflow . --set replicaCount=2 --set persistence.size=20Gi
```

## Configuration

The following table lists the configurable parameters for the RAGFlow chart and their default values.

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount` | Number of RAGFlow replicas | `1` |
| `image.repository` | RAGFlow image repository | `infiniflow/ragflow` |
| `image.tag` | RAGFlow image tag | `latest` |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `persistence.enabled` | Enable persistence | `true` |
| `persistence.size` | Size of persistent volume | `10Gi` |
| `persistence.storageClass` | Storage class for PVC | `""` (use default) |
| `config.logLevel` | Log level | `info` |
| `config.embeddingModel` | Embedding model to use | `BAAI/bge-small-en-v1.5` |
| `config.maxDocumentSize` | Maximum document size in bytes | `10485760` (10MB) |
| `llm.provider` | LLM provider (openai, azure, local) | `openai` |
| `llm.model` | LLM model to use | `gpt-3.5-turbo` |
| `llm.apiKey` | LLM API key | `""` |
| `milvus.enabled` | Deploy Milvus as dependency | `true` |
| `resources` | CPU/Memory resource requests/limits | See `values.yaml` |
| `service.type` | Kubernetes service type | `ClusterIP` |
| `service.port` | Service port | `8000` |
| `ingress.enabled` | Enable ingress | `false` |

## Common Usage Examples

### Deploying with External Milvus

```bash
helm install ragflow . \
  --set milvus.enabled=false \
  --set externalMilvus.host=my-milvus-service \
  --set externalMilvus.port=19530
```

### Using a Different LLM Provider

```bash
helm install ragflow . \
  --set llm.provider=azure \
  --set llm.model=gpt-4 \
  --set-string llm.apiKey=your-api-key
```

### Enabling Ingress

```bash
helm install ragflow . \
  --set ingress.enabled=true \
  --set ingress.hosts[0].host=ragflow.example.com \
  --set ingress.hosts[0].paths[0].path=/ \
  --set ingress.hosts[0].paths[0].pathType=Prefix
```

## Upgrading the Chart

```bash
helm upgrade ragflow . --set replicaCount=3
```

## Uninstalling the Chart

```bash
helm uninstall ragflow
```

## Persistence

This chart mounts a Persistent Volume for storing RAGFlow data. By default, the chart creates a PersistentVolumeClaim with `ReadWriteOnce` access mode. If persistence is disabled, an emptyDir volume is used.

## Troubleshooting

### Milvus Dependency Error

If you see an error like:
```
Error: found in Chart.yaml, but missing in charts/ directory: milvus
```

Run the following command to update dependencies:
```bash
helm dependency update
```

### Checking Logs

```bash
# Get pod name
kubectl get pods -l app.kubernetes.io/name=ragflow

# View logs
kubectl logs <pod-name>
```

## Security Notes

- API keys are stored as Kubernetes secrets
- For production use, consider using a secrets management solution like HashiCorp Vault
- Set resource limits appropriate to your workload to prevent resource exhaustion

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## Attribution

This Helm chart is provided to deploy [RAGFlow](https://github.com/infiniflow/ragflow).
RAGFlow is developed by InfiniFlow and licensed under the Apache License 2.0.

The Helm chart itself is also licensed under the Apache License 2.0.

This is not an official product of InfiniFlow.
