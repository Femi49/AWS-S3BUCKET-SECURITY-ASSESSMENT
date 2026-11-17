# AWS S3 Cloud Security Assessment and Hardening

![AWS](https://img.shields.io/badge/AWS-Cloud-orange?logo=amazonaws&logoColor=white)
![Amazon S3](https://img.shields.io/badge/Amazon_S3-Object_Storage-569A31?logo=amazons3&logoColor=white)
![CloudTrail](https://img.shields.io/badge/AWS-CloudTrail-FF9900?logo=amazonaws&logoColor=white)
![IAM](https://img.shields.io/badge/AWS-IAM-232F3E?logo=amazonaws&logoColor=white)
![CloudFront](https://img.shields.io/badge/Amazon-CloudFront-8C4FFF?logo=amazonaws&logoColor=white)
![NIST CSF](https://img.shields.io/badge/Framework-NIST_CSF_2.0-005A9C)
![ISO 27001](https://img.shields.io/badge/Standard-ISO_27001%3A2022-006B54)
![Status](https://img.shields.io/badge/Status-In_Progress-orange)

## Project Overview

This project focuses on assessing and hardening Amazon S3 storage resources within an AWS environment. It demonstrates cloud security configuration, access control, data protection, and audit logging.

## AWS Resources

| Resource | Purpose |
|---|---|
| `cloudstart-web-73921` | Static website hosting |
| `cloudstart-private-73921` | Private company documents |
| `cloudstart-logs-73921` | CloudTrail security logs |

## ARCHITECTURE OVERVIEW
<img width="1536" height="1024" alt="AWS S3 Security Assessment Infographic" src="https://github.com/user-attachments/assets/5d1dece5-76f6-4a7b-ad0d-93899fc5598e" />


## Security Controls

- Configured S3 Block Public Access.
- Enabled default bucket encryption using SSE-S3.
- Tested anonymous access to private objects.
- Created an IAM user for access-control testing.
- Configured CloudTrail management event logging for Read and Write events.
- Assessed S3 versioning and bucket security configurations.

## Planned Improvements

- Enable and validate S3 object-level data event logging.
- Implement and test S3 versioning.
- Validate IAM permissions through direct object-access testing.
- Implement Amazon CloudWatch for monitoring and alerting.
- Integrate Amazon SNS for security notifications.
- Strengthen protection and retention of CloudTrail logs.

## Framework Alignment

- NIST Cybersecurity Framework (CSF) 2.0
- ISO/IEC 27001:2022

## Skills Demonstrated

AWS Cloud Security | Amazon S3 | IAM | CloudTrail | Access Control | Data Encryption | Security Assessment | Audit Logging

## Project Status

The initial S3 security assessment and CloudTrail management event configuration have been completed. Additional monitoring, validation, and hardening activities remain planned.

**Note:** This is a practical cloud security learning project and not a formal compliance audit.
