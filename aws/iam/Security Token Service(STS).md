# AWS Security Token Service (STS)

AWS Security Token Service (STS) is a web service that enables you to request temporary, limited privilege credentials for IAM Users or Federated Users. STS is the service that is a building block of many identity processes within AWS. If you have used an IAM role, then you are already using the service provided by STS without necessarily being aware of it.

STS generates temporary IAM credentials whenever the `sts:AssumeRole*` operation is used. Whenever you assume an IAM role, you use the `sts:AssumeRole*` call, and in doing so, you gain access to IAM temporary credentials, which can be used by the identity that assumes the role.

When you perform a role switch in the UI, you are assuming the role in another AWS account and using STS to gain access to these temporary credentials. This happens behind the scenes, and you don’t see it within the UI.

It generates `ACCESS KEY` and `SECRET ACCESS KEY`, but they will expire and don’t belong to the identity (e.g., an IAM user has access/secret key, also known as long-term credentials) that assumes the role.

Requested credentials can be accessed by another identity that can be AWS or an external identity (for example, Google or Facebook, also known as Web Federation Identity).

**Benefits:**

* No need to embed tokens in the code, which reduces the risk of your credentials being compromised.
* Limited Lifetime (15 minutes – 1 and 1/2 days (36 hours)). So you don’t have to rotate them or explicitly revoke them.

**How STS Works:**

1.  We start with the user who wants to assume a role, and that’s why STS is involved.

2.  Around the role, we have a **trust policy**. A trust policy controls who can assume that role. Conceptually, it’s a wall that will only allow certain people to assume this role. So, whatever the trust policy allows, only that group of people can assume the role.

3.  The next step is to **assume the role** by calling `sts:AssumeRole*`. This call needs to originate from the existing identity, either AWS or an external federation.

4.  The next step is to read the **permissions policy** attached to the role. The permission policy controls what permissions are granted when anyone assumes the role (what is allowed or denied inside AWS).

5.  STS uses the **permission policy** to generate **temporary credentials**. Temporary credentials can access AWS resources until expiration. Access is authorized based on the permission policy.

6.  Temporary credentials are generated when a role is assumed, and they are returned to the identity who assumes the role. When credentials expire, another assume role call is needed to access new credentials.

**Temporary credentials include:**

* **AccessKeyId**: This is the unique ID of the credentials.
* **Expiration**: This denotes the date and time when credentials will expire.
* **Secret Access Key**: This key is used to sign requests to AWS.
* **Session Token**: This is a unique token that must be passed with all requests.

**Use Case:**

* If you assume a role within an AWS account.
* Role switch between AWS accounts using the console UI. You use STS.
* Cross-account access using a role.

At this stage, temporary credentials are generated and will be returned to the identity who assumes the role. Once the credentials expire, another `sts:AssumeRole*` call is needed to get access to new credentials.

**NOTE:** Temporary credentials can’t be canceled. You can’t manually invalidate credentials.

If you want to check these temporary credentials, log in to the EC2 instance, which assumes the IAM role `s3_full_access_role`, and access its metadata service (`169.254.169.254`) via the `curl` command. Check this doc for more info: [https://docs.aws.amazon.com/neptune/latest/userguide/iam-auth-temporary-credentials.html](https://docs.aws.amazon.com/neptune/latest/userguide/iam-auth-temporary-credentials.html)

```bash
$ curl [http://169.254.169.254/latest/meta-data/iam/security-credentials/](http://169.254.169.254/latest/meta-data/iam/security-credentials/)
s3_full_access_role

$ curl [http://169.254.169.254/latest/meta-data/iam/security-credentials/s3_full_access_role](http://169.254.169.254/latest/meta-data/iam/security-credentials/s3_full_access_role)
{
"Code" : "Success",
"LastUpdated" : "2022–08–16T22:57:12Z",
"Type" : "AWS-HMAC",
"AccessKeyId" : "ASIAXXXXXXXXXX",
"SecretAccessKey" : "mdsTXXXXXXXXXX",
"Token" : "IQoJbXXXXXXXX"
"Expiration" : "2022–11–19T05:32:30Z"
```

Now let's deep dive in and see the practical demonstration and assume the IAM role on the command line.

1.  **Create an IAM user named Vimal.**
    ```bash
    aws iam create-user --user-name Vimal
    ```

2.  **Create an IAM policy that grants user Vimal access to assume a role.** The contents of the `assume-role-policy.json` file are similar to this:
    ```json
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Action": [
            "sts:AssumeRole"
          ],
          "Resource": "*"
        }
      ]
    }
    ```

3.  **To create a policy, use the `aws iam create-policy` command:**
    ```bash
    aws iam create-policy --policy-name assume-role-policy --policy-document file://assume-role-policy.json
    ```
    Note down the ARN (Amazon Resource Name) of the IAM policy:
    ```
    arn:aws:iam::1234567890:policy/assume-role-policy
    ```

4.  **Attach this policy to user Vimal:**
    ```bash
    aws iam attach-user-policy --user-name Vimal --policy-arn "arn:aws:iam::1234567890:policy/assume-role-policy"
    ```

5.  **To review the policy attached to the user, run the below command:**
    ```bash
    aws iam list-attached-user-policies --user-name Vimal
    ```

6.  **Create a JSON file `assume-role-trust-policy.json` that defines the trust relationship:**
    ```json
    {
      "Version": "2012-10-17",
      "Statement": {
        "Effect": "Allow",
        "Principal": {
          "AWS": "arn:aws:iam::1234567890:user/Vimal"
        },
        "Action": "sts:AssumeRole"
      }
    }
    ```
    **NOTE:** This trust policy allows the user “`arn:aws:iam::1234567890:user/Vimal`” to assume this role if they allow the `sts:AssumeRole` action in their permissions policy.

7.  **Create the IAM role that user Vimal can assume and has read-only access to the S3 bucket.**
    ```bash
    # Create an IAM Role
    aws iam create-role --role-name assume-trust-role --assume-role-policy-document file://assume-role-trust-policy.json

    # Attach S3 read-only access policy to the role
    aws iam attach-role-policy --role-name assume-trust-role --policy-arn "arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess"

    # List the attached role policies
    aws iam list-attached-role-policies --role-name  assume-trust-role
    ```

8.  **Create the access key for the user:**
    ```bash
    aws iam create-access-key --user-name Vimal
    ```

9.  **Configure the access key:**
    ```bash
    aws configure
    AWS Access Key ID [****************PFEC]: XXXXXXXX
    AWS Secret Access Key [****************tHBh]: pYUgRXXXXXXXX
    Default region name [us-east-1]:
    Default output format [json]:
    ```

10. **Run the `aws sts get-caller-identity` command:**
    ```bash
    aws sts get-caller-identity
    ```
    Output:
    ```json
    {
        "UserId": "AIDAXDUKP5NXAKOJSKU2U",
        "Account": "1234567890",
        "Arn": "arn:aws:iam::488842980206:user/Vimal"
    }
    ```

11. **Try to list S3 buckets:**
    ```bash
    aws s3 ls
    ```
    Output (expected Access Denied):
    ```
    An error occurred (AccessDenied) when calling the ListBuckets operation: Access Denied
    ```

12. **Assume the IAM role:**
    ```bash
    aws sts assume-role --role-arn "arn:aws:iam::123456789012:role/assume-trust-role" --role-session-name my-demo-role
    ```
    This command will output several pieces of information. You can either manually export `AccessKeyId`, `SecretAccessKey`, and `SessionToken`:
    ```bash
    export AWS_ACCESS_KEY_ID=RoleAccessKeyID
    export AWS_SECRET_ACCESS_KEY=RoleSecretKey
    export AWS_SESSION_TOKEN=RoleSessionToken
    ```
    Or use the below script to automate the process:
    ```bash
    OUT=$(aws sts assume-role --role-arn arn:aws:iam::123456789012:role/assume-trust-role --role-session-name my-demo-role); \
    export AWS_ACCESS_KEY_ID=$(echo "$OUT" | jq -r '.Credentials''.AccessKeyId'); \
    export AWS_SECRET_ACCESS_KEY=$(echo "$OUT" | jq -r '.Credentials''.SecretAccessKey'); \
    export AWS_SESSION_TOKEN=$(echo "$OUT" | jq -r '.Credentials''.SessionToken');
    ```

13. **Now try to list S3 buckets again:**
    ```bash
    aws s3 ls
    ```
    Output (should now succeed):
    ```
    mytests3bucket
    ```

14. **To return to the IAM user, remove the environment variables:**
    ```bash
    unset AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY AWS_SESSION_TOKEN
    ```
```
