# MCSP Setup - API Control Plane

## Overview

This document outlines the technical setup process for the **MCSP API Control Plane**, covering the boundary, regional, and data planes, along with required infrastructure components and configurations.

---

## Cluster Information

- **Boundary Control Plane**
- **Regional Control Plane**
- **Dataplane**

---

## Infrastructure Components

### IAM Roles

#### Role: Control Plane OpenSearch Access

Ensure that the **OIDC provider ID** of the **Dataplane** (where OpenSearch runs) is added to the trust policy of the control plane role.

Example Trust Policy:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Federated": "arn:aws:iam::904233089553:oidc-provider/oidc.op1.openshiftapps.com/<dataplane-oidc-id>"
            },
            "Action": "sts:AssumeRoleWithWebIdentity",
            "Condition": {
                "StringLike": {
                    "oidc.op1.openshiftapps.com/<dataplane-oidc-id>:sub": "system:serviceaccount:cplane-*:*"
                }
            }
        }
    ]
}

```
IAM Policy
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "s3:ListBucketVersions",
                "s3:ListBucket",
                "s3:GetBucketAcl",
                "s3:GetBucketLocation"
            ],
            "Resource": [
                "arn:aws:s3:::ipaas-b0003-wm-apicp"
            ]
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:GetObjectAcl",
                "s3:GetObject",
                "s3:DeleteObject"
            ],
            "Resource": [
                "arn:aws:s3:::ipaas-b0003-wm-apicp/*"
            ]
        }
    ]
}
```



## Secret Manager Configuration

| Plane                  | Secret Path                                                |
|-----------------------|-------------------------------------------------|
| Dataplane               | `ipaas-b0003-d01/wm-apicp/replicate-secret-spoke` |
| Regional Control Plane  | `ipaas-b0003-h01/wm-apicp/replicate-secret`     |

Refer to:  
[MCSP Resources - Secrets Convention](https://github.com/ibm-webmethods/kub-helm-charts-common/wiki/MCSP-Resources#secrets-convention-in-aws-secrets-manager)

---

## Endpoint Service - S3

- Must be created for the **Dataplane**.
- This will be a **Gateway Endpoint**.

---

## Private Domain Configuration

- Add required DNS entries to the **Private Hosted Zone**.
- Follow naming conventions from:  
[MCSP Environments - Domain Pattern](https://github.com/ibm-webmethods/kub-helm-charts-common/wiki/MCSP-Environments#domain-pattern-for-private-hosted-zone)

---

## ArgoCD Applications

The following applications will be deployed via ArgoCD:

- `ctrlplane-secrets`
- `ctrlplane-storage`
- `ctrlplane-monitoring`
- Ingress Controller
- Tenant Manager (TM)
- Tenant Management System (TMS)

---

## Container Registry (ICR)

- Images are pulled from **IBM Container Registry (ICR)**.
- Two registries are used:
    - **Non-Production:** `icr.io/ipaas-non-prod`
    - **Production:** `icr.io/ipaas-prod`

### Example Repository

```bash
icr.io/ipaas-non-prod/wm-apicp/assetcatalog
```

# OCI

The `ctrlplane` chart is pushed to the OCI repository.

---

# DNS and TLS Management

- DNS is managed via **Cloud Internet Services (CIS)**.
- Certificate Management is automated.
- TLS Certificates follow conventions outlined in:  
  [MCSP Environments - TLS Certificates](https://github.com/ibm-webmethods/kub-helm-charts-common/wiki/MCSP-Environments#tls-certificates)

---

# Internal Application Authentication

- Authentication for internal components such as **TM**, **TMS**, and **IPAAS platform** is handled via **MCSP Token**.

### Example Token Request

```sh
curl -X POST 'https://account-iam.platform.test.saas.ibm.com/api/2.0/accounts/<INTERNAL-ACCOUNT-ID>/apikeys/token' \
-H 'accept: application/json' \
-H 'Mcsp-ApiKey: <MCSP-API-KEY>' \
-H 'Content-Type: application/json'
```

# Monitoring Setup

- In OpenShift, **ServiceMonitor** resources have been moved to tenant-specific configurations.
- **Prometheus Agent** is used for monitoring.

---

# Directory for Setting Values

- Refer to the **Ipaas-argo** repository.

---

# Publish Image in TMS

- Append the **image digest** when publishing the image in **TMS**.

For details, refer to:  
[kub-wmio-tms/docs/API.md at develop](https://github.com/ibm-webmethods/kub-wmio-tms/blob/develop/docs/API.md#publish-release)

---

# Post-Setup Activities

## 1. Validate Monitoring Setup

- Confirm that monitoring components are correctly collecting metrics.
- Verify **Prometheus** scrape targets and alerts.

## 2. Validate Log Collection

- Ensure logs from all components are visible in **Grafana** (Loki datasource).
- Confirm logs are being collected with correct labels:
    - `namespace`
    - `pod`
    - `container`
