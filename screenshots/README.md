# Lab Screenshots

All screenshots and architecture diagrams captured 
during AWS IAM and S3 Security Lab 3.1.

---

## Architecture Diagrams

| File | What It Shows |
|------|--------------|
| `arch-01-starting.png` | Starting lab setup with devuser, DeveloperGroup and 3 S3 buckets |
| `arch-02-final.png` | Final architecture showing devuser assuming BucketsAccessRole |
| `arch-03-bucket-analysis.png` | devuser could create a bucket but could not access objects inside bucket1 or bucket2 |
| `arch-04-role-analysis.png` | After assuming BucketsAccessRole bucket1 was accessible but bucket2 access raised a question |
| `arch-05-policy-diagram.png` | Full breakdown of how role-based and resource-based policies determined access |

---

## Lab Screenshots

| File | What It Shows |
|------|--------------|
| `01-logged-in-as-devuser.png` | Console confirming login as devuser |
| `02-ec2-access-denied.png` | EC2 dashboard blocked for devuser |
| `03-s3-insufficient-permissions.png` | S3 buckets showing insufficient permissions |
| `04-developer-group-policy.png` | Full JSON of DeveloperGroupPolicy |
| `05-bucket-created.png` | New bucket created successfully by devuser |
| `06-upload-access-denied.png` | File upload failed with Access Denied error |
| `07-role-switch.png` | Console confirming BucketsAccessRole assumed |
| `08-bucket1-download.png` | Image2.jpg downloaded from bucket1 |
| `09-iam-denied.png` | IAM blocked while using BucketsAccessRole |
| `10-bucket1-policy.png` | GrantBucket1Access policy JSON |
| `11-trust-policy.png` | Trust policy showing devuser as trusted entity |
| `12-bucket2-upload.png` | Successful upload to bucket2 |
| `13-bucket2-policy.png` | bucket2 resource-based bucket policy |
| `14-bucket3-policy.png` | bucket3 policy showing OtherBucketAccessRole |
| `15-bucket3-upload.png` | Successful upload to bucket3 |
| `16-lab-score.png` | Final submission report and score |
