# AWS Security Automation Portfolio

A hands-on portfolio demonstrating cloud security engineering across IAM hardening, threat detection, incident response, secrets management, and AI-assisted security automation — all built and documented on AWS.

---

## 🛠 Portfolio Directory & Lab Index

### [00. AWS Account Baseline](./00-aws-account-baseline)
* **Goal:** Establishing foundational account security and cost governance from day one through root account MFA enforcement and proactive billing alarms.
* **Core Tech:** AWS Root Account, Multi-Factor Authentication (MFA), AWS Billing Alarms, Amazon CloudWatch, AWS Budgets.

**Key Achievement:** Secured a newly created AWS account from inception by enforcing root account MFA and configuring tiered billing alarms, establishing identity and cost guardrails before deploying any workloads.

---

### [01. IAM Least-Privilege Hardening](./01-iam-least-privilege)
* **Goal:** Hardening baseline cloud configurations using IAM policies.
* **Core Tech:** AWS IAM, Policy Evaluation Logic, IAM Access Analyzer.

**Key Achievement:**

---

### [02. Threat Detection & Vulnerability Analysis](./02-guardduty-threat-detection)
* **Goal:** Simulated SQL injection attack vectors to observe automated intrusion telemetry.
* **Core Tech:** Amazon GuardDuty, AWS WAF, VPC Flow Logs.

**Key Achievement:**

---

### [03. Enterprise Incident Response Alerts](./03-incident-response-alerts)
* **Goal:** Instant real-time metric notification for unauthorized credential actions.
* **Core Tech:** AWS CloudTrail, CloudWatch Alarms, Amazon SNS.

**Key Achievement:**

---

### [04. Data Protection & Secrets Management](./04-kms-secrets-management)
* **Goal:** Eliminating hardcoded keys by establishing database rotation pipelines.
* **Core Tech:** AWS Secrets Manager, AWS KMS Customer-Managed Keys (CMK).

**Key Achievement:**

---

### [05. AI Compliance Scanner for Amazon S3](./05-ai-s3-security-scanner)
* **Goal:** Automated Python script parsing storage elements for vulnerability exposures.
* **Core Tech:** Python, Boto3, Large Language Model (LLM) Integration.

**Key Achievement:**

---

### [06. Automated AI Security Guard (Remediation)](./06-automated-ai-security-guard)
* **Goal:** Dynamic auto-remediation program that patches public-facing buckets instantly.
* **Core Tech:** Python, AWS EventBridge, Boto3, AI Coding Assistants.

**Key Achievement:**

---

## 🧰 Skills & Technologies Demonstrated

| Domain | Technologies |
|--------|--------------|
| **Cloud Security**            | AWS IAM, GuardDuty, WAF, KMS, Secrets Manager, CloudTrail |
| **Detection & Response**      | CloudWatch, SNS, EventBridge, VPC Flow Logs               |
| **Automation**                | Python, Boto3, AWS CLI                                    |
| **AI Integration**            | LLM-assisted scanning and remediation                     |
| **Version Control & Hygiene** | Git, SSH (Ed25519), security-focused `.gitignore`         |

---

## 🔐 Security Practices Applied Throughout

- Root account protected with MFA; root access keys removed
- Billing alarms configured as an early-warning detection control
- SSH key-based authentication to GitHub (no long-lived tokens)
- No hardcoded credentials — all access via IAM roles, env vars, or Secrets Manager
- `.gitignore` enforced before first commit to prevent secret leakage

---

## 📌 Status

Actively building — labs are added and committed incrementally as they are completed.
