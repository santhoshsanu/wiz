# Amazon Inspector – EC2 Vulnerability Scanning

## Overview
Amazon Inspector is an automated vulnerability management service that helps identify software vulnerabilities and unintended network exposure in supported AWS resources. For EC2 instances, it identifies known software vulnerabilities (CVEs) and generates findings with severity levels and remediation guidance.

## EC2 Scanning Modes

### 1. Agent-Based Scanning
- Uses the AWS Systems Manager (SSM) Agent.
- Assesses EC2 instances that are managed by Systems Manager.
- Collects inventory and software information to identify vulnerabilities.

### 2. Agentless Scanning
- Does not require an agent to be installed or configured inside the instance.
- Inspector takes snapshots of supported EBS volumes and analyzes filesystem data using EBS Direct APIs.
- Snapshots are not copied outside the AWS account.
- Agentless scanning has coverage and feature limitations compared with agent-based scanning.

### 3. Hybrid Scanning Mode
- Uses agent-based scanning for EC2 instances managed by Systems Manager.
- Automatically falls back to agentless scanning for eligible instances that are not managed by Systems Manager.
- Helps increase vulnerability assessment coverage across EC2 instances.

## Example
If an EC2 instance contains an outdated OpenSSL package with a known CVE, Amazon Inspector can identify the vulnerability, assign a severity, and provide details to help the team prioritize remediation.

## Typical Workflow
1. Enable Amazon Inspector for the account and supported resources.
2. Choose or configure the appropriate EC2 scanning mode.
3. Inspector assesses supported instances and software.
4. Review findings by severity and affected resource.
5. Patch or upgrade vulnerable software, then verify the finding is resolved.

## Key Takeaways
- **Inspector identifies vulnerabilities; it is not primarily a threat-detection service.**
- **Agent-based:** uses the SSM Agent.
- **Agentless:** analyzes supported EBS snapshot data without an in-instance agent.
- **Hybrid:** uses agent-based scanning where possible and agentless scanning for eligible unmanaged instances.

## Interview Answer
Amazon Inspector is an automated vulnerability management service. For EC2, it supports agent-based scanning through the SSM Agent, agentless scanning using supported EBS snapshot analysis, and hybrid scan mode. Hybrid mode assesses instances managed by Systems Manager with the agent and can use agentless scanning for eligible instances that are not managed by Systems Manager. Inspector generates findings for known vulnerabilities so teams can prioritize and remediate them.
