# Permission boundaries workshop <small> Clean-up phase</small>

It's now time to **Clean-up** the resources you created from the **Build** and **Verify** phases.

To clean-up you need to do the following:

1. Delete the Lambda functions
2. Delete the IAM user
3. Delete the IAM roles
4. Delete the IAM policies
5. Delete the S3 bucket


## Task 1 <small>Delete Lambda function(s)</small>

1. Browse to the [Lambda console](https://console.aws.amazon.com/lambda/home).
2. Click **Functions** on the left menu and delete any Lambda functions created in the **Verify** phase.

## Task 2 <small>Delete IAM entities</small>

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home).
2. Click **Users** on the left menu and delete the IAM user named **`webadmin`**.
3. Click **Roles** on the left menu and delete any IAM roles created in the **Verify** phase.
4. Click **Policies** on the left menu and delete the IAM policy named **`webadminspermissionpolicy`**.
5. Click **Policies** on the left menu and delete the IAM policy named **`webadminpermissionboundary`**.
6. Click **Policies** on the left menu and delete any IAM policies created in the **Verify** phase.

## Task 3 <small>Delete S3 Bucket</small>

1. Browse to the [S3 console](https://console.aws.amazon.com/s3/home).
2. Click **Buckets** on the left menu and delete the S3 bucket named **`webadmins-<Account_ID>-data`**.
