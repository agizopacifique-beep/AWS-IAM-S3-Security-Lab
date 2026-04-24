# AWS IAM and S3 Security Project

![AWS](https://img.shields.io/badge/AWS-IAM-orange)
![S3](https://img.shields.io/badge/Amazon-S3-blue)
![Status](https://img.shields.io/badge/Lab-Completed-success)
![CloudTrail](https://img.shields.io/badge/AWS-CloudTrail-yellow)

---

## Overview

This lab focuses on how AWS IAM identity-based policies and 
S3 resource-based bucket policies work together to control 
access. The core question the lab answers: what happens when 
a user switches to a role that has different permissions than 
their own account? The answer turns out to be more nuanced 
than expected.

The environment had three S3 buckets, one IAM user, one IAM 
role, and a mix of policies that either allowed or blocked 
access depending on how you were logged in.

---

## Lab Architecture

### Starting Architecture
AWS Account
│
├── IAM
│   ├── devuser (IAM User)
│   │   └── Member of DeveloperGroup
│   │       └── DeveloperGroupPolicy (attached)
│   │
│   └── BucketsAccessRole (IAM Role)
│       ├── ListAllBucketsPolicy
│       ├── GrantBucket1Access
│       └── Trust Policy (trusts devuser)
│
└── Amazon S3
├── bucket1 (objects inside: Image2.jpg)
├── bucket2 (resource-based bucket policy attached)
└── bucket3 (resource-based bucket policy attached)

### How Access Worked

| Who Is Logged In | bucket1 | bucket2 | bucket3 |
|-----------------|---------|---------|---------|
| devuser (no role) | No access | No access | No access |
| BucketsAccessRole | Read access | Read and write | Depends on bucket policy |

The interesting part was bucket2 and bucket3. The role itself 
had no explicit permission for either bucket, but the bucket 
policies on both granted access to the role directly. That 
combination is what made the uploads work.

---

## AWS Services Used

| Service | What It Did in This Lab |
|---------|------------------------|
| AWS IAM | Controlled user and role permissions |
| Amazon S3 | Stored objects across three buckets |
| AWS STS | Issued temporary credentials when switching roles |
| AWS CloudTrail | Logged every API call made during the lab |

---

## Tasks Completed

### Task 1 -- Logged In as devuser
Accessed the AWS console through a custom IAM login URL 
instead of the root account. This is how most real-world 
AWS users access the console.

### Task 2 -- Tested What devuser Could Access
Tried opening EC2 and S3. EC2 was blocked entirely. S3 
showed the buckets but showed "Insufficient permissions" 
for everything inside them.

### Task 3 -- Read Through the DeveloperGroupPolicy
Opened the actual JSON policy attached to DeveloperGroup. 
It allowed some S3 bucket actions but nothing on objects 
inside buckets. That explained why the S3 console loaded 
but downloads failed.

### Task 4 -- Tested Write Access to S3
Created a new S3 bucket -- that worked because the policy 
allowed s3:CreateBucket. Tried uploading a file to it and 
got Access Denied. The policy had no s3:PutObject 
permission.

### Task 5 -- Switched to BucketsAccessRole
Used the Switch Role feature to assume BucketsAccessRole. 
Downloaded Image2.jpg from bucket1 successfully. Then 
tested IAM access and got blocked -- the role had no IAM 
read permissions at all. Switching roles does not stack 
permissions; it completely replaces them.

### Task 6 -- Checked bucket2's Bucket Policy
Found two policy statements on bucket2. Both pointed to 
BucketsAccessRole as the principal. One allowed 
s3:GetObject and s3:PutObject. The other allowed 
s3:ListBucket. That bucket policy is what made the 
upload work, not the role's own policies.

### Challenge -- Got a File Into bucket3
Figured out bucket3 also had a bucket policy granting 
BucketsAccessRole access. Assumed the role and uploaded 
Image2.jpg successfully.

---

## Key Findings

The thing that surprised me most was how bucket2 worked. 
The role had no permission for bucket2 in its own attached 
policies, but the bucket policy on bucket2 granted access 
anyway. AWS evaluates both sides -- the caller's policies 
and the resource's policies -- and if either side grants 
access without the other side denying it, the action goes 
through.

A few other things worth noting:

- Every failed and successful action showed up in 
  CloudTrail. In a real environment that logging is how 
  you would catch someone doing something they should not.

- The trust policy on BucketsAccessRole explicitly named 
  devuser as a trusted entity. Without that, the Switch 
  Role would have failed completely.

- S3 bucket policies use the same JSON structure as IAM 
  policies but attach to the bucket instead of the user 
  or role. Both need to agree for access to work.

---

## Screenshots

All screenshots from the lab are in the `/screenshots` 
folder. Here is what each one shows:

| File | What It Shows |
|------|--------------|
| `01-logged-in-as-devuser.png` | Console with devuser shown in top right |
| `02-ec2-access-denied.png` | EC2 dashboard blocked for devuser |
| `03-s3-insufficient-permissions.png` | S3 bucket list with permission errors |
| `04-developer-group-policy.png` | Full JSON of DeveloperGroupPolicy |
| `05-bucket-created-successfully.png` | New bucket created by devuser |
| `06-upload-access-denied.png` | Upload failure and Access Denied error |
| `07-assumed-bucketsaccessrole.png` | Console showing role switch was successful |
| `08-bucket1-download-success.png` | Image2.jpg downloaded from bucket1 |
| `09-iam-access-denied-with-role.png` | IAM blocked while using BucketsAccessRole |
| `10-grant-bucket1-access-policy.png` | GrantBucket1Access policy JSON |
| `11-trust-relationships.png` | Trust policy showing devuser as trusted entity |
| `12-bucket2-upload-success.png` | Successful upload to bucket2 |
| `13-bucket2-resource-policy.png` | bucket2 bucket policy with both statements |
| `14-bucket3-policy.png` | bucket3 bucket policy |
| `15-bucket3-upload-success.png` | Successful upload to bucket3 |
| `16-lab-completion-score.png` | Final submission report and score |
Added accurate descriptions for all five lab architecture
diagrams based on lab analysis covering starting setup,
final architecture, bucket access findings, role assumption
flow and how IAM and resource-based policies determined
access decisions throughout the lab.
## Architecture Diagrams

### 1. Starting Architecture
![Starting Architecture](screenshots/arch-01-starting.png)

This is what the lab environment looked like before anything 
was done. devuser was already set up as an IAM user and placed 
inside DeveloperGroup. That group had DeveloperGroupPolicy 
attached to it. Three S3 buckets and an EC2 service were 
already in the environment. The goal was to test what devuser 
could actually do with those resources.

---

### 2. Final Architecture
![Final Architecture](screenshots/arch-02-final.png)

By the end of the lab devuser could assume BucketsAccessRole 
using AWS STS. That role assumption changed what was accessible 
across all three S3 buckets. The architecture looks similar to 
the start but the key difference is that devuser now has a path 
to assume a role and reach resources that were previously 
blocked.

---

### 3. Bucket Access Analysis -- What devuser Could and Could Not Do
![Bucket Analysis](screenshots/arch-03-bucket-analysis.png)

This diagram breaks down what happened in Tasks 3 and 4. 
devuser could create a brand new S3 bucket because 
DeveloperGroupPolicy allowed s3:CreateBucket. However accessing 
objects inside bucket1 or bucket2 failed completely. The policy 
had no s3:GetObject or s3:PutObject permissions so anything 
inside an existing bucket was off limits.

---

### 4. Role Access Analysis -- Why bucket2 Worked
![Role Analysis](screenshots/arch-04-role-analysis.png)

After assuming BucketsAccessRole things changed. The role had 
GrantBucket1Access attached which gave read access to bucket1 
only. bucket2 had no role-based policy covering it at all. Yet 
uploading to bucket2 worked anyway. This diagram captures that 
moment -- bucket1 access made sense but bucket2 access raised 
the question the lab was building toward.

---

### 5. Policy Decision Flow -- The Full Picture
![Policy Flow](screenshots/arch-05-policy-diagram.png)

This is the diagram that ties everything together. bucket1 was 
accessible because of the role-based policy attached to 
BucketsAccessRole through GrantBucket1Access. bucket2 was 
accessible for a completely different reason -- its own 
resource-based bucket policy named BucketsAccessRole as a 
trusted principal and granted it s3:GetObject, s3:PutObject 
and s3:ListBucket directly. Two different policy types, two 
different paths to access, same end result.
