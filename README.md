# Online Boutique GitOps

This repository contains the GitOps configuration for deploying the Online Boutique microservices application to Kubernetes using Kustomize.

## Overview

Online Boutique is a cloud-native microservices demo application. The application is a web-based e-commerce app where users can browse items, add them to the cart, and purchase them.

This repository follows GitOps principles, where the desired state of the Kubernetes infrastructure is stored in Git and automatically synchronized with the cluster using declarative configurations.

## Tools and Technologies

- **Kubernetes**: Container orchestration platform
- **Kustomize**: Kubernetes configuration management
- **ArgoCD**: GitOps continuous delivery tool
- **Istio**: Service mesh for microservices
- **Prometheus, Grafana & AlertManager**: Monitoring, observability, and alerting stack
- **External Secrets Operator**: Secure secret management
- **AWS Secrets Manager**: Cloud-based secrets storage
- **GitHub Actions**: CI/CD automation
- **Docker**: Container runtime

## Features

- **GitOps-based Deployment**: Infrastructure as code with Git as the single source of truth
- **Kustomize Integration**: Layered configuration management for different environments
- **Service Mesh**: Istio integration for traffic management, security, and observability
- **Monitoring & Alerting**: Prometheus ServiceMonitor for Istio metrics collection with AlertManager for notifications
- **Secure Secret Management**: External Secrets Operator integration with AWS Secrets Manager
- **Automated CI/CD**: GitHub Actions workflows for automated image updates
- **mTLS Security**: Service-to-service encryption with Istio Peer Authentication

## Repository Structure

```
.
├── .github/                                    # GitHub configuration
│   └── workflows/                              # GitHub Actions workflows
│       └── update-productcatalogue-tag.yaml    # Workflow to update product catalog service image tag
├── app-config/                                 # Application-specific configurations
│   ├── frontend-virtual-service.yaml           # Istio VirtualService for routing to frontend
│   ├── kustomization.yaml
│   └── stripe-external-secret.yaml             # External Secret for Stripe API key
├── base/                                       # Base Kubernetes manifests for all microservices
│   ├── adservice.yaml
│   ├── cartservice.yaml
│   ├── checkoutservice.yaml
│   ├── currencyservice.yaml
│   ├── emailservice.yaml
│   ├── frontend.yaml
│   ├── kustomization.yaml
│   ├── paymentservice.yaml
│   ├── productcatalogservice.yaml
│   ├── recommendationservice.yaml
│   ├── redis-cart.yaml
│   └── shippingservice.yaml
├── cluster-resources/                          # Cluster-wide resources
│   ├── external-secret/                        # External Secrets configuration
│   │   ├── aws-secret-store.yaml               # AWS Secrets Manager configuration
│   │   └── kustomization.yaml
│   ├── istio/                                  # Istio service mesh configuration
│   │   ├── gateway.yaml                        # Istio Gateway for ingress traffic
│   │   ├── istio-external-secret.yaml          # TLS certificate for Istio Gateway
│   │   ├── kustomization.yaml
│   │   └── mesh-peer-authentication.yaml       # mTLS configuration for service-to-service communication
│   ├── prometheus-grafana/                     # Prometheus monitoring configuration
│   │   ├── istio-servicemonitor.yaml            # ServiceMonitor for Istio metrics
│   │   └── kustomization.yaml
│   └── kustomization.yaml
└── overlays/                                   # Environment-specific configurations
    └── dev/                                    # Development environment
        └── kustomization.yaml
```

## CI/CD Automation

This repository includes GitHub Actions workflows that automate the deployment process and maintain the GitOps workflow:

- **Update Product Catalog Service Tag**: Automatically updates the image tag for the product catalog service when triggered by a repository dispatch event. The workflow is defined in `.github/workflows/update-productcatalogue-tag.yaml`.

### Required GitHub Secrets

The following secrets need to be configured in the GitHub repository settings:

- `PAT_TOKEN`: Personal Access Token with repository write permissions
- `USER_EMAIL`: Email address for Git commits
- `USER_NAME`: Username for Git commits

## Usage

### Prerequisites

- Kubernetes cluster with Istio installed
- kubectl installed and configured (includes Kustomize)
- ArgoCD installed in the cluster
- External Secrets Operator installed in the cluster

### Deployment Order

1. First, deploy the cluster-wide resources:

```bash
kubectl apply -k cluster-resources
```

2. Then, deploy the application to the development environment:

```bash
kubectl apply -k overlays/dev
```

## Microservices

The application consists of the following microservices:

- **Frontend**: Web UI
- **Product Catalog Service**: Provides the list of products
- **Currency Service**: Converts prices to different currencies
- **Cart Service**: Stores items in the user's shopping cart
- **Recommendation Service**: Recommends products to users
- **Shipping Service**: Calculates shipping costs
- **Checkout Service**: Manages the checkout process
- **Payment Service**: Processes payments
- **Email Service**: Sends confirmation emails
- **Ad Service**: Provides advertisements
- **Redis**: Used as a cache for the cart service

## External Dependencies

- **External Secrets Operator**: Used to securely manage secrets
  - **AWS Secret Store**: Configured to fetch secrets from AWS Secrets Manager
  - **Stripe API Key**: External secret for payment processing
  - **TLS Certificate**: External secret for Istio Gateway TLS termination
- **Istio**: Service mesh for traffic management and security
  - **Istio Gateway**: Configured in `cluster-resources/istio/gateway.yaml` to handle ingress traffic with TLS termination
  - **Virtual Service**: Routes external traffic to the frontend service in the `online-boutique` namespace
  - **Peer Authentication**: Enforces mutual TLS (mTLS) between services for secure communication
- **Prometheus Monitoring**: Observability, metrics collection, and alerting
  - **ServiceMonitor**: Configured in `cluster-resources/prometheus-grafana/istio-servicemonitor.yaml` to collect Istio control plane metrics
  - **AlertManager**: Bundled with Prometheus stack for alert routing and notifications

## Contributing

1. Create a new branch for your changes
2. Make your changes and test them
3. Submit a pull request

## License

See the [LICENSE](LICENSE) file for details.