# Permission Boundaries Round <small>CLEANUP phase</small>

It's now time to **Cleanup** the resources you created from the **Build** and **Verify** phases.

To cleanup you need to do the following:

1. Empty the S3 bucket
2. Delete the CloudFormation Stack
3. Delete the Lambda functions
4. Delete the IAM user
5. Delete the IAM roles
6. Delete the IAM policies

## Task 1 <small>Empty S3 Bucket</small>

1. Browse to the [S3 console](https://console.aws.amazon.com/s3/home?region=us-east-2).
2. Click **Buckets** on the left menu.
3. Select the S3 Bucket named **`web-admins-<ACCOUNT_ID>-us-east-2-data`** and click **Empty** to empty the bucket's contents.
3. When prompted, enter the S3 bucket name, **`web-admins-<ACCOUNT_ID>-us-east-2-data`**, and click **Confirm** to confirm the deletion of objects.

## Task 2 <small>Delete CloudFormation Stack</small>

1. Browse to the [CloudFormation console](https://console.aws.amazon.com/cloudformation/home?region=us-east-2).
2. Click **Stacks** on the left menu and delete the CloudFormation stack named **`Perm-Bound`** (Or, whatever you named it in the **Build** phase).

## Task 3 <small>Delete Lambda function(s)</small>

1. Browse to the [Lambda console](https://console.aws.amazon.com/lambda/home?region=us-east-2region#/functions).
2. Click **Functions** on the left menu and delete any Lambda functions created in the **Verify** phase.

## Task 4 <small>Delete IAM entities</small>

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home).
2. Click **Users** on the left menu and delete the IAM user named **`webadmin`**.
3. Click **Roles** on the left menu and delete any IAM roles created in the **Verify** phase.
4. Click **Policies** on the left menu and delete the IAM policy named **`webadminspermissionpolicy`**.
5. Click **Policies** on the left menu and delete the IAM policy named **`webadminpermissionboundary`**.
6. Click **Policies** on the left menu and delete any IAM policies created in the **Verify** phase.
