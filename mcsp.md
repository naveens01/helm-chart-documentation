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


IAM Policy




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
