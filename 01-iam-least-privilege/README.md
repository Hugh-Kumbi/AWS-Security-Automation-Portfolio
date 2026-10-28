# Lab 01: Cloud Security Hardening with AWS IAM & Identity Governance

## 📌 Overview
Operating in the AWS cloud with root credentials violates core compliance regulations and exposes systems to critical compromise vectors. This lab establishes an isolated, secure identity boundary. 

By designing granular, secure-by-default JSON policies and logical groupings, we isolate developer access and enforce the Principle of Least Privilege (PoLP) across the environment.

---

## 🛠️ Security Architecture & Controls Implemented

### 1. Role-Based Access Control (RBAC) Design
* **Security Grouping:** Provisioned separate IAM Groups mapped to job functions (e.g., Development, Security Audit) to avoid applying permissions directly to individual users.
* **MFA Enforcements:** Required hardware-emulated TOTP MFA tokens across all administrative and user identities.

### 2. Least-Privilege JSON Policies
* **S3 Action Isolation:** Configured granular resource-level policies restricting generic write/delete administrative access to specific S3 storage buckets.
* **Implicit Deny Safeguards:** Leveraged the AWS evaluation logic paradigm, utilizing "Implicit Deny" rules to block access to unmapped AWS services by default.

### 3. Identity & Access Auditing
* **Credential Reports:** Generated programmatic credential reports to verify last-login times, key-rotation status, and active password ages.
* **Access Analyzer Audit:** Used AWS IAM Access Analyzer to scan policies for over-privileged permission structures.

---

## 💡 Engineering & Exam Key Takeaways

* **AWS Policy Evaluation Logic:** AWS evaluates requests using an explicit deny model. An **Explicit Deny** always overrides an **Explicit Allow**, and an **Implicit Deny** exists on any resource or action not explicitly allowed.
* **Resource vs. Identity Policies:** Identity-based policies define what an IAM identity (User, Group, Role) can do. Resource-based policies (like S3 Bucket Policies) define who is permitted to access that specific resource from outside or inside the account.
