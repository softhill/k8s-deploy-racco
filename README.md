# k8s-deploy-racco

Kubernetes deployment configurations for **Racco** Odoo 18 environments.

## 🏗️ Infraestrutura

- **Servidor:** Hetzner CX32 (94.130.171.114)
- **Cluster:** K3s v1.33.5+k3s1
- **Ambientes:** DEV, HML, PROD
- **Database:** PostgreSQL 18.0 (self-hosted)
- **Cache:** Valkey (Redis-compatible)
- **SSL:** Let's Encrypt via Cert-Manager
- **Ingress:** Traefik

## 📦 Ambientes

| Ambiente | Domínio | Namespace | Database | Recursos |
|----------|---------|-----------|----------|----------|
| DEV | dev.racco.softhill.com.br | `racco-dev` | `racco_dev` | 1 CPU, 2Gi RAM |
| HML | hml.racco.softhill.com.br | `racco-hml` | `racco_hml` | 2 CPU, 4Gi RAM |
| PROD | racco.softhill.com.br | `racco-prod` | `racco_prod` | 2 CPU, 4Gi RAM (HPA 2-6) |

## 🚀 Deploy Automático

O deploy é realizado automaticamente via GitHub Actions quando há push na branch `main`:

1. Deploy DEV
2. Deploy HML (após DEV bem-sucedido)
3. Deploy PROD (após HML bem-sucedido)

### Secrets Necessários no GitHub

```bash
# No repositório softhill/k8s-deploy-racco → Settings → Secrets
RACCO_SERVER_IP=94.130.171.114
SSH_PRIVATE_KEY=<chave privada SSH para root@94.130.171.114>
GH_TOKEN=<GitHub Personal Access Token>
```

## 🛠️ Deploy Manual

### Via SSH no servidor

```bash
# SSH no servidor Racco
ssh root@94.130.171.114

# Deploy DEV
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
cd /root/k8s-deploy-racco
git pull origin main

helm upgrade --install racco-dev ./charts/odoo-multi \
  --namespace racco-dev \
  -f clients/racco/values-dev.yaml \
  --wait --timeout 10m

kubectl rollout restart deployment/racco-dev-odoo-multi -n racco-dev

# Deploy HML
helm upgrade --install racco-hml ./charts/odoo-multi \
  --namespace racco-hml \
  -f clients/racco/values-hml.yaml \
  --wait --timeout 10m

kubectl rollout restart deployment/racco-hml-odoo-multi -n racco-hml

# Deploy PROD
helm upgrade --install racco-prod ./charts/odoo-multi \
  --namespace racco-prod \
  -f clients/racco/values-prod.yaml \
  --wait --timeout 10m

kubectl rollout restart deployment/racco-prod-odoo-multi -n racco-prod
```

## 🔍 Monitoramento

### Ver logs

```bash
# Logs do DEV
kubectl logs -f -n racco-dev -l app=racco-dev-odoo-multi

# Logs do HML
kubectl logs -f -n racco-hml -l app=racco-hml-odoo-multi

# Logs do PROD
kubectl logs -f -n racco-prod -l app=racco-prod-odoo-multi
```

### Ver pods

```bash
kubectl get pods -n racco-dev
kubectl get pods -n racco-hml
kubectl get pods -n racco-prod
```

### Ver status do Ingress

```bash
kubectl get ingress -n racco-dev
kubectl get ingress -n racco-hml
kubectl get ingress -n racco-prod
```

## 🔐 Secrets

Os secrets são gerenciados no Kubernetes e contêm:

- `odoo-db`: Senha do PostgreSQL
- `odoo-redis`: Senha do Valkey (Redis)
- `ghcr-cred`: Credenciais do GitHub Container Registry

## 🗄️ Backup

Os backups são realizados automaticamente via `rclone` para Backblaze B2:

- **Filestore (PVCs):** Diário às 2:00 AM
- **PostgreSQL:** Diário às 3:00 AM
- **Retenção:** 30 dias
- **Destino:** `rclone:softhill-backups/racco/`

## 📞 Contato

**Gabriel Cardoso de Faria**  
CTO e Co-fundador - Softhill  
gabriel.cardoso@softhill.com.br

