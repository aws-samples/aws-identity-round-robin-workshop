# Permission boundaries workshop <small> Verify phase</small>

It's now time **VERIFY** the setup from the **Build** phase. You will be acting as the webadmins in this phase to check that you can do the following: 

1. Create an IAM policy
2. Create an IAM role (and attach that policy) 
3. Create a Lambda function (and attach that role)

!!! Pre-Verification
    <p style="font-size:14px;">
      In order validate your setup ***before*** swapping credentials with another team, enter the ***account Id for your team*** instead of the other team in the `~/.aws/config` file referenced above for the *role_arn* and proceed with the steps below. The VERIFY phase assumes you are checking another team's setup but for your own testing this will be just done in your own account. You'll need to keep that in mind because some of the VERIFY phase instructions assume you are doing this cross account so you'll need to adjust some of the commands. Once you have confirmed the delegated access is functioning within your team account, update `~/.aws/config` and test against the other team's account.
    </p>

1\. Verify in **your** team's account:

* To carry out the tasks below as the webadmins in **your team's** account, you will need to assume that role. To make that process easier, add the following to the `~/.aws/config` file:

```
[profile webadmins]
role_arn = arn:aws:iam::YOUR_TEAMS_ACCOUNT_ID:role/webadmins
source_profile = default
```

2\. Verify in **other** team's account:

* To carry out the tasks below as the webadmins in the **other team's** account, you will need to assume that role. To make that process easier, add the following to the `~/.aws/config` file:

```
[profile webadmins]
role_arn = arn:aws:iam::ACCOUNT_ID_FROM_OTHER_TEAM:role/webadmins
source_profile = default
```

If doing this as part of an AWS event you should have received the following information from another team. You will need the **Account ID** and the **Resource restriction** information to complete the tasks in this phase.

```
Webadmins role ARN:	arn:aws:iam::`ACCOUNT_ID_FROM_OTHER_TEAM`:role/**webadmins**
Resource restriction for both the roles and policies: /webadmins/`Resource restriction`
Permissions boundary name: **webadminspermissionsboundary**
Permission policy name: **webadminspermissionpolicy**
```

**When using the AWS CLI and you want to reference a profile other then the default one you need to add the `--profile` parameter to the CLI command. Since we are naming this profile webadmins, you will see that `--profile webadmins` has been added to all the commands in this phase.**

??? info "Application architecture"
	
	![image1](./images/architecture.png)

---

!!! Attention
    <p style="font-size:16px;">
      Please keep in mind where to add the AWS Account ID, correctly use pathing and change the region specified if needed (although if you are taking this as part of an AWS event, just use the already specified us-east-1.)
    </p>

## Task 1 <small>Create a policy</small>
	
First you will create a permission policy which just needs to allow log file creation and s3:ListBucket. You are in a hurry though, like many developers, and give the role full S3 permissions. The policy you create here will later be attached to the role you create in Task 2 which will then be passed to a Lambda function you will create in **Task 3**.

* Use the following JSON to create a file named **`verifypolicy.json`**: 
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents",
        "s3:*"
      ],
      "Resource": "*"
    }
  ]
}
```
* Create the policy (**there is a key parameter missing from the command below. Check the <a href="https://docs.aws.amazon.com/cli/latest/reference/" target="_blank"> AWS CLI documentation to determine the missing parameter. </a> **)
```
aws iam create-policy --policy-name NAME_OF_POLICY --policy-document file://verifypolicy.json --profile webadmins
```
<!-- `aws iam create-policy --policy-name NAME_OF_POLICY --path /webadmins/ --policy-document file://verifypolicy.json` -->

??? info "Stuck?"

    This section can be challenging so you are having continued trouble you **may** wish to review the  <a href="../artifacts/pbw-solution-notes.pdf" target="_blank">sample solution commands in the helper doc.</a>

## Task 2 <small>Create a role</small>

The role you create here will be passed to the Lambda function you create in the next task.

* Use the following JSON to create a file named **`verifytrustpolicy.json`**: 
```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Principal": {
      "Service": "lambda.amazonaws.com"
    },
    "Action": "sts:AssumeRole"
  }
}
```
* Create the role (**there is a key parameter missing from the command below. Check the <a href="https://docs.aws.amazon.com/cli/latest/reference/" target="_blank"> AWS CLI documentation to determine the missing parameter. </a> **)
```
aws iam create-role --role-name NAME_OF_ROLE --path /webadmins/ --assume-role-policy-document file://verifytrustpolicy.json --profile webadmins
```
<!-- `aws iam create-role --role-name NAME_OF_ROLE --path /NAME_OF_PATH/ --assume-role-policy-document file://verifytrustpolicy.json --permissions-boundary arn:aws:iam::ACCOUNT_ID_FROM_OTHER_TEAM:policy/webadminspermissionsboundary` -->
* Attach the policy you created in **Task 1** to the role:
```
aws iam attach-role-policy --policy-arn arn:aws:iam::<ACCOUNT_ID_FROM_OTHER_TEAM>:policy/webadmins/NAME_OF_POLICY --role-name NAME_OF_ROLE --profile webadmins
```
		
## Task 3 <small>Create and test a Lambda function</small>

Finally, you will create a **Node.js 8.10** Lambda function using the sample code below and pass the IAM role you just created:
 
* Create a file named **`index.js`** using the code below. Replace `"SHARED_LOGGING_BUCKET_NAME"` with the name of bucket that begins with `"shared-logging-"` and ends in `"-data"`. Also replace `"PREFIX_FROM_PERMISSIONS_BOUNDARY"` with the prefix the permissions boundary requires for that bucket.  In order to find the bucket name, just run `aws s3 ls --profile webadmins`. In order to find the prefix, examine the permissions boundary policy from the **BUILD** phase. 

??? info  "What is an S3 prefix?" 

    <p style="font-size:16px;">
      In Amazon S3, buckets and objects are the primary resources, and objects are stored in buckets. Amazon S3 has a flat structure instead of a hierarchy like you would see in a file system. However, for the sake of organizational simplicity, the Amazon S3 console supports the folder concept as a means of grouping objects. Amazon S3 does this by using a **shared name prefix** for objects (that is, objects that have names that begin with a common string). Object names are also referred to as key names. For example, you can create a folder on the console named photos and store an object named myphoto.jpg in it. The object is then stored with the key name photos/myphoto.jpg, where photos/ is the prefix.
    </p>

``` node
const AWS = require('aws-sdk');
const s3 = new AWS.S3();

exports.handler = async (event) => {
  console.log('Loading function');
  const allKeys = [];
  await getKeys({ Bucket: 'SHARED_LOGGING_BUCKET_NAME' , Prefix: 'PREFIX_FROM_PERMISSIONS_BOUNDARY'}, allKeys);
  return allKeys;
};

async function getKeys(params, keys){
  const response = await s3.listObjectsV2(params).promise();
  response.Contents.forEach(obj => keys.push(obj.Key));

  if (response.IsTruncated) {
    const newParams = Object.assign({}, params);
    newParams.ContinuationToken = response.NextContinuationToken;
    await getKeys(newParams, keys); 
  }
}
```
* Zip the index.js file for upload to Lambda
```
zip lambdafunction.zip index.js
```
* Create a Lambda function
```
aws lambda create-function --function-name verifyfunction --runtime nodejs14.x --role arn:aws:iam::<ACCOUNT_ID_FROM_OTHER_TEAM>:role/webadmins/NAME_OF_ROLE --handler index.handler --region us-east-1 --zip-file fileb://lambdafunction.zip --profile webadmins
```
* Invoke the Lambda function
```
aws lambda invoke --function-name verifyfunction --region us-east-1 --invocation-type RequestResponse outputfile.txt --profile webadmins
```
* Examine the output file. 

If you see files marked that **webadmins/you-should-SEE-this-file--webadmins...** then you have successfully verified that the webadmins can do their job. Also make sure the function is generating logs in CloudWatch logs.

* *(Optional)* Test Lambda for alternate bucket prefix

If time permits, repeat the previous steps in this task except create a new Lambda function such as `verifyfunction1` and set bucket prefix in the new Lambda function code to `appadmins`. What are the contents of the output file when the new Lambda function is invoked? Why is the result different than with the `webadmins` bucket prefix?

Congratulations!

## Task 4 <small>Cleanup</small>

**You do not need to perform cleanup if Event Engine is being used.** If Event Engine is not being used, to cleanup you need to delete the CloudFormation stack named `Perm-Bound-Adv` (this will also remove the Cloud9 stack if that was used in the workshop) and the IAM resources you created. Run these commands using the IAM user or role you used to do the **BUILD** phase.

Resources created in the **VERIFY** phase

* Detach policy from the role created in the **VERIFY** phase:
```
aws iam detach-role-policy --role-name NAME_OF_VERIFY_ROLE --policy-arn arn:aws:iam::<ACCOUNT_ID>:policy/webadmins/NAME_OF_VERIFY_POLICY
```
* Delete policy created in the **VERIFY** phase:
```
aws iam delete-policy --policy-arn arn:aws:iam::<ACCOUNT_ID>:policy/webadmins/NAME_OF_VERIFY_POLICY
```
* Delete role created in **VERIFY** phase:
```
aws iam delete-role --role-name verifyrole
```
* Delete the Lambda function created in **VERIFY** phase:
```
aws lambda delete-function --function-name verifyfunction --region us-east-1
```

Resources created in the **BUILD** phase

* Detach the two policies from the webadmins role created in the **BUILD** phase:
```
aws iam detach-role-policy --role-name webadmins --policy-arn arn:aws:iam::<ACCOUNT_ID>:policy/webadminspermissionpolicy
aws iam detach-role-policy --role-name webadmins --policy-arn arn:aws:iam::aws:policy/AWSLambdaReadOnlyAccess 
```
* Delete the webadmins role created in the **BUILD** phase:
```
aws iam delete-role --role-name webadmins
```
* Delete the permission policy created in the **BUILD** phase:
```
aws iam delete-policy --policy-arn arn:aws:iam::<ACCOUNT_ID>:policy/webadminspermissionpolicy
```
* Delete the permissions boundary created in the **BUILD** phase:
```
aws iam delete-policy --policy-arn arn:aws:iam::<ACCOUNT_ID>:policy/webadminspermissionsboundary
```
* Delete the CloudFormation stack created in the **BUILD** phase:
```
aws cloudformation delete-stack --stack-name Perm-Bound-Adv --region us-east-1
```
