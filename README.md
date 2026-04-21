# AWS-IAM-S3-Security-Lab
AWS IAM identity-based and resource-based policies lab using S3 bucket policies and IAM roles
# AWS IAM and S3 Security Lab 

## What This Lab Is About
This lab walks through setting up access controls in AWS 
using IAM policies and S3 bucket policies. The goal was to 
understand how permissions actually work when you combine 
user policies, role policies, and resource-based policies 
together.

---

## What I Worked With
- 3 S3 buckets with different access levels
- An IAM user called devuser with limited permissions
- An IAM role called BucketsAccessRole with specific S3 access
- A developer group policy that controls what devuser can do

---

## What I Did

| Task | What Happened |
|------|--------------|
| Task 1 | Logged in as devuser and explored the console |
| Task 2 | Tested what devuser could and could not access |
| Task 3 | Read through the DeveloperGroupPolicy to understand why |
| Task 4 | Tried writing to S3 -- some worked, some did not |
| Task 5 | Switched to BucketsAccessRole and retested everything |
| Task 6 | Looked at how bucket2 had its own access policy |
| Challenge | Got a file into bucket3 by figuring out its bucket policy |

---

## What I Learned
- IAM user policies and bucket policies can work together 
  or against each other depending on how they are set up
- Switching to a role completely changes what you can do 
  in the console -- permissions do not stack, they switch
- CloudTrail logs every single action whether it succeeds 
  or fails, which matters a lot for security auditing
- A bucket policy can grant access to a role even when 
  that role has no explicit permission for that bucket

---

## Screenshots
All screenshots are in the `/screenshots` folder showing 
each step as I completed it.

---

## AWS Services Used
- AWS IAM
- Amazon S3
- AWS CloudTrail
- AWS STS (Security Token Service)
