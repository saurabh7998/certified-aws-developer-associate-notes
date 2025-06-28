````md
# IAM: Identity and Access Management (Comprehensive Notes)

AWS IAM (Identity and Access Management) is the **core service** used to manage **access and permissions** for AWS resources. It enables you to **securely control who is authenticated** (signed in) and **authorized** (has permissions) to use AWS services and resources.

---

## 👥 IAM Core Concepts

- **Users**: A person or system needing access to AWS services.
- **Groups**: Collections of IAM users (e.g., "Developers", "Admins") to apply common permissions.
- **Roles**: IAM identities that AWS services or applications assume to perform actions on your behalf.
- **Policies**: JSON documents defining permissions.

> ❗ **Never use the root account except for initial setup. Always create individual IAM users with least privilege.**

---

## 🧾 What is a Policy?

IAM **policies** define **permissions** for actions in AWS:
- Who can do **what**, **on which resource**, and **under what conditions**.
- Policies are written in **JSON** and follow a specific structure.

---

## 🔐 IAM Policy Structure (JSON Format)

### 1. `Version`
- Specifies policy language version.
```json
"Version": "2012-10-17"
````

### 2. `Id` *(Optional)*

* Identifier for the policy.

### 3. `Statement` (Required)

* The array/object that holds the permission rules.

### Each Statement contains:

| Field       | Purpose                                                                 |
| ----------- | ----------------------------------------------------------------------- |
| `Sid`       | (Optional) Statement ID                                                 |
| `Effect`    | `"Allow"` or `"Deny"`                                                   |
| `Principal` | **Who** the policy applies to (only in resource-based policies)         |
| `Action`    | **What** actions are allowed or denied (e.g., `"s3:GetObject"`)         |
| `Resource`  | **Which AWS resources** the action applies to (e.g., an S3 bucket)      |
| `Condition` | (Optional) **When** the statement is in effect (e.g., based on IP, MFA) |

---

## 📌 Key Terms

### 🎯 What is a Resource?

* A specific AWS entity you are controlling access to.
* Examples:

  * `arn:aws:s3:::my-bucket/*` — All objects in an S3 bucket
  * `arn:aws:dynamodb:us-east-1:123456789012:table/my-table`
* Mentioned in the `Resource` field of a policy.

### 👤 What is a Principal?

* The **identity (user, role, account, or service)** that is receiving the permissions.
* Used only in **resource-based policies**.
* Examples:

  * IAM user ARN
  * AWS service like `"lambda.amazonaws.com"`
  * Account: `"Principal": {"AWS": "arn:aws:iam::123456789012:root"}`

---

## ✅ IAM Policy Example

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowS3Access",
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::example-bucket/*",
      "Condition": {
        "IpAddress": {
          "aws:SourceIp": "192.0.2.0/24"
        }
      }
    }
  ]
}
```

---

## 🔑 Types of Policies

### 1. **Identity-based Policies**

* Attach to **users**, **groups**, or **roles**.
* Grant permissions directly to IAM identities.
* Can be:

  * **Managed** (AWS or customer-created)
  * **Inline** (embedded in a single identity)

### 2. **Resource-based Policies**

* Attach directly to AWS resources (e.g., S3 bucket policies, Lambda permissions).
* Include a **Principal** field to define who has access.
* Support **cross-account access**.

### 3. **Permissions Boundaries**

* Advanced feature to set the **maximum permissions** a user or role can have.
* Used to **restrict** what identity-based policies can grant.
* Do **not** grant permissions on their own.

### 4. **Service Control Policies (SCPs)**

* Used in **AWS Organizations**.
* Set **permission guardrails** for **all accounts** in an Organization or Organizational Unit (OU).
* Define **maximum available permissions**.
* Do **not** grant permissions — they **limit** what can be granted.

**Example Use Case:** Prevent use of specific regions or services across all child accounts.

### 5. **Access Control Lists (ACLs)**

* Used for **legacy and cross-account** access in services like S3.
* Controls **who can access a resource and what actions they can perform**.
* Do **not use JSON**, unlike IAM policies.
* Cannot grant permissions to users in the same account.
* Mostly used in **S3** and **Amazon EFS**.

### 6. **Session Policies**

* Passed when using `sts:AssumeRole` or federated sign-in.
* Apply **temporary restrictions** for the session only.
* Do **not grant** permissions — they **limit** permissions during a session.

---

## 🧪 IAM Policy Testing Tools

### ✅ AWS Policy Simulator

* Use to test IAM and resource-based policies.
* 🔗 [AWS Policy Simulator](https://policysim.aws.amazon.com/home/index.jsp)

### ✅ CLI — Dry Run Option

* Many CLI commands support `--dry-run` to test permissions.
* If allowed:

  > `Request would have succeeded, but DryRun flag is set`
* If denied:

  > `An error occurred (UnauthorizedOperation)...`

---

## 🛡️ IAM Best Practices

* Create **one IAM user per person** (no shared accounts).
* Create **one IAM role per application or service**.
* **Never embed credentials** in code or push to Git.
* Use **IAM roles** (with temporary credentials) for EC2, Lambda, etc.
* **Avoid root account** after initial setup — delete its access keys.
* Apply **least privilege principle** — give only necessary permissions.
* Enable **MFA** for all users.
* Rotate access keys regularly.
* Use **SCPs** for governance in multi-account setups.

---

## 📝 Summary Table: IAM Policy Types

| Policy Type                  | Grants Permissions? | Attached To                | Notes                                                        |
| ---------------------------- | ------------------- | -------------------------- | ------------------------------------------------------------ |
| Identity-based               | ✅                   | Users, Groups, Roles       | Standard way to define permissions                           |
| Resource-based               | ✅                   | AWS resources              | Supports cross-account access. Requires `Principal`          |
| Permissions Boundary         | ❌                   | IAM Users/Roles            | Sets **maximum** permissions allowed, does not grant any     |
| SCP (Service Control Policy) | ❌                   | AWS Accounts (via Org/OUs) | Used for account-wide guardrails. Requires AWS Organizations |
| ACL                          | ✅ (limited)         | S3, EFS resources          | Older system, only cross-account; not JSON-based             |
| Session Policy               | ❌                   | Temporary Sessions         | Restricts permissions during a session (STS/federated)       |

