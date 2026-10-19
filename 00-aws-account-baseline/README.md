
# Lab 00: AWS Multi-Tenant Sandbox Baseline & IAM Hardening

## 📌 Overview
Before deploying cloud resources, an enterprise-grade landing zone must be established. This baseline lab establishes root-user protection, defines budget boundaries, and initiates proactive billing notifications to satisfy secure-by-default architecture parameters.

This sandbox serves as the secure, authenticated environment for all subsequent DevSecOps and automation workloads within this repository.

---

## 🛠️ Security Controls Implemented

### 1. Identity & Access Management (IAM) Hardening
* **Root User MFA:** Configured a hardware-emulated, virtual Multi-Factor Authentication (MFA) token utilizing Time-based One-Time Password (TOTP) cryptographic rotation.
* **Access Containment:** Isolated the root credentials from daily operations, establishing a policy of zero-use for default root credentials in alignment with the AWS Well-Architected Security Pillar.

### 2. Proactive Cost & Financial Guardrails
* **CloudWatch Billing Metrics:** Permanently enabled billing data pipeline integration in the `us-east-1` region to expose financial metadata metrics to Amazon CloudWatch.
* **AWS Free Tier Alarms:** Registered active email notification endpoints to instantly alarm on billing activity exceeding basic 85% utilization thresholds.
* **AWS Budgets & Anomaly Monitors:** Implemented custom budgets monitoring actual vs. forecasted spend to intercept running cost overruns within 24 hours of resource initialization.

---

## 🔍 Validation Log

### Root Account Status Verification
Below is the verification of account baseline activation and standard security configurations:

* **Basic Support Plan:** Activated (Includes baseline documentation, AWS Trust Advisor identity checks, and global support access).
* **MFA Token Status:** **ENABLED** (Verified via consecutive TOTP token sync checks).

---

## 💡 Architecture & Security Takeaways
* **The Root Risk Profile:** Unlike standard Administrator accounts governed by Role-Based Access Control (RBAC), the AWS Root account possesses absolute, unrestrictable permissions. Restricting daily usage of this identity is a baseline security requirement.
* **Reactive vs. Forecasted Alerting:** Standard billing notifications are historical. Utilizing forecasted metrics in AWS Budgets allows engineers to flag cost anomalies before they generate major liabilities, ensuring strict cost-control adherence.

