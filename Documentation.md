# AWS S3 Cloud Security Assessment and Hardening

## 1. Project Overview

This project involved configuring and assessing an Amazon Simple Storage Service (Amazon S3) environment to understand how cloud storage resources can be protected against unauthorized access, accidental data exposure, and inadequate security monitoring.

I created three S3 buckets for different purposes: hosting a static website, storing private company documents, and collecting security logs. I then reviewed the initial security configurations, tested access to the private documents bucket, created an IAM user to explore access control, and configured AWS CloudTrail to record account activity.

The project demonstrates practical experience with AWS storage security, identity and access management, access-control validation, and cloud audit logging.

### Project Objectives

- Deploy a sample static website using Amazon S3.
- Configure separate buckets for website content, private documents, and security logs.
- Assess S3 Block Public Access settings.
- Review bucket policies and default encryption settings.
- Assess S3 bucket versioning.
- Test anonymous access to the private documents bucket.
- Explore IAM-based access to S3 resources.
- Configure AWS CloudTrail for security event logging.
- Document the initial security posture and identify opportunities for improvement.

### Scope

The assessment covered the initial configuration of the three S3 buckets, selected access-control tests, and CloudTrail logging.

It was a controlled learning exercise and not a comprehensive audit of every AWS security control.

---

## 2. AWS Environment and Architecture

Three S3 buckets were created to separate website content, private business documents, and security logs.

| Bucket | Name | Purpose |
|---|---|---|
| Bucket 1 | `cloudstart-web-73921` | Stores static website files |
| Bucket 2 | `cloudstart-private-73921` | Stores private company documents |
| Bucket 3 | `cloudstart-logs-73921` | Receives CloudTrail security logs |

*Note: S3 bucket names are globally unique. The names above identify the resources used in this project.*

### Architecture Overview

<img width="1536" height="1024" alt="AWS S3 Security Architecture Flow" src="https://github.com/user-attachments/assets/248cdd08-5f59-4f73-a2a0-4577cc83d592" />


The website bucket stores the static website files. The private documents bucket is intended to remain accessible only to authorized identities. The logs bucket receives CloudTrail log files.

CloudTrail management events and S3 object-level data events are different categories of logging. Object-level activities such as `GetObject` and `PutObject` require the appropriate S3 data event selectors.

---

## 3. Phase One: Static Website Bucket

### 3.1 Objective

The first phase involved preparing the website bucket to store the static website files.

### 3.2 Website Files

I used a sample static website containing the following files:

```text
website/
├── index.html
├── contact.html
└── styles.css
```

- `index.html` — The main landing page.
- `contact.html` — The contact page.
- `styles.css` — Defines the website's visual presentation.

### 3.3 Upload Procedure

1. Open the AWS Management Console.
2. Navigate to Amazon S3.
3. Open the bucket named `cloudstart-web-73921`.
4. Select **Upload**.
5. Choose **Add files**.
6. Select `index.html`, `contact.html`, and `styles.css`.
7. Select **Upload** to transfer the files.
8. Confirm that the three files appear in the bucket's object listing.

### 3.4 Static Website Hosting Configuration

I navigated to the bucket's **Properties** tab and located the **Static website hosting** section.

The intended configuration was:

| Setting | Value |
|---|---|
| Static website hosting | Enabled |
| Hosting type | Host a static website |
| Index document | `index.html` |

The index document specifies the default page served when a visitor opens the website endpoint.

**Security consideration:** Enabling static website hosting does not, by itself, make the website publicly accessible. Public access depends on the bucket's access configuration and the hosting architecture.

For a production-style implementation, a preferred approach is to keep the S3 bucket private and use Amazon CloudFront with Origin Access Control (OAC). This allows CloudFront to retrieve website content without granting anonymous users direct access to the bucket.

Direct S3 website hosting may require public-read access to website objects. If used for a learning exercise, it should be isolated from private documents and security logs.

### 3.5 Initial Security Assessment: Bucket 1

I reviewed the initial security configuration of `cloudstart-web-73921`.

#### A. Block Public Access

**Navigation:** S3 → `cloudstart-web-73921` → Permissions → Block public access.

I checked the four S3 Block Public Access settings.

| Control | Initial observation | Status |
|---|---|---|
| Block public access settings | All four settings enabled | Pass |

**Security significance:** These settings help prevent public access through bucket policies and access control lists, depending on the configuration.

**Note:** If the bucket is intended to serve a website directly through the S3 website endpoint, the public-access configuration may need to change. A private S3 bucket behind CloudFront is preferable when the objective is to serve a public website without making the bucket itself publicly accessible.

<img width="1918" height="851" alt="baselinesecurity" src="https://github.com/user-attachments/assets/c8936112-725d-48f6-9fcc-9eb714b08c5c" />


#### B. Bucket Policy

**Navigation:** Permissions → Bucket policy.

No bucket policy was displayed during the initial assessment.

| Control | Observation | Status |
|---|---|---|
| Bucket policy | No bucket policy displayed | Pass for the initial policy review; other access paths still require assessment |

The absence of a bucket policy does not establish that a bucket is completely secure. Access can also be affected by IAM identity policies, access point policies, ACLs where applicable, and other AWS authorization mechanisms.

#### C. Default Encryption

**Navigation:** Properties → Default encryption.

I verified that default encryption was enabled.

| Control | Observation | Status |
|---|---|---|
| Default encryption | Enabled | Pass |

Default encryption helps protect newly uploaded objects at rest.

For this project, Amazon S3-managed encryption keys (SSE-S3) provide a straightforward baseline. AWS Key Management Service (SSE-KMS) can be considered where additional key-management controls or specific compliance requirements apply.

#### D. Bucket Versioning

**Navigation:** Properties → Bucket Versioning.

I located the versioning configuration and identified it as a control to assess.

| Control | Initial result | Status |
|---|---|---|
| Bucket versioning | Suspended | Control not Passed |

Versioning preserves multiple versions of an object when it is overwritten or deleted. It can support recovery from accidental changes and certain forms of malicious modification.

The actual versioning state should be recorded from the S3 console before assigning a Pass or Fail result.

<img width="1910" height="713" alt="suspendedbucketversioning" src="https://github.com/user-attachments/assets/e3b9ec3e-2302-437e-8fb7-e8b36873ecf5" />



---

## 4. Phase Two: Private Company Documents Bucket

### 4.1 Objective

The second bucket was designed to represent a company's internal document repository.

The objective was to assess whether anonymous users and an IAM identity without the appropriate permissions could access the stored resources.

### 4.2 Bucket Configuration

The bucket was named:

`cloudstart-private-73921`

It was intended to store private company documents rather than publicly accessible website content.

For a realistic company environment, this bucket should contain only test documents during the lab. Real customer records, confidential company information, and sensitive personal data should not be uploaded to a learning environment.

### 4.3 Initial Security Assessment

#### A. Block Public Access

**Navigation:** S3 → `cloudstart-private-73921` → Permissions → Block public access.

I checked the four public-access blocking settings.

| Control | Observation | Status |
|---|---|---|
| Block public access | All four settings enabled | Pass |

Keeping all four settings enabled is appropriate for a private documents bucket unless a documented business requirement justifies a different configuration.

These settings provide an important preventive control against accidental public exposure.

#### B. Bucket Policy

**Navigation:** Permissions → Bucket policy.

No bucket policy was displayed during the initial review.

| Control | Observation | Status |
|---|---|---|
| Bucket policy | No bucket policy displayed | Pass for the initial policy review; full authorization review still required |

The absence of a bucket policy does not mean that the bucket has no access controls. IAM policies and other applicable authorization mechanisms still determine which identities can access the resources.

#### C. Default Encryption

**Navigation:** Properties → Default encryption.

I verified that default encryption was enabled.

| Control | Observation | Status |
|---|---|---|
| Default encryption | Enabled | Pass |

This provides a baseline for protecting stored documents at rest.

#### D. Bucket Versioning

**Navigation:** Properties → Bucket Versioning.

I found that versioning was not enabled.

| Control | Observation | Status |
|---|---|---|
| Bucket versioning | Not enabled | Improvement required |

**Security implication:** Without versioning, overwriting an object or deleting it may make recovery more difficult. Versioning can help preserve earlier object versions and improve recovery options.

**Recommendation:** Enable bucket versioning and test it by uploading a harmless test file, modifying it, and reviewing the resulting object versions.

Versioning is a recovery control, not a substitute for access control, backups, or appropriate retention policies.

<img width="1915" height="886" alt="nobucketpolicyforprivdoc" src="https://github.com/user-attachments/assets/e0f7a492-0a8f-4af2-82ba-f029af58134e" />


---

## 5. Phase Three: Anonymous Access Testing

### 5.1 Objective

The purpose of this test was to determine whether an unauthenticated external user could access the private documents bucket.

Anonymous access testing helps identify unintended public exposure without relying on an AWS login session.

### 5.2 Test Procedure

1. Opened the private documents bucket in the S3 console.
2. Obtained the appropriate object URL for the test.
3. Opened an incognito browser window to avoid relying on an existing authenticated browser session.
4. Attempted to access the private document without signing in to AWS.
5. Observed the response returned by AWS.

### 5.3 Result

The browser returned an access-denied response.

| Field | Finding |
|---|---|
| Test ID | S3-002 |
| Asset | Private company document bucket |
| Test type | Anonymous access validation |
| Expected result | Unauthenticated users cannot access private documents |
| Observed result | Access denied |
| Result | Pass for the tested anonymous-access path |


<img width="1918" height="1030" alt="anonymouscannotopenobject" src="https://github.com/user-attachments/assets/73b64292-2f1a-4410-b9fc-71c8ad10a73c" />



### 5.4 Security Interpretation

The result indicates that the tested anonymous request was denied.

This is consistent with the intended configuration of a private S3 bucket.

However, a single access-denied response does not prove that every object and access path is secure. Additional checks should include bucket policies, IAM identity policies, access point policies where applicable, and any other authorization mechanisms configured for the resource.

---

## 6. Phase Four: IAM User Access Testing

### 6.1 Objective

I created an IAM user and attempted to determine whether the identity could locate or access the S3 buckets, particularly the private documents bucket.

The purpose was to explore the relationship between IAM permissions and S3 resource access.

### 6.2 Procedure

1. Opened the AWS IAM console.
2. Created an IAM user.
3. Attempted to use the IAM identity to locate the S3 buckets.
4. Observed that the user could not locate the private documents bucket.

### 6.3 Initial Observation

The IAM user was unable to locate the bucket through the attempted process.

| Field | Finding |
|---|---|
| Test ID | IAM-001 |
| Asset | `cloudstart-private-73921` |
| Test type | IAM access exploration |
| Expected result | An identity without the required permissions should not be able to access private documents |
| Observed result | The bucket could not be located by the IAM user |
| Result | Inconclusive — further validation required |

<img width="1918" height="861" alt="creatednewuserwithouts3permission" src="https://github.com/user-attachments/assets/03ba97df-ad73-4ebb-9afb-2f57756f9281" />


### 6.4 Why the Result Is Inconclusive

An IAM user may be unable to see a bucket because it lacks the `s3:ListAllMyBuckets` permission, even if the permissions attached to that identity would allow access to a specifically named bucket or object.

Similarly, a user may be able to list buckets but lack permission to read their contents.

These are separate authorization operations:

| IAM permission | Purpose |
|---|---|
| `s3:ListAllMyBuckets` | Allows listing buckets in the account |
| `s3:ListBucket` | Allows listing objects within a specified bucket |
| `s3:GetObject` | Allows retrieving an object, subject to applicable authorization |
| `s3:PutObject` | Allows uploading an object, subject to applicable authorization |
| `s3:DeleteObject` | Allows deleting an object, subject to applicable authorization |

Therefore, the inability to locate a bucket does not independently prove that the user is denied all access to that bucket.

### 6.5 Recommended Follow-Up Test

To validate access more reliably:

1. Confirm the IAM user's attached identity policies and group memberships.
2. Check whether the user has `s3:ListAllMyBuckets`.
3. Attempt to access the private bucket by its exact name through the S3 console or an authorized AWS CLI session.
4. Test a read operation against a known test object.
5. Record the result and the AWS error message.
6. Review CloudTrail for the corresponding event, where the relevant event type is being recorded.

Use a test identity with no unnecessary permissions. Do not grant broad administrative access merely to make the test succeed.

If the test identity is supposed to have no access, verify that the object-read request is denied. Document the specific action and response rather than recording only that the bucket was not visible.

### 6.6 Least-Privilege Principle

IAM permissions should be assigned according to the identity's actual responsibilities.

For example, an employee who only needs to download a particular document may require read access to that object, but should not automatically receive permission to upload, delete, or administer the bucket.

This is the principle of **least privilege**: grant only the permissions required to perform the assigned task.

---

## 7. Phase Five: CloudTrail Security Logging

### 7.1 Objective

I created a dedicated S3 bucket to receive AWS CloudTrail log files.

The bucket was named:

`cloudstart-logs-73921`

The objective was to establish an audit trail of selected AWS account activity and create a foundation for investigating security-relevant events.

### 7.2 Log Destination

The intended configuration was:

| Setting | Value |
|---|---|
| Logging service | AWS CloudTrail |
| Destination bucket | `cloudstart-logs-73921` |
| Management events | Enabled |
| S3 data events | To be verified for the private documents bucket |

The logs bucket should remain private. It contains security-relevant information that could help an attacker understand the AWS environment if exposed.

### 7.3 Management Event Logging

I completed the management event settings.

Management events record control-plane operations involving AWS resources.

Examples include:

- Creating or deleting an S3 bucket.
- Changing bucket configuration.
- Modifying relevant AWS resource settings.
- Performing other supported AWS management operations.

For the project, both read and write management events were selected.

| Setting | Configuration |
|---|---|
| Read management events | Enabled |
| Write management events | Enabled |

This provides visibility into selected AWS management activity.

Management events do not automatically record every action performed on individual S3 objects.

<img width="1918" height="641" alt="cloudTrail enabling" src="https://github.com/user-attachments/assets/9c81ca16-baba-482e-977a-afd13d35fe44" />


### 7.4 S3 Data Event Logging

S3 data events can record object-level operations, including supported upload, retrieval, and deletion actions.

Examples include:

- `PutObject` — Uploading or writing an object.
- `GetObject` — Retrieving an object.
- `DeleteObject` — Deleting an object.

These events are separate from management events.

To monitor object activity in `cloudstart-private-73921`, the trail must have the appropriate S3 data event selectors configured for that bucket.

The intended configuration is:

| Setting | Intended configuration |
|---|---|
| Data event source | S3 |
| Resource | `cloudstart-private-73921` |
| Read data events | Enabled |
| Write data events | Enabled |

**Verification status:** The management event settings were completed. The S3 data event configuration and successful capture of object-level events must be verified before marking this control as fully operational.

### 7.5 Finding the CloudTrail Log Files

CloudTrail organizes delivered log files in an S3 folder structure that commonly resembles the following:

```text
cloudstart-logs-73921/
└── AWSLogs/
    └── <AWS_ACCOUNT_ID>/
        └── CloudTrail/
            └── <AWS_REGION>/
                └── YYYY/
                    └── MM/
                        └── DD/
                            └── <log-file>.json.gz
```

The account ID, region, and date directories depend on the AWS account, trail configuration, and recorded event locations.

The `.json.gz` files contain compressed JSON log records.

### 7.6 CloudTrail Log Verification Procedure

1. Open Amazon S3 in the AWS Management Console.
2. Open `cloudstart-logs-73921`.
3. Navigate into `AWSLogs`.
4. Open the folder corresponding to the AWS account ID.
5. Open `CloudTrail`.
6. Select the relevant AWS region.
7. Navigate through the year, month, and day folders.
8. Locate a log file ending in `.json.gz`.
9. Download a log file and inspect its contents using a suitable extraction utility or command-line tool.

Log delivery is not necessarily immediate. Allow time for CloudTrail to deliver events.

If no files appear, verify the trail's status, destination configuration, region settings, and permissions. Also confirm that relevant events have occurred.

### 7.7 Verify That the Logs Contain the Expected Events

Finding a log file is not sufficient to demonstrate that every intended event is being recorded.

For management events, inspect the records for relevant fields such as:

- `eventTime`
- `eventSource`
- `eventName`
- `awsRegion`
- `userIdentity`
- `requestParameters`
- `errorCode`, when applicable

For S3 object-level tests, look for events associated with the `s3.amazonaws.com` event source and the expected object operation.

For example, an upload test may generate a `PutObject` event if the corresponding data event selector is configured correctly.

### 7.8 Recommended CloudTrail Test

After confirming that S3 data events are configured:

1. I upload a harmless file named `security-test.txt` to the private documents bucket using an authorized test identity.
2.  I downloaded the file.
3. I attempted an unauthorized access operation with the test identity.
4. I allowed time for event delivery.
5. I inspectd the CloudTrail logs for the corresponding events.
6. Record the observed event names, timestamps, identities, and outcomes.

A denied request may be recorded differently depending on the operation and event category. Confirm that the event actually appears before claiming that CloudTrail captured it.

### 7.9 Logging Cost Consideration

CloudTrail management events and S3 data events have different pricing implications.

S3 data events can incur additional charges. For this learning project, configure data-event logging only for the bucket being assessed unless broader coverage is required.

The logs bucket should also be protected against unauthorized deletion or modification. Additional controls to consider include versioning, restrictive access policies, and S3 Object Lock where an appropriate retention requirement exists.

---

## 8. Consolidated Initial Assessment Results

The following table summarizes the observations made during the project.

| ID | Control | Asset | Observation | Status |
|---|---|---|---|---|
| S3-001 | Block Public Access | Website bucket | All four settings enabled during the initial review | Pass for the observed configuration |
| S3-002 | Anonymous access | Private documents bucket | Unauthenticated access returned Access Denied | Pass for the tested access path |
| S3-003 | Bucket policy | Website bucket | No bucket policy displayed | Initial review completed; broader authorization review required |
| S3-004 | Bucket policy | Private documents bucket | No bucket policy displayed | Initial review completed; broader authorization review required |
| S3-005 | Default encryption | Website bucket | Enabled | Pass |
| S3-006 | Default encryption | Private documents bucket | Enabled | Pass |
| S3-007 | Versioning | Website bucket | Initial state not recorded | To be assessed |
| S3-008 | Versioning | Private documents bucket | Disabled | Improvement required |
| IAM-001 | IAM access testing | Private documents bucket | IAM user could not locate the bucket | Inconclusive; direct resource-access test required |
| CT-001 | Management event logging | AWS account | Read and write management events configured | Configuration completed; event delivery should be verified |
| CT-002 | S3 data event logging | Private documents bucket | Configuration and capture not yet confirmed | To be verified |
| CT-003 | Log delivery | Logs bucket | Dedicated logs bucket created and used as the CloudTrail destination | Verify successful log delivery and contents |

---

## 9. Security Findings and Recommendations

### Finding 1: S3 Bucket Versioning Disabled

**Finding ID:** S3-008

**Affected asset:** `cloudstart-private-73921`

**Observation:** Bucket versioning was not enabled.

**Potential impact:** Accidental overwrites and deletions may be harder to recover from. Versioning can preserve earlier versions of objects and support recovery.

**Recommendation:** Enable versioning and test it using a harmless object. Establish appropriate retention and recovery procedures based on the organization's requirements.

**Priority:** Medium, subject to the importance of the stored information and the availability of other recovery controls.

### Finding 2: IAM Resource Access Requires Further Validation

**Finding ID:** IAM-001

**Affected asset:** `cloudstart-private-73921`

**Observation:** The IAM user could not locate the bucket.

**Potential impact:** The current observation does not establish whether the identity is denied all resource access. An incomplete test could leave unintended permissions undiscovered.

**Recommendation:** Review the user's attached policies, group memberships, and applicable resource policies. Test direct access to a known object and record the outcome.

**Priority:** Medium until the authorization configuration and test results are confirmed.

### Finding 3: S3 Data Event Logging Requires Verification

**Finding ID:** CT-002

**Affected asset:** `cloudstart-private-73921`

**Observation:** Management event settings were completed, but S3 data event capture has not yet been confirmed.

**Potential impact:** Without the appropriate data event selectors, object-level operations may not appear in the CloudTrail logs, reducing visibility into file access and modification.

**Recommendation:** Enable read and write S3 data events for the private documents bucket, perform controlled test operations, and confirm that the expected records are delivered.

**Priority:** Medium to high, depending on the sensitivity of the documents and the organization's monitoring requirements.

### Finding 4: Logs Bucket Protection Requires Further Review

**Finding ID:** CT-004

**Affected asset:** `cloudstart-logs-73921`

**Observation:** A dedicated bucket was created for CloudTrail logs. The complete access-control and retention configuration has not been documented.

**Potential impact:** Unauthorized modification or deletion of logs could weaken incident investigation and audit capabilities.

**Recommendation:** Verify Block Public Access, default encryption, restrictive IAM and bucket policies, versioning, and the relevant retention requirements. Consider additional protections for log integrity where appropriate.

**Priority:** Medium to high, depending on the role of the logs in the organization's security and compliance program.

---

## 10. Recommended Hardening Plan

The following activities would strengthen the environment beyond the initial assessment.

| Priority | Action | Expected outcome |
|---|---|---|
| 1 | Confirm Block Public Access on all three buckets | Reduce the risk of accidental public exposure |
| 2 | Review IAM identity policies and group memberships | Establish least-privilege access |
| 3 | Complete the direct IAM access test | Confirm whether the test identity can access private objects |
| 4 | Enable versioning on the private documents bucket | Improve recovery from accidental overwrites and deletions |
| 5 | Verify S3 data event selectors for the private documents bucket | Improve visibility into object-level activity |
| 6 | Confirm CloudTrail log delivery and inspect event records | Demonstrate that the logging configuration works |
| 7 | Review the logs bucket's access controls | Reduce the risk of unauthorized access to audit records |
| 8 | Consider CloudFront with Origin Access Control for the website | Serve website content without requiring direct public access to the S3 bucket |
| 9 | Review log retention and integrity requirements | Improve the usefulness and protection of audit evidence |
| 10 | Document the final configuration and retest | Produce reproducible assessment evidence |

---

## 11. Framework Alignment

This project provides a practical starting point for mapping technical controls to established cybersecurity frameworks.

The following mappings are indicative. They are not a declaration of compliance or certification.

### NIST Cybersecurity Framework 2.0

| Function | Relevance to the project |
|---|---|
| Govern | Establish ownership and requirements for cloud storage security |
| Identify | Identify S3 buckets, their purposes, and the data they contain |
| Protect | Configure access controls, encryption, and appropriate recovery mechanisms |
| Detect | Use CloudTrail to record and investigate relevant AWS activity |
| Respond | Use audit records to support investigation of suspicious activity |
| Recover | Use versioning and appropriate recovery procedures to improve resilience |

### ISO/IEC 27001:2022

The project can also support practical discussion of selected ISO/IEC 27001:2022 control themes, including:

- Access control and identity management.
- Information access restriction.
- Information backup and recovery.
- Logging and monitoring.
- Protection of information in storage.
- Configuration management.

A formal ISO/IEC 27001 assessment would require reviewing the organization's information security management system, risk treatment, documented processes, and the applicability of relevant controls.

---

## 12. Evidence and Repository Structure

I recommend organizing the GitHub repository as follows:

```text
aws-s3-cloud-security-assessment/
│
├── README.md
├── documentation.md
│
└── evidence/
    ├──├── enabling-web-staticwbhosting.png
    │   ├── accessing-the-endpointpng
    │   ├── baselineSecurity.png
    │   └── suspended-bucket-versioning.png
    │
    ├── ├── enabled-bucket-versioning.png
    │   ├── private-document-denied.png
    │   ├── CloudTrail-enabled.png
    │   ├── CloudTrail-management-event.png
    │   └── anonymous-access-denied.png
    │
    ├── ├── iamusercannotacess-bucket.png
    │   └── confirming-cloudTraillog.png
    │
    ├── ├── management-event-settings.png
    │   └── s3-data-event-settings.png
    │
    └── ├── test-user-permissions.png
        └── access-test-results.png
```

---

## 13. Lessons Learned

This project provided practical experience with several cloud security concepts.

### Separation of Storage Responsibilities

Using separate buckets for website content, private documents, and security logs makes it easier to apply different access controls and monitoring requirements to each resource.

### Preventive Access Controls

S3 Block Public Access provides an important layer of protection against unintended public exposure. However, a complete access review must also consider IAM policies and other applicable authorization mechanisms.

### Authentication and Authorization Are Different

An IAM identity's inability to locate a bucket does not necessarily prove that every access request would be denied. Listing a bucket, listing its objects, and retrieving an object are separate operations with distinct permissions.

### Logging Must Be Tested

Creating a CloudTrail trail is only part of the process. The relevant events must be enabled, delivered, and inspected before the logging configuration can be considered validated.

### Recovery Controls Matter

Bucket versioning can help recover from accidental changes and deletions. It should be evaluated alongside backup, retention, and access-control requirements.

---

## 14. Conclusion

This project established a basic AWS S3 environment with separate buckets for website hosting, private document storage, and security logging.

The initial assessment confirmed that the four Block Public Access settings were enabled for the website and private document buckets, default encryption was enabled on both buckets, and anonymous access to the tested private document was denied.

The assessment also identified that versioning was disabled on the private documents bucket and that the IAM access test required further validation. CloudTrail management event settings were completed, while the capture and delivery of S3 object-level data events remain verification tasks.

The next stage is to complete the outstanding checks, implement appropriate hardening measures, collect evidence, and update the assessment results to reflect the verified final configuration.

This project demonstrates an applied understanding of AWS storage security, access-control testing, cloud audit logging, and security assessment documentation.
