# Permission Boundaries Round <small>VERIFY phase</small>

You are now in the **VERIFY** phase. It is time to put on the hat of the web admins and test out their access. 

You should have receieved from another team the following information:

* IAM users sign-in link:	**https://Account_ID.signin.aws.amazon.com/console**
* IAM user name:	**webadmin**
* IAM user password:	
* Resource restriction identifier:	
* Permission boundary name: **webadminpermissionboundary**
* Permission policy name: **webadminpermissionpolicy**

You will be setting up an IAM policy, an IAM role and a Lambda function. The Lambda function should be able to list files in an S3 bucket. This is how you will verify you are able to do what the web admins are allowed to do.

The web admins should not be able to impact any resources in the account  that they do not own (e.g. the Application admins) including IAM users, roles, S3 buckets, Lambda functions, etc.

Application architecture: ![image1](./images/architecture.png)

## Task 1 <small>Create a customer managed IAM policy</small>
	
* The first step is to create an IAM policy. This will set the permissions of the role that you will pass to a Lambda function. Since the function will be working with S3 and since the point of this is to show how permission boundaries work, use the following policy which grants basic Lambda logging permissions and S3 full access. 
* Keep in mind the resource restrictions put in place which will require you to use a certain name for the policy.

``` json
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

## Task 2 <small>Create an IAM role</small>

* Next create an IAM Role. Choose Lambda as the service for this role. Attach the policy you just created and the permission boundary (which should be named:  **webadminpermissionboundary**)
* Keep in mind the resource restrictions put in place which will require you to use a certain name for the role.
	
## Task 3 <small>Create a Lambda function</small>

* Finally you will create a **Node.js 8.10** Lambda function using the code below and attach the IAM role you just created to it. You will need to replace `"WEB_ADMIN_BUCKET_NAME"` with the bucket from the account that begins with `"web-admins-"` and ends in `"-data"`

``` node
const AWS = require('aws-sdk');
const s3 = new AWS.S3();

exports.handler = async (event) => {
  console.log('Loading function');
  const allKeys = [];
  await getKeys({ Bucket: 'WEB_ADMIN_BUCKET_NAME' }, allKeys);
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

* Test the Lambda function and make sure it is generating logs in CloudWatch logs and that it is able to list the objects in the bucket.

## Task 4 <small>Investigate if you are able to do anything else</small>

The final step is to determine if you can do anything else in the account. Can you impact any resources not owned by the web admins?

### <small>[Click here to go to the CLEANUP phase](./cleanup.md)</small>
