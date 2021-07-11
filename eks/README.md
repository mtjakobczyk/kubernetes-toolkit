Dirty hands-on step by step instructions to run EKS:

1. Create a new customer-managed **IAM Policy** that allows full access to EKS and ECR
    ```json
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Sid": "EKSFullAccess",
          "Effect": "Allow",
          "Action": [
              "eks:*",
              "ecr:*"
          ],
          "Resource": "*"
        }
      ]
    }
    ```
    Save the policy document as `eksfa.json` and execute:
    ```bash
    aws iam create-policy --policy-name "EKSFullAccess" --policy-document file://eksfa.json
    ```
2. Create **IAM Group** and attach to it the relevant IAM policies
    ```bash
    GROUP_NAME=BemowoDevOpsSquad
    aws iam create-group --group-name $GROUP_NAME
    POLICIES=(
        'AmazonEC2FullAccess' 
        'AmazonS3FullAccess' 
        'AmazonVPCFullAccess' 
        'AmazonSNSReadOnlyAccess'
        'AWSCloudFormationFullAccess'
        'IAMReadOnlyAccess' 
        'EKSFullAccess'
    )
    for policy in "${POLICIES[@]}";
    do
        ARN=$(aws iam list-policies --query "Policies[?PolicyName=='$policy'].Arn" --output text)
        aws iam attach-group-policy --group-name $GROUP_NAME --policy-arn $ARN
    done
    ```
3. Create **IAM Users** and add them to the IAM Group
    You need to install the `pwgen` utility to run the commands below.
    ```bash
    GROUP_NAME=BemowoDevOpsSquad
    USERS=('devops1' 'devops2')
    for user in "${USERS[@]}";
    do
        aws iam create-user --user-name $user
        aws iam add-user-to-group --group-name $GROUP_NAME --user-name $user
        TMP_PASS=$(pwgen -Bcnsy 14 1)
        aws iam create-login-profile --user-name $user --password "$TMP_PASS" --password-reset-required
        echo "$user -> $TMP_PASS"
    done
    ```
    You may need to double check the current password policy for your account before you ask the team to change their passwords on the first login:
    ```bash
    aws iam get-account-password-policy
    ```
