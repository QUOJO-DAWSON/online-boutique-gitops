# Online Boutique GitOps

This repository contains the GitOps configuration for deploying the Online Boutique microservices application to Kubernetes using Kustomize.

## Overview

Online Boutique is a cloud-native microservices demo application. The application is a web-based e-commerce app where users can browse items, add them to the cart, and purchase them.

This repository follows GitOps principles, where the desired state of the Kubernetes infrastructure is stored in Git and automatically synchronized with the cluster.

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
│   └── kustomization.yaml
└── overlays/                                   # Environment-specific configurations
    └── dev/                                    # Development environment
        └── kustomization.yaml
```

## CI/CD Automation

This repository includes GitHub Actions workflows that automate the deployment process:

- **Update Product Catalog Service Tag**: Automatically updates the image tag for the product catalog service when triggered by a repository dispatch event. The workflow is defined in `.github/workflows/update-productcatalogue-tag.yaml`.

## Usage

### Prerequisites

- Kubernetes cluster
- kubectl installed and configured
- Kustomize installed

### Deploying to Development Environment

```bash
kubectl apply -k overlays/dev
```

### Deploying Cluster Resources

```bash
kubectl apply -k cluster-resources
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
  - **Istio Gateway**: Configured in `cluster-resources/istio/gateway.yaml` to handle ingress traffic
  - **Virtual Service**: Routes external traffic to the frontend service
  - **Peer Authentication**: Enforces mutual TLS (mTLS) between services for secure communication

## Contributing

1. Create a new branch for your changes
2. Make your changes and test them
3. Submit a pull request

## License

See the [LICENSE](LICENSE) file for details.