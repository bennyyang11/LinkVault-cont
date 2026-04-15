---
title: Helm Chart Values Reference
visible_when:
  entitlements:
    - isHelmInstallEnabled
---

# Helm Chart Values Reference

Complete reference for configurable Helm values in the LinkVault chart. Pass these values using `--set key=value` or in a custom `values.yaml` file.

## Application

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| `replicaCount` | integer | `1` | Number of LinkVault application replicas |
| `image.repository` | string | *(required)* | Container image repository for the LinkVault application |
| `image.tag` | string | *(required)* | Container image tag |
| `image.pullPolicy` | string | `IfNotPresent` | Image pull policy. One of: `Always`, `IfNotPresent`, `Never` |

## Service

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| `service.type` | string | `NodePort` | Kubernetes Service type. One of: `ClusterIP`, `NodePort`, `LoadBalancer` |
| `service.port` | integer | `8080` | Service port the application listens on |
| `service.nodePort` | integer | `30080` | NodePort value when `service.type=NodePort`. Must be in range 30000-32767 |

## Ingress

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| `ingress.enabled` | boolean | `false` | Enable Kubernetes Ingress resource for external HTTP(S) access |
| `ingress.host` | string | `""` | Hostname for the Ingress rule. Example: `linkvault.example.com` |
| `ingress.className` | string | `nginx` | IngressClass name. Must match an IngressClass installed on the cluster |
| `ingress.tls.enabled` | boolean | `false` | Enable TLS termination on the Ingress |
| `ingress.tls.certManager.enabled` | boolean | `false` | Use cert-manager to automatically provision a TLS certificate |
| `ingress.tls.certManager.issuerName` | string | `letsencrypt-prod` | cert-manager Issuer or ClusterIssuer name |
| `ingress.tls.certManager.issuerKind` | string | `ClusterIssuer` | cert-manager issuer kind: `Issuer` or `ClusterIssuer` |
| `ingress.tls.secretName` | string | `""` | Name of an existing TLS Secret. Use this to provide your own certificate |
| `ingress.tls.selfSigned` | boolean | `false` | Generate a self-signed certificate (for testing only) |

## Database (Embedded PostgreSQL)

By default, LinkVault deploys an embedded PostgreSQL instance using the Bitnami subchart. Set `postgresql.enabled=false` and configure `externalPostgresql` to use your own database.

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| `postgresql.enabled` | boolean | `true` | Deploy the embedded PostgreSQL subchart |
| `postgresql.auth.postgresPassword` | string | `linkvault-default-pw` | Password for the embedded PostgreSQL `postgres` user |
| `postgresql.auth.database` | string | `linkvault` | Database name to create |
| `postgresql.primary.persistence.size` | string | `1Gi` | PVC size for PostgreSQL data |

## Database (External PostgreSQL)

Configure these values when `postgresql.enabled=false` to connect to an external PostgreSQL instance.

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| `externalPostgresql.host` | string | `""` | Hostname or IP of the external PostgreSQL server |
| `externalPostgresql.port` | string | `"5432"` | TCP port for the external PostgreSQL server |
| `externalPostgresql.user` | string | `"postgres"` | Username for PostgreSQL authentication. Must have CREATE TABLE and DML privileges |
| `externalPostgresql.password` | string | `""` | Password for PostgreSQL authentication |
| `externalPostgresql.database` | string | `"linkvault"` | Database name. The database must already exist |

## Cache (Embedded Redis)

By default, LinkVault deploys an embedded Redis instance. Set `redis.enabled=false` and configure `externalRedis` to use your own Redis, or disable caching entirely.

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| `redis.enabled` | boolean | `true` | Deploy the embedded Redis subchart |
| `redis.architecture` | string | `standalone` | Redis architecture. Only `standalone` is supported |
| `redis.auth.enabled` | boolean | `false` | Enable Redis authentication |

## Cache (External Redis)

Configure these values when `redis.enabled=false` to connect to an external Redis instance.

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| `externalRedis.host` | string | `""` | Hostname or IP of the external Redis server |
| `externalRedis.port` | string | `"6379"` | TCP port for the external Redis server |
| `externalRedis.password` | string | `""` | Password for Redis authentication. Leave blank if auth is not required |
