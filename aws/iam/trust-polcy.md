</br>
</br>

---


![image](https://github.com/user-attachments/assets/fa2e8987-75f7-4b12-98c7-ed4fbd18757e)


---


</br>
</br>

An IAM trust policy is a resource-based policy attached to an IAM role. It defines which principal(s) (AWS accounts, IAM users, IAM roles, and AWS services) are allowed to assume that role. In essence, it establishes the "trust relationship" that allows entities to obtain temporary security credentials by assuming the role.

Here are a few common examples of IAM trust policies:

**Example 1: Allowing an IAM User in the Same AWS Account to Assume the Role:**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:user/my-developer"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

* **`Version`**: Specifies the version of the IAM policy language. `2012-10-17` is the latest version.
* **`Statement`**: An array of one or more statements that define the permissions.
* **`Effect`**: Can be either `"Allow"` or `"Deny"`. In a trust policy, it's almost always `"Allow"` to grant permission to assume the role.
* **`Principal`**: Specifies the entity or entities that are allowed to assume the role.
    * **`AWS`**: Used to specify AWS account IDs or ARNs of IAM users or roles. In this case, it allows the IAM user named `my-developer` within the AWS account `123456789012` to assume this role.
* **`Action`**: Specifies the AWS action(s) that the principal is allowed to perform on this resource (the IAM role itself). For trust policies, the standard action is `sts:AssumeRole`.

**Example 2: Allowing an IAM Role in a Different AWS Account to Assume the Role (Cross-Account Access):**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::987654321098:role/service-role"
      },
      "Action": "sts:AssumeRole",
      "Condition": {
        "StringEquals": {
          "sts:ExternalId": "unique-session-identifier"
        }
      }
    }
  ]
}
```

* This policy allows the IAM role named `service-role` in the AWS account `987654321098` to assume this role.
* **`Condition`**: This optional element adds further restrictions on when the permission is granted.
    * **`StringEquals`**: A condition operator that checks for an exact string match.
    * **`sts:ExternalId`**: A parameter that can be passed during the `AssumeRole` API call. Using `sts:ExternalId` is a best practice for cross-account access to prevent the "confused deputy" problem. The trusting account (the one with this trust policy) expects a specific `ExternalId` value when the principal from the trusted account attempts to assume the role.

**Example 3: Allowing an AWS Service to Assume the Role (Service-Linked Role Trust Policy - Simplified):**

Many AWS services need permissions to perform actions on your behalf. Service-linked roles are a specific type of IAM role that is pre-defined and managed by an AWS service. Their trust policies typically allow only that specific service to assume the role.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

* **`Principal`**:
    * **`Service`**: Used to specify an AWS service as the principal. In this case, it allows the Amazon EC2 service (`ec2.amazonaws.com`) to assume this role. This is a common pattern for service-linked roles that allow EC2 instances to interact with other AWS services.

**Key Takeaways about IAM Trust Policies:**

* They are attached to IAM *roles*.
* They define *who* is allowed to assume the role (the `Principal`).
* The primary `Action` is almost always `sts:AssumeRole`.
* They can include `Condition` elements to further restrict when the role can be assumed, enhancing security.
* Trust policies are fundamental for enabling delegation of permissions in AWS.

When you create an IAM role, you must define its trust policy to specify which entities are trusted to assume that role and gain the permissions associated with it through its identity-based policies.
