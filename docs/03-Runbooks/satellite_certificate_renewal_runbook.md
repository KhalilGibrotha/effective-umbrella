---
title: "Red Hat Satellite & Capsule Certificate Renewal Runbook"
runbook_id: "seo_satellite_cert_upgrade_2025"
version: "1.0"
author: "Server Engineering & Operations"
approver: "Principal Engineer & Architect"
review_date: "2025-11-12"
system: "Red Hat Satellite 6.x & Capsules"
change_type: "Standard Change (SSL Certificate Renewal)"
crq_required: true
environment: "Production"
tags: ["satellite", "certificate", "renewal", "venafi", "capsule", "ssl", "governance"]
---

# Red Hat Satellite & Capsule Certificate Renewal Runbook

**Owner:** Server Engineering & Operations  
**Purpose:** Replace expiring SSL certificates for Red Hat Satellite and Capsule servers using Venafi-issued certificates.

---

## 1. Overview

This runbook covers the end-to-end renewal and deployment of Venafi-managed SSL certificates for one Satellite 6.x instance and twelve Capsules.

The procedure includes renewal, validation, snapshot, certificate installation, and full environment verification.

> **Change Type:** Standard Change  
> **Total Maintenance Window:** ~10–12 hours (fully manual)  
> **Estimated Impact:** Temporary Satellite & Capsule service interruption

---

## CRQ Information

### Estimated Downtime

- Satellite: 2 hrs
- Capsule(per pair): 1 hr
- Total Window: 10-12 hours

### Rollback Plan

1. Stop Satellite services (`satellite-maintain service stop`)
2. Revert to pre-upgrade hypervisor snapshot (`SATELLITE_PRE_CERT_YYYYMMDD`)
3. Start Satellite services (`satellite-maintain service start`)
4. Validate UI, API, and Capsule communication restored
5. If Capsule rollback required, revert Capsule snapshot individually

### Risk Level

- Medium: Operationally disruptive, low technical risk if validated

### Validation Criteria

- Venafi Certificate Authority
- VMware Snapshot Access
- BMC Helix CRQ Approval
- Network access between Satellite and Capsules

### Dependencies

- Venafi Certificate Authority
- VMware Snapshot Access
- BMC Helix CRQ Approval
- Network access between Satellite and Capsules

---

## 2. Prerequisites

| Requirement | Details |
|--------------|----------|
| **Access** | Root SSH to Satellite and all Capsules |
| **Dependencies** | Venafi portal access, snapshot rights on hypervisor |
| **Tools** | `satellite-maintain`, `hammer`, `katello-certs-check`, `capsule-certs-generate` |
| **Change Reference** | BMC Helix CRQ # `<TBD>` |
| **Artifacts** | Venafi PEM bundle: `satellite_cert.pem`, `satellite_key.pem`, `ca_chain_bundle.pem` |

---

## 3. High-Level Flow

Renew in Venafi → stage on Satellite → validate inputs → snapshot → apply Satellite cert → validate → generate Capsule bundles → upgrade Capsules (in pairs) → validate.

---

## 4. Renewal & Staging

### 4.1. Retrieve from Venafi

Export from Venafi:

- `satellite_cert.pem`  
- `satellite_key.pem`  
- `ca_chain_bundle.pem`

Transfer to Satellite:

```bash
scp satellite_*pem ca_chain_bundle.pem satellite.example.com:/root/
sudo mkdir -p /root/certs/2025 && sudo chmod 700 /root/certs/2025
sudo mv /root/*pem /root/certs/2025/
sudo chmod 600 /root/certs/2025/*pem
```

---

## 5. Pre-Flight Validation

### 5.1. Health & Sync Checks

```bash
sudo satellite-maintain health check
hammer ping
foreman-rake katello:sync_status
```

> Ensure all repos and content views are fully synchronized before downtime.

---

### 5.2. Content Policy Sanity (Capsules)

> **Caution:** If Capsules are set to **Immediate**, ensure Satellite repos are not **On-Demand**, or it will trigger full content pulls.

---

### 5.3. Validate Certificate Inputs

```bash
katello-certs-check -t satellite   -b /root/certs/2025/ca_chain_bundle.pem   -c /root/certs/2025/satellite_cert.pem   -k /root/certs/2025/satellite_key.pem
```

Resolve any CN/SAN or chain order mismatches before proceeding.

---

## 6. Snapshot

```bash
sudo satellite-maintain service stop
# Take hypervisor snapshot: SATELLITE_PRE_CERT_YYYYMMDD
```

Label snapshot clearly for CRQ rollback plan.

---

## 7. Apply New Satellite Certificate

```bash
sudo satellite-installer --scenario satellite   --certs-server-cert "/root/certs/2025/satellite_cert.pem"   --certs-server-key "/root/certs/2025/satellite_key.pem"   --certs-server-ca-cert "/root/certs/2025/ca_chain_bundle.pem"   --certs-update-server   --certs-update-server-ca
```

Installer will manage service restarts automatically.

---

## 8. Post-Install Validation

```bash
hammer ping
sudo satellite-maintain health check
katello-certs-check -t satellite   -b /root/certs/2025/ca_chain_bundle.pem   -c /root/certs/2025/satellite_cert.pem   -k /root/certs/2025/satellite_key.pem
```

Confirm HTTPS UI/API access and Smart Proxy status = OK.

---

## 9. Identify Custom Configurations

Review for any non-standard SSL paths:

- `/etc/foreman-installer/scenarios.d/satellite-answers.yaml`  
- `/etc/foreman-installer/custom-hiera.yaml`  
- `/etc/httpd/conf.d/`  
- `/etc/pki/` (custom trust or overrides)

Document deviations before Capsule upgrades.

---

## 10. Capsule Certificate Renewal

### 10.1. Generate Capsule Bundle on Satellite

For each Capsule:

```bash
capsule-certs-generate   --capsule-fqdn capsule01.example.com   --certs-tar /root/certs/2025/capsule01_certs.tar   --server-cert /root/certs/2025/satellite_cert.pem   --server-key /root/certs/2025/satellite_key.pem   --server-ca-cert /root/certs/2025/ca_chain_bundle.pem
```

> This outputs a unique `satellite-installer` command for that Capsule.

Transfer and apply on Capsule:

```bash
scp /root/certs/2025/capsule01_certs.tar capsule01:/root/certs/2025/
sudo satellite-installer --scenario capsule   --certs-tar-file "/root/certs/2025/capsule01_certs.tar"   --certs-update-server --certs-update-server-ca
```

Repeat in **pairs** (two Capsules at a time).

---

## 11. Final Validation

```bash
hammer ping
satellite-maintain health check
```

| Validation | Result |
|-------------|---------|
| Satellite UI/API reachable |  |
| Katello cert check clean |  |
| Capsule sync success |  |
| Smart Proxy status OK |  |
| Content View publish test |  |

---

## 12. Rollback Plan

If post-upgrade issues occur:

1. `satellite-maintain service stop`  
2. Restore the pre-upgrade snapshot: `SATELLITE_PRE_CERT_YYYYMMDD`  
3. `satellite-maintain service start`  
4. Validate UI/API access  
5. If Capsule issues persist, revert affected Capsule snapshot(s) individually  
6. Notify Change Manager and open an incident record in BMC Helix

---

## 13. Notes & Future Enhancements

- Consider automating cert validation and renewal with AAP.  
- Integrate Venafi → Satellite cert push via API for 2026 cycle.  
- Maintain shared `certs/YYYY` structure for consistency.

---

**End of Runbook**
