# 11.1.4 Rollout Steps

## Release Information

This release primarily includes changes related to the adoption of Cloudflare and Gainsight updates.

#### Release Tag

[11.1.4](https://github.webmethods.io/AIM/kub-helm-charts-ctrlplane/releases/tag/11.1.4)


#### Fix iTrac

[KUB-35109](https://icoitrac.eur.ad.sag/browse/KUB-36094)

## Pre-Rollout Steps

### Preparing Inventory

#### Updating the release tag

Update the helm branch reference in the remote inventories file.

###### inventory/classes/environments/prod/azure/westeurope/api_remote_inventories.yml
```yaml
parameters:
  ctrlplane_helm_branch: 11.1.4
```

Also update the correct reference in the target file of your environment.

> :information_source: The order/type of imports in your target file depends on your environment. Please update accordingly.

###### inventory/targets/azure/prod/api/api-prod-06-az-westeurope.yml

Classes:
```yaml
    - products.infrastructure.azure.ca3s-base.CHANGEME.0.19.5.stateful-base  #Update the tag to corresponding release of common charts. Example: 
    - products.infrastructure.azure.ca3s-base.CHANGEME.0.19.5.external-module-deps  #Update the tag to corresponding release of common charts. Example: 
    - products.infrastructure.azure.ca3s-base.CHANGEME.0.19.5.api-shared-external-module-deps  #Update the tag to corresponding release of common charts. Example:
    - products.infrastructure.azure.cplane-base.CHANGEME.0.19.5.stateful-base  #Update this with tag: 11.1.4
    - products.infrastructure.azure.cplane-base.CHANGEME.0.19.5.external-module-deps  #Update this with tag: 11.1.4
      ....
    - products.applications.common.CHANGEME.0.19.5.azure-applications  #Update the tag to corresponding release of common charts. Example: 
    - products.applications.common.CHANGEME.0.19.5.helm-values.common-azure-values-wrapper  #Update the tag to corresponding release of common charts. Example: 
    - products.applications.cplane.CHANGEME.0.19.5.applications  #Update this with tag: 11.1.4
    - products.applications.cplane.CHANGEME.0.19.5.helm-values.ctrlplane-azure-values-wrapper  #Update this with tag: 11.1.4
```

### Kapitan Compilation

Once the inventory changes are ready, proceed with compilation using the command: `kapitan compile --force-fetch -t <targetName>`

Commit the following files:
* `inventory/` (Include changes made to the target file, remote inventory file, environment-specific inventory files, and product inventory files)
* `components/charts/ctrlplane*/11.1.4/*` (Charts)
* `compiled/<targetName>/applications/ctrlplane-tms` (Compiled changes)

###  Cloudflare Setup 

Kindly check these steps for the Cloudflare setup [Cloudflare setup](../common/cplane-cloudflare.md)


## Rollout Steps


### ArgoCD Sync
Ensure all API Control Plane applications (whichever are commited) are synced properly in ArgoCD.

> :warning: ArgoCD will automatically sync the changes if the auto-sync option is enabled. DO NOT FORCE SYNC ANY APPLICATION UNLESS ABSOLUTELY REQUIRED.
 
Update the encoded certificate and key in your Secret Manager or Key Vault

### Verify Cloudflare Secret

- After ArgoCD synchronization, confirm that the following secret is created in the `cplane-system` namespace:  
  **`cplane-cloudflare-origin-pull-tls`** 

```bash
  kubectl get secret cplane-cloudflare-origin-pull-tls -n cplane-system
```


### Restart tenant-manager

Roll out a restart of the `tenant-manager` StatefulSet to apply the changes.

```bash
   kubectl rollout restart sts cplane-tenant-manager -n cplane-system
```

### Update each tenant with new secret

Once the `tenant-manager` pods are up and running, refresh the tenant secrets with the following steps:

1. **Access a `tenant-manager` pod**:

     ```bash
     kubectl exec -it <tenant-manager-pod-name> -n cplane-system -- sh
     ```

2. **Refresh tenant secrets**:
   - Execute the following cURL command inside the pod to update the secrets for each tenant:
     ```bash
     curl -X PUT 'http://localhost:8080/v3/tenants/secret/<tenant-name>/cplane_ingress' \
     -H 'Content-Type: application/json' \
     -H 'Authorization: Bearer <Bearer-token>' \
     -d '{
         "majorVersion": "CHANGEME"  # Replace with the major version of the environment
     }'
     ```

> :information_source: Kindly use TM admin creds to generate Bearer-token

3. **Repeat for all tenants**:
   - Replace `<tenant-name>` in the cURL command for each tenant in the environment and re-execute.


### Traffic switch

After completing the previous steps, configure traffic switching in Cloudflare to direct traffic appropriately.

### Steps for Traffic Switch:


1. **Log in to Cloudflare**:
   - Access the Cloudflare account associated with your domain.

2. **Navigate to DNS Settings**:
   - Go to the **DNS** tab for the domain where the configuration is to be updated.

3. **Add DNS Records**:
   - Depending on your cloud provider (AWS or Azure), add the appropriate DNS records:

   #### **For AWS**:
   - **Type**: `CNAME`  
   - **Name**: `*`  
   - **Content**: Load Balancer (LB) address of the NGINX Ingress Controller.  
   - **Proxy Status**: `Proxied`  
   - **TTL**: `Auto`

   #### **For Azure**:
   - **Type**: `A`  
   - **Name**: `*`  
   - **Content**: IP address of the NGINX Ingress Controller.  
   - **Proxy Status**: `Proxied`  
   - **TTL**: `Auto`

> :information_source: **Wildcard DNS (`*`)** will match all subdomains. This ensures that any subdomain not explicitly defined will still resolve to the specified Load Balancer or IP address.  


## Post-Rollout Steps

### 1. Verify Gainsight Changes

- Ensure the **UI pod** is up and running to confirm that any Gainsight-related changes are functioning properly.

### 2. Cloudflare Analytics & Logs

- **Navigate to Cloudflare Analytics & Logs** to ensure that HTTP traffic is being routed correctly:
  1. Go to the **Analytics** tab.
  2. In the **HTTP Traffic** section, confirm that the total number of requests is being displayed.
  3. Ensure that traffic is flowing through Cloudflare's edge network, as expected.


### 3. Inspect Tenant Traffic

- **Inspect Tenant Network Traffic**:

  1. Log into a tenant.
  2. Open **DevTools** (Right-click -> Inspect, or press `F12` on most browsers).
  3. Navigate to the **Network** tab.
  4. Interact with any functionality in the application (e.g., click a button or load a page).
  5. Confirm that requests are being routed through **Cloudflare** by checking the `Server` field in the response headers.


> [!WARNING]
>  If the server is not listed as **Cloudflare**, ensure that the DNS records and proxy settings are correctly configured in Cloudflare.






