# AWS Secrets Manager and EC2 IAM Access

## 1. What is needed to authorise your EC2 to retrieve secrets from AWS Secrets Manager?

To allow an EC2 instance to retrieve secrets from AWS Secrets Manager, the following components are required:

1. An IAM Role attached to the EC2 instance.
2. An IAM Policy that grants permission to access the specific secret.
3. The EC2 instance must assume the IAM role automatically through an Instance Profile.
4. The application running on EC2 must use the AWS SDK or AWS CLI to request the secret.

This approach is recommended because it avoids hardcoding AWS credentials directly inside the application or server.

---

# 2. IAM Policy (JSON)

Below is an example IAM policy that allows an EC2 instance to retrieve a secret from AWS Secrets Manager.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "secretsmanager:GetSecretValue"
      ],
      "Resource": "arn:aws:secretsmanager:ap-southeast-1:123456789012:secret:prod/cart-service/credentials-*"
    }
  ]
}
```

---

# 3. Sensible ARN for the Secret

Given the secret name:

```text
prod/cart-service/credentials
```

A sensible ARN (Amazon Resource Name) would be:

```text
arn:aws:secretsmanager:ap-southeast-1:123456789012:secret:prod/cart-service/credentials-abc123
```

## ARN Breakdown

| Component | Description |
|---|---|
| arn | AWS ARN prefix |
| aws | AWS partition |
| secretsmanager | AWS service |
| ap-southeast-1 | AWS region |
| 123456789012 | AWS account ID |
| secret | Resource type |
| prod/cart-service/credentials-abc123 | Secret name with generated suffix |

AWS automatically appends a random suffix to secret names for uniqueness.

---

# 4. Real-World Example

An e-commerce application running on EC2 may need database credentials securely stored in AWS Secrets Manager.

Instead of storing usernames and passwords in:
- source code,
- configuration files,
- or environment variables,

the application retrieves the credentials dynamically at runtime using the IAM role attached to the EC2 instance.

This improves:
- security,
- credential rotation,
- and operational management.

---

# Conclusion

To securely access AWS Secrets Manager from EC2, an IAM role with appropriate permissions must be attached to the instance. The IAM policy should follow the principle of least privilege by restricting access to only the required secret ARN. This approach improves cloud security and avoids exposing sensitive credentials directly within applications or infrastructure.
