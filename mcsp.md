# MCSP Setup - API Control Plane

## Cluster Information

- **Boundary Control Plane**
- **Regional Control Plane**
- **Dataplane**

---
> [!IMPORTANT]
> APICP proceeding with opensearch [ 2.18 ] for mcsp


### Architecture Diagram

Refer to the architecture diagram here: [API Control Plane Deployment Architecture](https://github.ibm.com/Cloud-Integration/ibm-ipaas-architecture/blob/feature/arch-84/docs/architecture-diagrams/api-management-saas/api-control-plane/images/api-control-plane-deployment-arch.svg)

## Infrastructure Components

### IAM Roles

#### Role: Control Plane OpenSearch Access

Ensure that the **OIDC provider ID** of the **Dataplane** (where OpenSearch runs) is added to the trust policy of the control plane role.

Example Trust Relationship:

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

#### Policy

Example Policy:


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
                "arn:aws:s3:::<bucket-name>"
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
                "arn:aws:s3:::<bucket-name>/*"
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


###Secrets

| Secret Name                               | Value / Description                                      |
|--------------------------------|-----------------------------------------------------------------|
| **KC_TMS_POWER_CLIENT_ID**            | `ipaas-b0003-h01-apicp-tms-power-user`                        |
| **KC_TMS_POWER_CLIENT_SECRET**    | `xxxxxxxxxx` (generated via script )                   |
| **IPAAS_PLATFORM_AUTH_TOKEN_CLIENT_ID**    | `provisioning-callbacks`                                      |
| **IPAAS_PLATFORM_AUTH_TOKEN_CLIENT_SECRET** | _Obtain from the Platform Team_                     |
| **IPAAS_PLATFORM_MTLS_KEYSTORE_PASS**  | _Steps to prepare this will be shared separately_ |
| **OAUTH_API_KEYS**                             | _Product team will share to you_ |
| **OAUTH_API_SECRET** | _Product team will share to you_ |

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
Note:  The tenant runtimes are provisoned via TM and TMs

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

# Tenant URL

The Tenant URL follows a standardized format to ensure consistency across different environments. Below is the general structure:


**Format** -   ``` *.<cloudProvider>-<region>-<dp-id>.<capability>.ipaas.<env-id>.automation.ibm.com ```

- **Build Environment:**  
    ```
    buildtenant01.a-vir-d1.apicp.ipaas.dev.automation.ibm.com
    ```
- **Test Environment:**  
    ```
    testtenant01.a-vir-d1.apicp.ipaas.test.automation.ibm.com
    ```


## Tenant Tier 

### **Standard Tier**  
The `ipaas_standard` tier is equivalent to the **paid** tier.

## OCI Activity

### Pushing to Non-Prod Registry (by DevOps)

To push the Helm chart to the **non-prod** OCI registry, DevOps uses the following target:

```sh
oci://icr.io/ipaas-non-prod/wm-apicp/charts
```
###  Promoting Chart to Prod Registry (by CloudOps)
```sh
helm pull oci://icr.io/ipaas-non-prod/wm-apicp/charts --version <chart-version>
```

```sh
helm push ctrlplane-<chart-version>.tgz oci://icr.io/ipaas-prod/wm-apicp/charts
```

### Important Note
Whenever the chart version is updated in the **Helm chart's** `Chart.yaml` file, make sure to also update the corresponding chart version in tenant-manager.yaml in ipaas-argo
[**tenant-manager.yaml**](https://github.ibm.com/automation-paas-cd-pipeline/ipaas-argocd/blob/main/values/wm-apicp-stack/aws-dev/tenant-manager.yaml#L25)


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
