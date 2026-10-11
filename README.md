# Security Compliance Frameworks

An overview of four widely used security frameworks and standards: NIST, CIS, PCI DSS, and ISO 27001.

## 1. Understand the Four Frameworks

### 1. NIST
**National Institute of Standards and Technology**

Provides cybersecurity frameworks and guidance to help organizations manage security risks.

**Example:** Identifying cloud risks, protecting resources, detecting threats, responding to incidents, and recovering from disruptions.

---

### 2. CIS
**Center for Internet Security**

Provides security benchmarks and best practices for securely configuring cloud services, operating systems, and Kubernetes.

**Example:** Checking whether AWS S3 buckets are publicly accessible or whether EKS clusters have appropriate security configurations.

---

### 3. PCI DSS
**Payment Card Industry Data Security Standard**

A security standard for organizations that store, process, or transmit payment card data.

**Example:** Protecting cardholder data, restricting access, maintaining logging, and securing systems that handle payment information.

---

### 4. ISO 27001
**International information security management standard**

Defines requirements for establishing and continually improving an Information Security Management System (ISMS).

**Example:** Managing security risks, access controls, asset inventories, incident response, and security policies.

---

## Quick Comparison

| Framework | Full Name | Focus | Typical Use |
|-----------|-----------|-------|-------------|
| NIST | National Institute of Standards and Technology | Risk management and cybersecurity guidance | Identify, protect, detect, respond, recover |
| CIS | Center for Internet Security | Secure configuration benchmarks | Hardening cloud, OS, and Kubernetes settings |
| PCI DSS | Payment Card Industry Data Security Standard | Payment card data protection | Organizations handling cardholder data |
| ISO 27001 | International information security management standard | Information Security Management System (ISMS) | Formal, certifiable security management |


## AWS GuardDuty

AWS GuardDuty is a managed threat detection service that continuously monitors AWS accounts, workloads, and supported data sources to identify potential security threats.

It analyzes data such as CloudTrail management events, VPC Flow Logs, and DNS query logs using machine learning, anomaly detection, and threat intelligence to identify suspicious activities, such as unauthorized API calls, compromised EC2 instances, malicious IP communication, and potential cryptocurrency mining.

When GuardDuty detects suspicious activity, it generates a security finding containing details about the threat, its severity, and the affected resource.

We can integrate GuardDuty with AWS Security Hub for centralized security visibility and Amazon EventBridge to automate incident response. For example, when a high-severity finding is generated, EventBridge can trigger an SNS notification to alert the security team or invoke a Lambda function to perform an approved remediation action.

This helps us detect potential threats early and respond to security incidents more efficiently.

## AWS GuardDuty

Important points to remember
Service	Purpose
GuardDuty	Detects threats and provides finding details.
CloudTrail	Investigates AWS API calls and account activity.
VPC Flow Logs	Investigates network traffic metadata, including source/destination IPs and ports.
Incident ticket	Tracks severity, priority, investigation, and remediation.








