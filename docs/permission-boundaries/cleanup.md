# Permission Boundaries Round <small>CLEANUP phase</small>

It's now time to **Cleanup** the resources you created from the **Build** and **Verify** phases.

To cleanup you need to do the following:

1. Empty the S3 bucket
2. Delete the CloudFormation Stack
3. Delete the Lambda functions
4. Delete the IAM user
5. Delete the IAM roles
6. Delete the IAM policies

!!! Attention
	Please switch back to the original web browser (or private browsing session) you used to login to your AWS account as a **full administrator** and don't use the **`webadmin`** user for any of the cleanup tasks.

## Task 1 <small>Empty S3 Bucket</small>

1. Browse to the [S3 console](https://console.aws.amazon.com/s3/home?region=us-east-2).
2. Click **Buckets** on the left menu.
3. Select the S3 Bucket named **`web-admins-<ACCOUNT_ID>-us-east-2-data`** and click **Empty** to empty the bucket's contents.
3. When prompted, enter the S3 bucket name, **`web-admins-<ACCOUNT_ID>-us-east-2-data`**, and click **Confirm** to confirm the deletion of objects.

## Task 2 <small>Delete CloudFormation Stack</small>

1. Browse to the [CloudFormation console](https://console.aws.amazon.com/cloudformation/home?region=us-east-2).
2. Click **Stacks** on the left menu and delete the CloudFormation stack named **`Perm-Bound`** (Or, whatever you named it in the **Build** phase).
3. You can refresh the CloudFormation console to see the stack being deleted. Before moving on, make sure the stack is in a **`DELETE_COMPLETE`** status. You will need to change the **Stacks** *filter* from **Active** to **Deleted** once it has finished being deleted to see it.

	??? Attention "CloudFormation Stack in **`DELETE_FAILED`** Status"
	
		If the CloudFormation stack fails to completely delete due to an S3 Bucket resource being non-empty and is in the **`DELETE_FAILED`** status, then you can enable the option to delete the stack whilst retaining the S3 bucket and then manually remove it via the S3 Console:
		
		1. Follow the steps in the [Delete a stack with a dependent object or other dependencies that cannot be deleted](https://aws.amazon.com/premiumsupport/knowledge-center/cloudformation-stack-delete-failed/) section to delete the stack.
		2. Browse to the [S3 console](https://console.aws.amazon.com/s3/home?region=us-east-2).
		3. Click **Buckets** on the left menu.
		4. Select the S3 Bucket named **`web-admins-<ACCOUNT_ID>-us-east-2-data`** and click **Delete** to delete the bucket (and empty the bucket's contents).
		5. When prompted, enter the S3 bucket name, **`web-admins-<ACCOUNT_ID>-us-east-2-data`**, and click **Confirm** to confirm the deletion of the bucket.

## Task 3 <small>Delete Lambda function(s)</small>

1. Browse to the [Lambda console](https://console.aws.amazon.com/lambda/home?region=us-east-2region#/functions).
2. Click **Functions** on the left menu and delete any Lambda functions created in the **Verify** phase by selecting each Lambda function and choosing **Delete** from the **Actions** menu.

## Task 4 <small>Delete IAM entities</small>

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home).
2. Click **Users** on the left menu and delete the IAM user named **`webadmin`**.
3. Click **Roles** on the left menu and delete any IAM roles created in the **Verify** phase.
4. Click **Policies** on the left menu and delete the IAM policy named **`webadminspermissionpolicy`**.
5. Click **Policies** on the left menu and delete the IAM policy named **`webadminpermissionboundary`**.
6. Click **Policies** on the left menu and delete any IAM policies created in the **Verify** phase.
