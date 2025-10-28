# ⚠️ REPOSITÓRIO MOVIDO

Este repositório foi **consolidado** no repositório principal:

👉 **https://github.com/softhill/k8s-deploy-softhill**

## 📦 Nova Estrutura

Todos os clientes Odoo da Softhill agora compartilham o mesmo repositório de deploy:

```
k8s-deploy-softhill/
├── clients/
│   ├── multimodas/
│   │   ├── values-dev.yaml
│   │   ├── values-hml.yaml
│   │   └── values-prod.yaml
│   └── racco/
│       ├── values-dev.yaml
│       ├── values-hml.yaml
│       └── values-prod.yaml
└── .github/workflows/
    ├── deploy-multimodas.yml
    └── deploy-racco.yml
```

## 🚀 Novo Workflow

Workflows separados por cliente garantem deploys isolados e eficientes.

---

**Data da movimentação:** 28/10/2025  
**Por:** Gabriel Cardoso de Faria - CTO Softhill
