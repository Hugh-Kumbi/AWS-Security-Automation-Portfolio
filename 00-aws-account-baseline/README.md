# Lab 00: AWS Account Baseline & Identity Hardening

## Overview

Before deploying any cloud resources, an enterprise-grade landing zone must be established. This baseline lab documents the foundational security controls applied to an existing AWS account (originally registered in 2024, reactivated for this portfolio) to satisfy secure-by-default architecture parameters.

This sandbox serves as the secure, authenticated environment for all subsequent DevSecOps and automation workloads within this repository.

**Target Role:** Cloud Security Engineer / DevSecOps Engineer  
**Certification Track:** AWS Certified Security – Specialty (in progress)

---

## Security Controls Implemented

### 1. Identity & Access Management (IAM) Hardening
- **Root User MFA:** Configured a virtual Multi-Factor Authentication (MFA) token using Time-based One-Time Password (TOTP) rotation via an authenticator app. Root credentials are now isolated from daily operations in alignment with the AWS Well-Architected Security Pillar.
- **Access Containment:** Established a policy of zero-use for root credentials. All future operational work will be performed through scoped IAM identities with least-privilege policies.

### 2. Proactive Cost & Financial Guardrails
- **AWS Budgets:** Created a $2.00 USD cost budget with two alert thresholds, one at 80% of *actual* spend and one at 100% of *forecasted* spend. Forecasted alerts analyze real-time usage patterns to warn of projected overruns before charges are incurred.
- **AWS Cost Anomaly Detection (CAD):** Enabled an ML-based monitor that establishes a normal daily spend baseline and flags anomalous spikes with email subscription notifications. Investigable via Amazon Q for plain-language root-cause explanations.
- **CloudWatch Billing Alerts:** Permanently enabled the billing data pipeline in `us-east-1`, exposing financial metrics to Amazon CloudWatch for custom alarm creation. This preference is irreversible once enabled, it is free and enables fine-grained spend monitoring.
- **Billing Alerts:** Registered a primary email endpoint to receive automated notifications on billing activity.
- **Pay-As-You-Go Awareness:** Confirmed the account operates on a paid plan (the 12-month Free Tier expired), establishing a discipline of terminating all lab resources immediately after each session.

---

## Validation Log

| Control | Status | Verification |
|---------|--------|--------------|
| Root User MFA                    | ✅ Enabled                                        | [Root MFA enabled](https://github.com/Hugh-Kumbi/AWS-Security-Automation-Portfolio/blob/main/00-aws-account-baseline/screenshots/01-root-mfa-enabled.png)        |
| AWS Budgets                      | ✅ $2 budget; 80% actual + 100% forecasted alerts | [AWS Budget alerts](https://github.com/Hugh-Kumbi/AWS-Security-Automation-Portfolio/blob/main/00-aws-account-baseline/screenshots/02-aws-budget-alerts.png)       |
| Cost Anomaly Detection           | ✅ Active monitor + email subscription            | [Cost Anomaly Detection monitor](https://github.com/Hugh-Kumbi/AWS-Security-Automation-Portfolio/blob/main/00-aws-account-baseline/screenshots/03-cost-anomaly-detection.png)  |
| CloudWatch Billing Metrics       | ✅ Enabled                                        | [Billing preferences](https://github.com/Hugh-Kumbi/AWS-Security-Automation-Portfolio/blob/main/00-aws-account-baseline/screenshots/04-billing-preferences.png)     |
| Current Monthly Bill at Baseline | ✅ $0.00 USD                                      | [Billing dashboard](https://github.com/Hugh-Kumbi/AWS-Security-Automation-Portfolio/blob/main/00-aws-account-baseline/screenshots/05-billing-dashboard-zero.png)  |
| Support Plan                     | ✅ Basic (Free)                                   | [Support plan](https://github.com/Hugh-Kumbi/AWS-Security-Automation-Portfolio/blob/main/00-aws-account-baseline/screenshots/06-support-plan.png)                                              |

---

## Visual Verification

> **Root MFA enabled (IAM Dashboard green checkmark):**
![Root MFA enabled](https://github.com/Hugh-Kumbi/AWS-Security-Automation-Portfolio/blob/main/00-aws-account-baseline/screenshots/01-root-mfa-enabled.png)

> **AWS Budgets ($2 cost budget with actual + forecasted alerts):**
![AWS Budget alerts](https://github.com/Hugh-Kumbi/AWS-Security-Automation-Portfolio/blob/main/00-aws-account-baseline/screenshots/02-aws-budget-alerts.png)

> **AWS Cost Anomaly Detection (Active monitor and email subscription):**
![Cost Anomaly Detection monitor](https://github.com/Hugh-Kumbi/AWS-Security-Automation-Portfolio/blob/main/00-aws-account-baseline/screenshots/03-cost-anomaly-detection.png)

> **Billing Preferences (Alerts and CloudWatch billing metrics enabled):**
![Billing preferences](https://github.com/Hugh-Kumbi/AWS-Security-Automation-Portfolio/blob/main/00-aws-account-baseline/screenshots/04-billing-preferences.png)

> **Baseline billing dashboard ($0.00 USD at project start):**
![Billing dashboard](https://github.com/Hugh-Kumbi/AWS-Security-Automation-Portfolio/blob/main/00-aws-account-baseline/screenshots/05-billing-dashboard-zero.png)

> **Support plan (Basic *Free* confirmed):**
[!Support plan verification](https://github.com/Hugh-Kumbi/AWS-Security-Automation-Portfolio/blob/main/00-aws-account-baseline/screenshots/06-support-plan.png)

---

## Architecture & Security Takeaways

- **The Root Risk Profile:** Unlike Administrator IAM users governed by Role-Based Access Control (RBAC), the AWS Root account holds absolute, unrestrictable permissions. It is immune to IAM policy restrictions and is the only identity that can close the account, change support plans, or access certain billing details. Compromise of the root user leaves no fallback entity to evict an attacker. Restricting its daily use and enforcing MFA is a non-negotiable baseline control.
- **Auditability:** IAM user actions are logged in CloudTrail with unique ARNs, preserving individual accountability. Root actions are logged simply as `Root`, which is why operational work should always flow through IAM identities.
- **Reactive vs. Forecasted Alerting:** Standard billing notifications are historical, they tell you what you already spent. Layering AWS Budgets (with forecasted-spend alerts) and Cost Anomaly Detection on top of basic alarms intercepts runaway costs *before* they become liabilities. In the cloud, cost anomalies are often the first signal of misconfiguration or compromise.
- **Defense in Depth for Cost:** Three overlapping layers, Budgets (threshold-based), CAD (ML-based anomaly), and CloudWatch billing alarms (metric-based). Which means that no single failure mode goes unnoticed.

---

## Tools & Services Used

`AWS IAM` · `Multi-Factor Authentication (TOTP)` · `AWS Billing & Cost Management` · `AWS Budgets` · `AWS Cost Anomaly Detection` · `Amazon CloudWatch` · `Git` · `Visual Studio Code`

---

## What's Next

**Lab 01: IAM Least-Privilege Hardening** - designing scoped users, groups, and custom policies to enforce least privilege beyond the root account.