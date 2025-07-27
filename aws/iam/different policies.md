
---

</br>
</br>



<img width="878" alt="image" src="https://github.com/user-attachments/assets/01686598-e1ca-4bee-aac1-06519cf2ca44" />


</br>
</br>

---
AWS Identity and Access Management (IAM) policies are JSON documents that define permissions and control access to AWS resources. There are several different types of policies in AWS IAM, each serving a specific purpose:

**1. Identity-Based Policies:**

* These policies are attached to IAM identities: users, groups, or roles.
* They define what actions the identity is allowed to perform on which resources and under what conditions.
* Identity-based policies grant permissions to an IAM principal.
* There are three types of identity-based policies:
    * **AWS Managed Policies:** These are pre-written policies created and maintained by AWS. They cover common use cases and provide a quick way to grant necessary permissions. Examples include `AdministratorAccess`, `ReadOnlyAccess`, `AmazonS3FullAccess`, etc. AWS manages the updates to these policies.
    * **Customer Managed Policies:** These are policies that you create, manage, and update in your AWS account. They offer more granular control over permissions tailored to your specific needs. These policies are reusable and can be attached to multiple IAM identities.
    * **Inline Policies:** These policies are directly embedded within a single IAM user, group, or role. They are useful when the permissions are specific to that identity and are not intended to be reused. Inline policies are deleted if the associated IAM identity is deleted.

**Example of an Identity-Based Policy (Customer Managed):**

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::my-company-bucket/*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeInstances",
                "ec2:StartInstances",
                "ec2:StopInstances"
            ],
            "Resource": "arn:aws:ec2:us-east-1:123456789012:instance/*",
            "Condition": {
                "StringEquals": {
                    "ec2:ResourceTag/Environment": "Development"
                }
            }
        }
    ]
}
```

This policy allows the attached identity to:

* Read any object from the `my-company-bucket` in S3.
* Describe, start, and stop EC2 instances in the `us-east-1` region of the account `123456789012`, but only if the instance has a tag with the key `Environment` and the value `Development`.

**2. Resource-Based Policies:**

* These policies are attached directly to AWS resources, such as S3 buckets, SQS queues, KMS keys, and VPC endpoints.
* They specify which principals (AWS accounts, IAM users, IAM roles, and AWS services) are allowed to access that specific resource and what actions they can perform.
* Resource-based policies grant permissions to the principal specified in the policy.
* The most common type of resource-based policy in IAM itself is the **IAM role trust policy**, which is attached to an IAM role and defines which principals are allowed to assume that role.

**Example of a Resource-Based Policy (S3 Bucket Policy):**

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::987654321098:user/another-account-user"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::public-data-bucket/*"
        }
    ]
}
```

This policy attached to the `public-data-bucket` allows the IAM user `another-account-user` in the AWS account `987654321098` to read any object in that bucket.

**3. Permissions Boundaries:**

* Permissions boundaries are used to set the maximum permissions that an identity-based policy can grant to an IAM entity (user or role).
* They do not grant permissions themselves but rather limit the scope of permissions that identity-based policies can provide.
* When an IAM entity has a permissions boundary, the effective permissions of that entity are the intersection of the permissions granted by its identity-based policies and the permissions allowed by its permissions boundary.
* Permissions boundaries help in delegating permissions management while ensuring that users or roles cannot exceed certain permission limits.

**Example of a Permissions Boundary Policy:**

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:*",
                "ec2:Describe*",
                "ec2:Start*",
                "ec2:Stop*"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Deny",
            "Action": "iam:*",
            "Resource": "*"
        }
    ]
}
```

If this policy is used as a permissions boundary for an IAM user, even if the user has an identity-based policy granting them full IAM access, the permissions boundary will prevent them from performing any IAM actions.

**4. Service Control Policies (SCPs):**

* SCPs are used in AWS Organizations to centrally manage permissions for all accounts within an organization.
* They allow you to define guardrails that specify the maximum permissions for member accounts.
* SCPs affect all IAM users, groups, and roles within the accounts they are applied to, including the root user.
* SCPs can either allow or deny access to AWS services and actions.

**5. Session Policies:**

* Session policies are advanced policies that you pass when you programmatically create temporary credentials for a role or a federated user.
* They limit the permissions that the temporary credentials have for the duration of that session.
* Session policies act as further constraints on the permissions that the assumed role or federated user already has. The effective permissions for the session are the intersection of the session policy and the identity-based policies of the role or federated user.
* Use cases include limiting the scope of access for specific tasks or enhancing security when external identities access your AWS environment.

**Example of a Session Policy (passed during `AssumeRole`):**

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::temp-data-bucket"
        }
    ]
}
```

If an IAM user with broader S3 permissions assumes a role with this session policy, their access during that specific session will be limited to only listing the `temp-data-bucket`.

Understanding these different types of IAM policies and how they interact is crucial for implementing a robust and secure access control system in your AWS environment, following the principle of least privilege.
