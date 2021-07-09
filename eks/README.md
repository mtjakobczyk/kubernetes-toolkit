Dirty hands-on step by step instructions to run EKS:

1. Prepare IAM Policy that allows access to EKS and ECR
    ```hcl
    data "aws_iam_policy_document" "EKSFullAccess" {
      statement {
        sid = "EKSFullAccess"
        actions = [
          "eks:*",
          "ecr:*"
        ]
        resources = [
          "*"
        ]
      }
    }
    resource "aws_iam_policy" "EKSFullAccess" {
      name = "EKSFullAccess"
      description = "Full access to EKS and ECR resources"
      path = "/"
      policy = data.aws_iam_policy_document.EKSFullAccess.json
    }
    ```
