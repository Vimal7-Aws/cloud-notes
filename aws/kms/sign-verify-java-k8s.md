To use **AWS KMS for signing and verifying data from a Kubernetes application**, you'll need to integrate the **AWS SDK** (e.g., Java) into your app running in a Kubernetes pod, and ensure it can authenticate to AWS (via IAM roles, service accounts, etc.).

---

## ✅ Overview

1. **Provision an asymmetric KMS key** (`SIGN_VERIFY`).
2. **Configure IAM permissions** for your pod.
3. **Deploy your app in Kubernetes** with access to AWS.
4. **Use AWS SDK in your app** to sign and verify.

---

## 📌 Step-by-Step Kubernetes Setup

### 🔐 1. Create KMS Asymmetric Key

Use RSA or ECC key with `SIGN_VERIFY` usage:

```bash
aws kms create-key \
  --description "Asymmetric key for K8s signing" \
  --key-usage SIGN_VERIFY \
  --customer-master-key-spec RSA_2048
```

Save the Key ID or ARN.

---

### 🔑 2. Create IAM Policy

Create a policy that allows KMS signing and verification:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "kms:Sign",
        "kms:Verify",
        "kms:GetPublicKey"
      ],
      "Resource": "arn:aws:kms:<region>:<account-id>:key/<key-id>"
    }
  ]
}
```

---

### 🎭 3. Attach IAM Role to Kubernetes Pod (EKS Example)

If you're using **Amazon EKS**, use **IAM Roles for Service Accounts (IRSA)**:

```bash
eksctl create iamserviceaccount \
  --name kms-sa \
  --namespace default \
  --cluster my-cluster \
  --attach-policy-arn arn:aws:iam::<account-id>:policy/KmsSignVerifyPolicy \
  --approve
```

In your Kubernetes deployment:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: kms-sa
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::<account-id>:role/<role-name>
```

---

### 🚀 4. Deploy Java App with AWS SDK in Kubernetes

**Java code** (same as previous answer for sign/verify) will work inside a pod.

**Example Deployment YAML:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: kms-sign-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kms-sign-app
  template:
    metadata:
      labels:
        app: kms-sign-app
    spec:
      serviceAccountName: kms-sa
      containers:
      - name: app
        image: your-dockerhub-user/kms-sign-app:latest
        env:
        - name: AWS_REGION
          value: us-west-2
```

---

### 🧪 5. Verify It's Working

Inside the pod:

```bash
curl localhost:8080/sign      # assuming your app has a REST endpoint
curl localhost:8080/verify
```

Make sure logs or responses confirm signature validity.

---

## 🛡️ Security Notes

* Always hash the message before signing.
* Never expose your private key (KMS doesn't allow that).
* Rotate keys if needed.

---

Would you like a full working example of a Kubernetes-ready Java REST API that signs/verifies messages using AWS KMS?
