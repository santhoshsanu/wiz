# Security Incident Response — Automated AWS Alerting Pipeline

This document covers the end-to-end setup of an automated security alerting pipeline on AWS. When a real threat is detected, an email alert lands in your inbox within 1–2 minutes — no human intervention required at any step after setup.

---

## Architecture Overview

```
GuardDuty          detects real threats
     ↓
Security Hub       aggregates & normalises findings
     ↓
EventBridge        routes the finding event to Lambda
     ↓
Lambda             parses finding, labels severity, formats alert
     ↓
SNS                publishes the message
     ↓
Email              alert arrives in your inbox
```

---

## Steps

### Step 1 — Enable GuardDuty

**What it is:** GuardDuty is AWS's threat detection service. It uses machine learning and threat intelligence to continuously monitor your account for threats such as SSH brute-force attacks, crypto-mining activity, and compromised credentials.

**What we did:** Enabled GuardDuty with a single click. It begins watching immediately after activation — no additional configuration needed to start collecting signals.

---

### Step 2 — Enable Security Hub

**What it is:** Security Hub is the central aggregation layer. It collects findings from GuardDuty, Config, Inspector, and other sources and normalises them all into one standard format called **ASFF (Amazon Security Finding Format)**.

**What we did:**
- Enabled Security Hub.
- Accepted the GuardDuty integration so findings flow from GuardDuty into Security Hub automatically.

---

### Step 3 — Create the SNS Topic

**What it is:** SNS (Simple Notification Service) is the delivery mechanism for alerts. A topic acts as a notification channel — every subscriber receives every message published to it.

**What we did:** Created an SNS topic named **`SecurityAlertsTopic`**.

---

### Step 4 — Subscribe Email to SNS

**What we did:** Subscribed `gullapudirupa@gmail.com` to `SecurityAlertsTopic` using the Email protocol.

**Important:** AWS sends a confirmation link to the address. The subscription is inactive until that link is clicked. Without confirmation, no emails are delivered even when Lambda publishes successfully.

---

### Step 5 — Create the IAM Role

**What it is:** An IAM role is the identity that Lambda assumes at runtime. It controls which AWS services the Lambda function is permitted to call.

**What we did:** Created a role named **`SecurityIncidentResponseLambdaRole`**. Without this role, Lambda cannot call SNS or write logs.

---

### Step 6 — Attach the Least-Privilege Policy

**What we did:** Attached a custom inline policy with exactly two permissions:

| Permission | Scope |
|---|---|
| `sns:Publish` | Only the `SecurityAlertsTopic` ARN |
| `logs:CreateLogGroup`, `logs:CreateLogStream`, `logs:PutLogEvents` | Only this Lambda's log group |

The Lambda cannot access any other AWS resource. This follows the principle of least privilege.

---

### Step 7 — Create and Deploy the Lambda Function

**What we did:** Created **`SecurityIncidentResponseFn`** in Python 3.12.

**What the function does:**
1. Receives a Security Hub finding from EventBridge.
2. Extracts severity, title, resource, account ID, and description.
3. Labels `CRITICAL` findings as **P1**.
4. Formats a human-readable alert message.
5. Publishes the message to `SecurityAlertsTopic`.

**Environment variables configured:**

| Variable | Value |
|---|---|
| `SNS_TOPIC_ARN` | ARN of `SecurityAlertsTopic` |
| `MIN_SEVERITY` | `40` (findings below this score are skipped) |

---

### Step 8 — Create the EventBridge Rule

**What it is:** EventBridge watches the AWS event bus and routes matching events to targets (in this case, the Lambda function).

**What we did:** Created **`SecurityHubFindingsRule`** on the default event bus with this event pattern:

```json
{
  "source": ["aws.securityhub"],
  "detail-type": ["Security Hub Findings - Imported"]
}
```

This rule fires on every finding imported into Security Hub and invokes the Lambda function automatically. EventBridge is the bridge between Security Hub and Lambda.

---

### Step 9 — Test with a Simulated Finding

**What we did:** Injected a simulated CRITICAL GuardDuty finding directly via the Lambda Test tab.

**CloudWatch log results:**

| Check | Result |
|---|---|
| Finding received | ✅ |
| Severity detected as CRITICAL | ✅ |
| Labelled as P1 | ✅ |
| SNS publish succeeded (Sent=1, Skipped=0) | ✅ |

---

### Step 10 — End-to-End Pipeline Live

The full automated pipeline is active. A real threat detected by GuardDuty will trigger an email alert automatically within **1–2 minutes**.

---

## Key Resources Created

| Resource | Name / Identifier |
|---|---|
| GuardDuty | Enabled (account-level) |
| Security Hub | Enabled (account-level) |
| SNS Topic | `SecurityAlertsTopic` |
| SNS Subscription | `gullapudirupa@gmail.com` (Email) |
| IAM Role | `SecurityIncidentResponseLambdaRole` |
| Lambda Function | `SecurityIncidentResponseFn` (Python 3.12) |
| EventBridge Rule | `SecurityHubFindingsRule` |

---

## Notes

- The email subscription **must be confirmed** by clicking the link AWS sends. Unconfirmed subscriptions receive no messages.
- The `MIN_SEVERITY=40` threshold filters out low-priority findings. Adjust this value to widen or narrow alerting coverage.
- All Lambda permissions are scoped to the minimum required resources. Expanding permissions requires a deliberate policy update.
