# Permissions boundaries workshop <small> Build phase </small>


!!! Attention
    <p style="font-size:16px;">
      Throughout the workshop, keep in mind where you need to replace <ACCOUNT_ID> with the correct Account ID, where you need to use pathing and also where you need to change the region (although if you are taking this as part of an AWS event, just use the already specified us-east-1.) Missing any of these items can result in problems and errors like **"An error occurred (MalformedPolicyDocument) when calling the CreatePolicy operation: The policy failed legacy parsing"**.
    </p>

!!! Tip
    <p style="font-size:16px;">
      Tasks 1, 2 and 3 can be done independently if you are working in a team and want to divide up the tasks.
    </p>


## Setup Instructions

To setup your environment expand the appropriate choice from the following drop-downs, perform the steps, and then move on to **Task 1**

??? info "Click here if you are *using your own AWS account*. You will be using your computer to run the commands."

	Log in to your account however you would normally. You should use an IAM user or role with admin rights. 

	**CloudFormation:** Launch the CloudFormation stack below to setup the environment:

	Region| Deploy
	------|-----
	US East 1 (Virginia) | <a href="https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=Perm-Bound-Adv&templateURL=https://s3-us-west-2.amazonaws.com/sa-security-specialist-workshops-us-west-2/identity-workshop/permissionboundary/identity-workshop-web-admins-advanced-solo.yaml" target="_blank">![Deploy in us-east-2](./images/deploy-to-aws.png)</a>

	1. Click the **Deploy to AWS** button above.  This will automatically take you to the console to run the template.  
	2. Click **Next** under the **Create stack** section.
	3. Click **Next** under the **Specify stack details** section (the stack name will already be filled - you can leave the other options in Parameters at their default settings too)
	4. Click **Next** under the **Advanced options** section.
	5. Finally, acknowledge that the template will create IAM resources with custom names under **Capabilities** and click **Create stack**.

	This will bring you back to the CloudFormation console. You can refresh the stack set to see the latest status. Before moving on, make sure the stack finally shows **CREATE_COMPLETE**.

	You will need to configure the <a href="https://aws.amazon.com/cli/" target="_blank"> AWS CLI </a> on your computer with an access key from a principal (IAM user, role, etc) that has at least IAM Full Access and sts:AssumeRole (in order to complete the cleanup at the end, you will also need Lambda Full Access and CloudFormation Full Access.) Then move on to **Task 1**.
	

??? info  "Click here if you're at an *AWS event* where the *Event Engine* is being used. You will be using Cloud9 to run the commands." 

    <p style="font-size:20px;">
      **Step 1** : Retrieve temporary credentials from Event Engine
    </p>
	
	1. Navigate to the <a href="https://dashboard.eventengine.run" target="_blank">Event Engine dashboard</a>
	2. Enter your **team hash** code. 
	3. Click **AWS Console**
	4. Copy the **export** commmands under the **Credentials** section for the temporary credentials (you will need these in the next step.)

    <p style="font-size:20px;">
      **Step 2** : Connect to the AWS Console via Event Engine and browse to the AWS Cloud9 IDE
    </p>	
	
	1. Click **Open Console** from the Event Engine window
	2. Navigate to the <a href="https://us-east-1.console.aws.amazon.com/cloud9/home" target="_blank">AWS Cloud9</a> console.
	2. Click on **Open IDE** in the `workshop-environment` under **Your environments**
	3. Click the **gear image** icon in the upper right hand corner to open the Cloud9 Preferences. Scroll down in the settings, click on the **AWS SETTINGS** section and click the button next to **AWS managed temporary credentials** to disable this.
	5. Now go to a Cloud9 terminal tab (tab title will start with the words **bash**). 
	6. Type `aws configure --profile default` hit enter. Hit enter until you get to the choice **Default region name** and type in `us-east-1`. Hit enter and then enter again to leave this menu.
	7. Then create a file in the `~/.aws` directory named `credentials` and paste in the credentials you copied from the Event Engine. You will need to remove the word **export** from the start of each line. Add `[default]` before all these rows. You should end up with something that looks like this:<br>
	[default]</br>
	AWS_ACCESS_KEY_ID=ASIA________</br>
	AWS_SECRET_ACCESS_KEY=iZoD_______________________</br>
	AWS_SESSION_TOKEN=FQoG_____________________________________________</br>
	8. Now you can run commands from within the Cloud9 IDE using the temporary credentials from Event Engine. If you open a new tab you will need to paste in the credentials again.
	9. Move on to **Task 1**.


<!---	
??? info  "Click here if you're at an *AWS event* and *AWS provided an account to you*. You will be using Cloud9 to run the commands." 

	<p style="font-size:20px;">
      **Step 1**: Login to the console and run the CloudFormation template
    </p>
	
	**Console Login:** Your team should have been given a piece of paper with a URL and credentials. This will allow you to login using AWS SSO. 

	After you login click **AWS Account** box, then click on the Account ID displayed below that (the red box in the image.) You should see a link below that for **Management console**. Click on that and you will be taken the AWS console. Make sure the region is set to US East 2 (Ohio).

	** Click the *Deploy to AWS* button below to launch the CloudFormation stack. **
	
	Region| Deploy
	------|-----
	US East 1 (Virginia) | <a href="https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=Perm-Bound-Adv&templateURL=https://s3-us-west-2.amazonaws.com/sa-security-specialist-workshops-us-west-2/identity-workshop/permissionboundary/identity-workshop-web-admins-advanced.yaml" target="_blank">![Deploy in us-east-2](./images/deploy-to-aws.png)</a>
	
	1. Click **Next** on the **Select Template** section.
	2. Click **Next** on the **Specify Details** section (the stack name will be already filled - you can change it or leave it as is)
	3. Click **Next** on the **Options** section.
	4. Finally, acknowledge that the template will create IAM roles under **Capabilities** and click **Create**.
	5. This will bring you back to the CloudFormation console. You can refresh the page to see the stack starting to create. Before moving on, make sure the stack shows **CREATE_COMPLETE**.

	 <p style="font-size:20px;">
      **Step 2** : Create an IAM user so you can grab the 
    </p>
	
    <p style="font-size:20px;">
      **Step 2** : Connect to the AWS Cloud9 IDE
    </p>
	
	1. Navigate to the <a href="https://us-east-2.console.aws.amazon.com/cloud9/home" target="_blank">AWS Cloud9</a> console.
	2. Click on **Open IDE** in the `workshop-environment` under **Your environments**
	3. Click the **gear** icon in the upper right hand corner to open the Cloud9 Preferences. Scroll down in the settings, click on the **AWS SETTINGS** section and click the button next to **AWS Managed Temporary Credentials** to disable this.
	4. --- Need steps here ---
	4. Go back to the Cloud9 environment. Type `aws configure` hit enter. Hit enter until you get to the choice **Default region name** and type in `us-east-2`. Hit enter and then enter again to leave this menu.
	5. Then create a file in the `~/.aws` directory named `credentials` and paste in the credentials you copied from the SSO login page. Rename the profile to `default` (it will be named something similar to **Account_ID_AdministratorAccess** when you first paste it in)
	4. Now you can run commands from within the Cloud9 IDE using the temporary credentials from AWS SSO. 
	4. Move on to **Task 1**.
--->

---


###


## Task 1 <small>Create the webadmins role</small>

First you will create an IAM role for the webadmins (initially this role will trust your own AWS account but when you switch accounts with an another team in the **Verify** phase you will configure it to trust the other team's account):

* For many of the steps below you will need your account ID. To get that type in `aws sts get-caller-identity'. The account ID will be the first number listed after **Account**. (If you are using Cloud9, you can do this from a second terminal window so you can refer back to it later when needed.)
* Use the following JSON to create a file named **`trustpolicy.json`** for the trust policy (using your preferred text editor): 
```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Principal": {
      "AWS": "arn:aws:iam::<ACCOUNT_ID>:root"
    },
    "Action": "sts:AssumeRole"
  }
}
```
* Create the IAM role that will be used by the webadmins:
```
aws iam create-role --role-name webadmins --assume-role-policy-document file://trustpolicy.json
```
* Attach the AWSLambdaReadOnlyAccess AWS Managed Policy to the role:
```
aws iam attach-role-policy --policy arn:aws:iam::aws:policy/AWSLambdaReadOnlyAccess --role-name webadmins
```


## Task 2 <small>Create the permissions boundary the webadmins will use when creating roles</small>

Next you will create the policy that will be used as the permissions boundary.  The permissions boundary should only allow the following actions: Create log groups, create log streams, put logs and list the files in the webadmins folder of the bucket that starts with `"shared-logging-"` and ends in `"-data"`:

* Use the following JSON to create a file named **`permissionsboundary.json`** for the permissions boundary policy:
	
``` json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "CreateLogGroup",
            "Effect": "Allow",
            "Action": "logs:CreateLogGroup",
            "Resource": "arn:aws:logs:us-east-1:<ACCOUNT_ID>:*"
        },
        {
            "Sid": "CreateLogStreamandEvents",
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "arn:aws:logs:us-east-1:<ACCOUNT_ID>:log-group:/aws/lambda/*:*"
        },
        {
            "Sid": "AllowS3GetObject",
            "Effect": "Allow",
            "Action": [
                "s3:List*"
            ],
            "Resource": "arn:aws:s3:::shared-logging-<ACCOUNT_ID>-us-east-1-data",
             "Condition": {
                "StringEquals": {
                    "s3:prefix": "webadmins"
             		}
        		}
        },
        {
    		  "Sid": "OtherPermissionsNeeded",
    		  "Effect": "Allow",
    		  "Action": [
                "dynamodb:PutItem",
                "dynamodb:UpdateItem",
                "dynamodb:DeleteItem"
   			  ],
    		  "Resource": "*",
    		  "Condition": {
        			"StringEquals": {
            		"aws:RequestedRegion": "us-east-1"
        			}
        		}
        }
    ]
}
```
* Create the policy:
```
aws iam create-policy --policy-name webadminspermissionsboundary --policy-document file://permissionsboundary.json
```

!!! question
	* To what object will the webadmins attach the permissions boundary?
	* How does a permissions boundary differ from an identity-based policy?

## Task 3 <small>Create the identity-based policy for the webadmins</small>

!!! hint 
	**IMPORTANT!!!!** - The question marks **`????`** in the policy below should be replaced with something that could act as part of a resource restriction.  The end result is that you will have a pathing requirement for the roles and policies. Replacing the **`????`** is one of the key challenges in this workshop and probably the most confusing part. Talk to a facilitator if you have issues with this. 

Next you will create the policy that will be attached to the webadmins role.

* Use the following JSON to create a file named **`identitybasedpolicy.json`** for the permission policy:

``` json
{
    "Version": "2012-10-17",
    "Statement": [
		{
            "Sid": "CreateCustomerManagedPolicies",
            "Effect": "Allow",
            "Action": [
                "iam:CreatePolicy",
                "iam:DeletePolicy",
                "iam:CreatePolicyVersion",
                "iam:DeletePolicyVersion",
                "iam:SetDefaultPolicyVersion"
            ],
            "Resource": "arn:aws:iam::<ACCOUNT_ID>:policy/webadmins/????"
        },
        {
        	  "Sid": "RoleandPolicyActionswithnoPermissionBoundarySupport",
            "Effect": "Allow",
            "Action": [
            		"iam:UpdateRole",
                	"iam:DeleteRole"
            ],
            "Resource": [
                "arn:aws:iam::<ACCOUNT_ID>:role/webadmins/????"
            ]
        },
        {
            "Sid": "CreateRoles",
            "Effect": "Allow",
            "Action": [
                "iam:CreateRole",
                "iam:AttachRolePolicy",
                "iam:DetachRolePolicy"
            ],
            "Resource": [
                "arn:aws:iam::<ACCOUNT_ID>:role/webadmins/????"
            ],
            "Condition": {"StringEquals": 
                {"iam:PermissionsBoundary": "arn:aws:iam::<ACCOUNT_ID>:policy/webadminspermissionsboundary"}
            }
        },
        {
            "Sid": "LambdaFullAccess",
            "Effect": "Allow",
            "Action": "lambda:*",
            "Resource": "arn:aws:lambda:us-east-1:<ACCOUNT_ID>:function:*"
        },
        {
            "Sid": "PassRoletoLambda",
            "Effect": "Allow",
            "Action": "iam:PassRole",
            "Resource": "arn:aws:iam::<ACCOUNT_ID>:role/webadmins/????",
            "Condition": {
                "StringLikeIfExists": {
                    "iam:PassedToService": "lambda.amazonaws.com"
                }
            }
        },
        {
            "Sid": "AdditionalPermissionsforLambda",
            "Effect": "Allow",
            "Action": 	["kms:ListAliases", "logs:Describe*", "logs:ListTagsLogGroup", "logs:FilterLogEvents", "logs:GetLogEvents"],
            "Resource": "*"
        },
        {
            "Sid": "DenyPermissionBoundaryandPolicyDeleteModify",
            "Effect": "Deny",
            "Action": [
                "iam:CreatePolicyVersion",
                "iam:DeletePolicy",
                "iam:DeletePolicyVersion",
                "iam:SetDefaultPolicyVersion"
            ],
            "Resource": [
                "arn:aws:iam::<ACCOUNT_ID>:policy/webadminspermissionsboundary",
                "arn:aws:iam::<ACCOUNT_ID>:policy/webadminspermissionpolicy"
            ]
        },
        {
            "Sid": "DenyRolePermissionBoundaryDelete",
            "Effect": "Deny",
            "Action": "iam:DeleteRolePermissionsBoundary",
            "Resource": "*"
        }
    ]
}
```
 
* Create a policy named `webadminspermissionpolicy`:
```
aws iam create-policy --policy-name webadminspermissionpolicy --policy-document file://identitybasedpolicy.json
```
* Attach the policy to the webadmins role
```
aws iam attach-role-policy --role-name webadmins --policy-arn arn:aws:iam::<ACCOUNT_ID>:policy/webadminspermissionpolicy
```
When you are done the **webadmins** role should have these two policies attached: **webadminspermissionpolicy** & **AWSLambdaReadOnlyAccess**.

!!! question "Questions"

	* Why were there some blocks in the policy above that used the permissions boundary condition and some that did not?
	* Why are we using pathing here?
	* There are two ways of doing resource restrictions: naming and pathing. Which option allows you to create policies using both the AWS Console and CLI?
	* Why do we add the Deny for DeletePolicy actions regarding the webadminspermissionsboundary & webadminspermissionpolicy? Is this actually needed?
	
## Task 4 <small>Test the webadmins permissions</small>
	
It's time to check your work and make sure the webadmins are set up properly. 

The instructions for how to test the setup can be found in the **[VERIFY phase](./verify.md)**. The **VERIFY** phase assumes you are checking another team's setup but for your own testing this will be just done in your own account. You'll need to keep that in mind because some of the **VERIFY** phase instructions assume you are doing this cross account so you'll need to adjust some of the commands. 

Come back to the **BUILD** phase after you have verified your work is correct, move on to **Task 5**.

## Task 5 <small>Gather info needed for the **VERIFY** phase</small>

It is time to pass the work on to another team who will work through the **VERIFY** tasks. If you are doing this workshop online on your own then you already verified your work in **Task 4** so you can skip this task. 

If you were given a form to fill out then enter the info and hand it to another team (or send this to the other team using whatever method is easiest.) If you followed the recommended naming conventions you only need to enter your **Account ID** and **Resource restriction**.
 
* Webadmins role ARN:	arn:aws:iam::`<YOUR_ACCOUNT_ID>`:role/**webadmins**
* Resource restriction for both the roles and policies: /webadmins/`Resource restriction you used`
* Permissions boundary name: **webadminspermissionsboundary**
* Permission policy name: **webadminspermissionpolicy**

Exchange forms with another team and then update the trust policy of the webadmins roles so the other team can assume the role (they will do the same for your team):

* Use the following JSON to create a file name `trustpolicy2.json` for the trust policy. Replace `<YOUR_ACCOUNT_ID>` with your Account ID so you can still retest this if needed and the `<ACCOUNT_ID_FROM_OTHER_TEAM>` with the other team's Account ID:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": [
          "arn:aws:iam::<YOUR_ACCOUNT_ID>:root",
          "arn:aws:iam::<ACCOUNT_ID_FROM_OTHER_TEAM>:root"
        ]
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```
* Update the trust policy on the webadmins roles so both your team and the verify team can assume the role
```
aws iam update-assume-role-policy --role-name webadmins --policy-document file://trustpolicy2.json
```

**[Click here to go to the VERIFY phase](./verify.md)**
