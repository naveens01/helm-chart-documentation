# Cloudflare Setup Guide

This document outlines the steps for adopting and configuring Cloudflare services for domain management, security, and DNS settings.

---

## Cloudflare Setup

### 1. Domain Registration

Register the domain in Cloudflare with the **Enterprise Plan**.

**Example:** `apicp-aw-us.webmethods-dev.io` (for development environments).

---

### 2. SSL/TLS Configuration

#### a. Overview Section

- Enable **Full (strict)** mode for SSL/TLS encryption.

#### b. Edge Certificates

- Enable **Total TLS** and select **Let's Encrypt**.
- Enable the **Always Use HTTPS** option.
- Enable **TLS 1.3**.

#### c. Origin Server

1. Enable **Authenticated Origin Pulls**.
2. Configure a client certificate:
   - Click **Create Certificate**.
   - Select **RSA (2028)**.
   - Set validity (e.g., 15 years, or as required).
   - Generate the certificate and private key.

#### d. Certificate Management

- Encode the generated certificate and key.
- Update the encoded certificate and key in your Secret Manager or Key Vault (e.g., `cplane-common-tls-secret`).
