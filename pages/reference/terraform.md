---
title: Terraform Module
---

# Terraform Module

Deploy LinkVault to a Kubernetes cluster using the official Terraform module. This module wraps the LinkVault Helm chart and provides a declarative way to manage your deployment.

## Requirements

| Name | Version |
|------|---------|
| terraform | >= 1.5.0 |
| helm | >= 2.12.0 |
| kubernetes | >= 2.25.0 |

## Usage

```hcl
module "linkvault" {
  source = "registry.terraform.io/linkvault/linkvault/kubernetes"
  version = "3.1.0"

  namespace        = "linkvault"
  create_namespace = true

  # License
  license_id    = var.replicated_license_id
  license_token = var.replicated_license_token

  # Database
  database_mode = "embedded"
  # For external PostgreSQL:
  # database_mode     = "external"
  # database_host     = "db.example.com"
  # database_port     = 5432
  # database_user     = "linkvault"
  # database_password = var.db_password
  # database_name     = "linkvault"

  # Cache
  cache_mode = "embedded"

  # Service
  service_type = "ClusterIP"
  service_port = 8080

  # Ingress
  ingress_enabled = true
  ingress_host    = "linkvault.example.com"
  ingress_tls     = true
}
```

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|----------|
| `namespace` | Kubernetes namespace to deploy into | `string` | `"default"` | no |
| `create_namespace` | Create the namespace if it does not exist | `bool` | `false` | no |
| `license_id` | Replicated license ID for registry authentication | `string` | — | yes |
| `license_token` | Replicated license token for registry authentication | `string` | — | yes |
| `database_mode` | Database mode: `embedded` or `external` | `string` | `"embedded"` | no |
| `database_host` | External PostgreSQL hostname (required when `database_mode = "external"`) | `string` | `""` | no |
| `database_port` | External PostgreSQL port | `number` | `5432` | no |
| `database_user` | External PostgreSQL username | `string` | `"postgres"` | no |
| `database_password` | External PostgreSQL password | `string` | `""` | no |
| `database_name` | External PostgreSQL database name | `string` | `"linkvault"` | no |
| `cache_mode` | Cache mode: `embedded`, `external`, or `disabled` | `string` | `"embedded"` | no |
| `service_type` | Kubernetes Service type: `ClusterIP`, `NodePort`, or `LoadBalancer` | `string` | `"ClusterIP"` | no |
| `service_port` | Service port | `number` | `8080` | no |
| `ingress_enabled` | Enable Kubernetes Ingress | `bool` | `false` | no |
| `ingress_host` | Ingress hostname | `string` | `""` | no |
| `ingress_tls` | Enable TLS on Ingress | `bool` | `false` | no |

## Outputs

| Name | Description |
|------|-------------|
| `namespace` | Namespace where LinkVault is deployed |
| `service_name` | Name of the LinkVault Kubernetes Service |
| `service_endpoint` | Internal cluster endpoint for the LinkVault service |
| `ingress_hostname` | Ingress hostname (if ingress is enabled) |

## Example: External Database with Ingress

```hcl
module "linkvault" {
  source  = "registry.terraform.io/linkvault/linkvault/kubernetes"
  version = "3.1.0"

  namespace        = "linkvault-prod"
  create_namespace = true

  license_id    = var.replicated_license_id
  license_token = var.replicated_license_token

  database_mode     = "external"
  database_host     = aws_db_instance.linkvault.address
  database_port     = aws_db_instance.linkvault.port
  database_user     = "linkvault"
  database_password = var.db_password
  database_name     = "linkvault"

  cache_mode = "embedded"

  service_type    = "ClusterIP"
  ingress_enabled = true
  ingress_host    = "bookmarks.${var.domain}"
  ingress_tls     = true
}
```
