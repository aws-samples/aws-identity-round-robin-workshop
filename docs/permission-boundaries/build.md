# Permissions Boundaries Round <small>Build Phase</small>

Below are a series of tasks to delegate permissions to the web admins. In these tasks you will be creating policies and testing them. It helps to divide the team into people doing the tasks and people testing things out.

## Setup Instructions

To setup your environment please expand one of the following drop-downs (depending on how if you are doing this workshop at an **AWS event** or **individually**) and follow the instructions: 

??? info "AWS Sponsored Event"

	**Console Login:** if you are attending this workshop at an official AWS event then your team should have the URL and login credentials for your account. This will allow you to login to the account using AWS SSO. Browse to that URL and login. 

	After you login click **AWS Account** box, then click on the Account ID displayed below that (the red box in the image.) You should see a link below that for **Management console**. Click on that and you will be taken the AWS console. 

	![login-page](./images/login.png)

	Make sure the region is set to Ohio (us-east-2)

	**CloudFormation:** Launch the CloudFormation stack below to setup the environment:

	Region| Deploy
	------|-----
	US East 2 (Ohio) | [![Deploy permissions boundary round in us-east-2](./images/deploy-to-aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/new?stackName=Perm-Bound&templateURL=https://jnv-aws-test-website.s3-ap-southeast-2.amazonaws.com/aws-identity-round-robin-workshop/permission-boundaries/identity-workshop-web-admins.yaml)

	1. Click the **Deploy to AWS** button above.  This will automatically take you to the console to run the template.  
	2. Click **Next** on the **Select Template** section.
	3. Click **Next** on the **Specify Details** section (the stack name will be already filled - you can change it or leave it as is)
	4. Click **Next** on the **Options** section.
	5. Finally, acknowledge that the template will create IAM roles under **Capabilities** and click **Create**.

	This will bring you back to the CloudFormation console. You can refresh the page to see the stack starting to create. Before moving on, make sure the stack is in a **CREATE_COMPLETE**.

??? info "Individual"

	Log in to your account however you would normally

	**CloudFormation:** Launch the CloudFormation stack below to setup the environment:

	Region| Deploy
	------|-----
	US East 2 (Ohio) | [![Deploy permissions boundary round in us-east-2](./images/deploy-to-aws.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/new?stackName=Perm-Bound&templateURL=https://jnv-aws-test-website.s3-ap-southeast-2.amazonaws.com/aws-identity-round-robin-workshop/permission-boundaries/identity-workshop-web-admins.yaml)

	1. Click the **Deploy to AWS** button above.  This will automatically take you to the console to run the template.  
	2. Click **Next** on the **Select Template** section.
	3. Click **Next** on the **Specify Details** section (the stack name will already be filled - you can change it or leave it as is)
	4. Click **Next** on the **Options** section.
	5. Finally, acknowledge that the template will create IAM roles under **Capabilities** and click **Create**.

	This will bring you back to the CloudFormation console. You can refresh the page to see the stack starting to create. Before moving on, make sure the stack is in a **CREATE_COMPLETE**.

!!! Attention
	As you use the provided IAM policy hints in each task, keep in mind where you need to replace **`<ACCOUNT_ID>`** with the correct Account ID, where you need to correctly use the resource restrictions and also where you need to change the region. Missing any of these items can result in problems and errors like **"An error occurred (MalformedPolicyDocument) when calling the CreatePolicy operation: The policy failed legacy parsing**".


## Task 1 <small>Create an IAM user and an IAM policy with permission to create managed policies, IAM roles and Lambda functions</small>

Build an IAM policy so that web admins can create customer managed policies, IAM roles and Lambda functions. They should only be able to edit the policies, roles and Lambda functions they create.

### Walk Through
#### Create **`webadmin`** IAM User and Policies

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home).
2. On the first screen you see in the IAM console (which should be the **Dashboard** left menu), find the **IAM users sign-in link**. Copy this link because you will need the Account ID in the URL for the policies and you will need the entire URL when you hand this account to another team for the **VERIFY** phase.
![image1](./images/iam-dashboard.png)
3. Click **Users** on the left menu and create a new IAM user named **`webadmin`** by clicking **Add user**:

	i. *Enable* **AWS Management Console access** and then either *autogenerate* a password or set a *custom* password.
	
	ii. *Disable* the **Require password reset** setting.
	
	iii. *Attach* the AWS Managed Policies **IAMReadOnlyAccess** and **AWSLambdaReadOnlyAccess** to the user.
	
	![image1](./images/create-iam-user.png)

4. Once the **`webadmin`** user has been created, click **Policies** on the left menu, create a new IAM policy named **`webadmintestpolicy`** that is based on the IAM policy hint below by clicking **Create policy**.
5. Switch to the **JSON** tab, base the IAM policy on the IAM policy hint below and click **Review policy**:

	!!! hint
		[IAM Identifiers](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_identifiers.html): You will want to use either *naming* or *pathing* resource restrictions in the IAM policy. The question marks **`<????>`** in the resource element below should be replaced with something that could act as a resource restriction. 
		
		Examine the existing resources (roles, Lambda functions) to make sure the policy will give access to existing resources owned by the web admins. Replacing the **`<????>`** references is really the key to this workshop.
		
		**Remember** that *path* based resource restrictions **cannot** be created using the *AWS Console*, but *naming* based ones can.
		
		**Remember** to replace the **`<ACCOUNT_ID>`** placeholder below with the correct Account ID of your AWS account.
		
		*If you get stuck* then have a look at the example IAM policy *below* that uses *naming* based resource restrictions to restrict access to resources having a *prefix* of **`web-admins-*`**.
	
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
	            "Resource": "arn:aws:iam::<ACCOUNT_ID>:policy/<????>"
	        },
	        {
	            "Sid": "CreateRoles",
	            "Effect": "Allow",
	            "Action": [
	                "iam:CreateRole",
	                "iam:UpdateRole",
	                "iam:DeleteRole",
	                "iam:AttachRolePolicy",
	                "iam:DetachRolePolicy"
	            ],
	            "Resource": [
	                "arn:aws:iam::<ACCOUNT_ID>:role/<????>"
	            ]
	        },
	        {
	            "Sid": "LambdaFullAccesswithResourceRestrictions",
	            "Effect": "Allow",
	            "Action": "lambda:*",
	            "Resource": "arn:aws:lambda:us-east-2:<ACCOUNT_ID>:function:<????>"
	        },
	        {
	            "Sid": "PassRoletoLambda",
	            "Effect": "Allow",
	            "Action": "iam:PassRole",
	            "Resource": "arn:aws:iam::<ACCOUNT_ID>:role/<????>",
	            "Condition": {
	                "StringLikeIfExists": {
	                    "iam:PassedToService": "lambda.amazonaws.com"
	                }
	            }
	        },
	        {
	            "Sid": "AdditionalPermissionsforLambda",
	            "Effect": "Allow",
	            "Action": [ "kms:ListAliases", "logs:Describe*", "logs:ListTagsLogGroup", "logs:FilterLogEvents", "logs:GetLogEvents" ],
	            "Resource": "*"
	        }
	    ]
	}
	```

	??? info "Example **`webadmintestpolicy`** IAM Policy using Naming (**`web-admins-*`**) Resource Restrictions"
	
		This example IAM policy uses **naming** based resource restrictions to restrict access to resources having a *prefix* of **`web-admins-*`**.
		
		**Remember** to replace the **`<ACCOUNT_ID>`** placeholder below with the correct Account ID of your AWS account.
		
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
		            "Resource": "arn:aws:iam::<ACCOUNT_ID>:policy/web-admins-*"
		        },
		        {
		            "Sid": "CreateRoles",
		            "Effect": "Allow",
		            "Action": [
		                "iam:CreateRole",
		                "iam:UpdateRole",
		                "iam:DeleteRole",
		                "iam:AttachRolePolicy",
		                "iam:DetachRolePolicy"
		            ],
		            "Resource": [
		                "arn:aws:iam::<ACCOUNT_ID>:role/web-admins-*"
		            ]
		        },
		        {
		            "Sid": "LambdaFullAccesswithResourceRestrictions",
		            "Effect": "Allow",
		            "Action": "lambda:*",
		            "Resource": "arn:aws:lambda:us-east-2:<ACCOUNT_ID>:function:web-admins-*"
		        },
		        {
		            "Sid": "PassRoletoLambda",
		            "Effect": "Allow",
		            "Action": "iam:PassRole",
		            "Resource": "arn:aws:iam::<ACCOUNT_ID>:role/web-admins-*",
		            "Condition": {
		                "StringLikeIfExists": {
		                    "iam:PassedToService": "lambda.amazonaws.com"
		                }
		            }
		        },
		        {
		            "Sid": "AdditionalPermissionsforLambda",
		            "Effect": "Allow",
		            "Action": [ "kms:ListAliases", "logs:Describe*", "logs:ListTagsLogGroup", "logs:FilterLogEvents", "logs:GetLogEvents" ],
		            "Resource": "*"
		        }
		    ]
		}
		```
	
6. Name the IAM policy by entering **`webadmintestpolicy`** into the **Name** field.
7. Enter an optional description into the **Description** field and click **Create policy** to create the IAM policy.
8. Attach the **`webadmintestpolicy`** IAM policy you just created to the recently created **`webadmin`** IAM user:
	1. Search for the newly created IAM policy by entering **`webadmintestpolicy`** into the IAM policy *Search* field at the top of the **Policies** screen.
	2. Select the **`webadmintestpolicy`** IAM policy and select **Attach** from the drop-down **Policy actions** menu.
	3. Search for the recently created IAM user by entering **`webadmin`** into the Attach policy *Search* field at the top of the **Attach policy** screen.
	4. Select the **`webadmin`** IAM user and click **Attach policy**.

#### Test **`webadmin`** IAM User and Policies
As you complete the following tests, keep in mind the resource restriction you set up in the policy above (i.e. what you replaced **`<????>`** with).

Use the **IAM users sign-in link** you gathered earlier to login to a new AWS Console session with the newly created **`webadmin`** IAM user:

1. Login with the **webadmin** IAM user (using a different web browser or a private browsing session) to verify the user can create an IAM policy. The permissions you set on the policy do not matter at this point.

	??? info "Example IAM policy"
		
		1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home).
		2. Click **Policies** on the left menu, create a new IAM policy that will act as the permissions policy for the IAM role that will be attached to the Lambda function by clicking **Create policy**.
		3. Switch to the **JSON** tab, **copy** the IAM policy from below and click **Review policy**:
			
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
			
		4. Name the IAM policy by entering a name into the **Name** field based on the resource restrictions (the **Resource Restriction Identifier**) put in place for the IAM policy name.
		5. Enter an optional description into the **Description** field and click **Create policy** to create the IAM policy.
		
2. Also, verify you can create an IAM role (while following the resource restriction). This role should use Lambda as the trusted entity (we will use this role to test the next task) and attach the policy you just created to it.

	??? info "Example IAM role"
	
		1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home).
		2. Click **Roles** on the left menu and create a new IAM role:
			1. Select **AWS service** as the type of trusted entity.
			2. Choose **Lambda** as the service for this role and click **Next: Permissions**.
			3. *Attach* the newly created IAM policy above to the role under **Permission Policies** and click **Next: Tags**.
			4. Click **Next: Review**.
			5. Name the IAM role by entering a name into the **Role name** field based on the resource restrictions (the **Resource Restriction Identifier**) put in place for the IAM role name.
			6. Enter an optional description into the **Description** field and click **Create role** to create the IAM role.

3. Finally verify the user can create a Lambda function with the role you just created attached.

	??? info "Example Lambda function"
		
		1. Browse to the [Lambda console](https://console.aws.amazon.com/lambda/home?region=us-east-2region#/functions).
		2. Click **Functions** on the left menu and click **Create function** to create a new Lambda function:
			1. Choose **Author from scratch**.
			2. Name the Lambda function based on the resource restrictions (the **Resource Restriction Identifier**) put in place for the Lambda function name.
			3. Select **Node.js 10.x** as the **Runtime**.
			4. Select **Use an existing role** under **Execution role**.
			5. Select your newly created Lambda IAM role above under **Existing role**.
			6. Click **Create function** to complete creating the Lambda function.

!!! note
	You don't need to test executing your newly created Lambda function as being able to create it with your newly created IAM role is enough to prove your IAM permissions policy worked correctly.

!!! question
	* Why are we using resource restrictions here?
	* There are two ways of doing resource restrictions: naming and pathing. Which option are we using here? Which option allows you to create policies using both the AWS Console and CLI?

## Task 2 <small>Create a permissions boundary</small>

!!! Attention
	Please switch back to the original web browser (or private browsing session) you used to login to your AWS account as a **full administrator** and don't use the **`webadmin`** user for this task. Because we want to create an IAM Permissions Boundary for the **webadmin** user.

The **webadmin** user can create IAM polices, IAM role and Lambda functions. We now need to limit the permissions of the roles the user can create. If not then the web admins could simply create new policies with full admin rights, attach these to the roles, pass these roles to Lambda functions and escalate their permissions (either intentionally or inadvertently).

We will use permissions boundaries to limits the effective permissions of the roles. The permissions boundary should allow the following [effective permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html) for any role created by the web admins:

>	i. Create log groups (but can not overwrite any other log groups)

>	ii. Create log streams and put logs

>	iii. List the objects from the S3 bucket name that starts with `"web-admins-"` and ends in `"-data"`

### Walk Through: 

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home).
2. Click **Policies** on the left menu, create a new IAM policy named **`webadminpermissionboundary`** that will act as the permissions boundary for the web admin by clicking **Create policy**.
3. Switch to the **JSON** tab, **copy** the IAM policy from the hint below and click **Review policy**:

	!!! hint
		**Remember** to replace the **`<ACCOUNT_ID>`** placeholder below with the correct Account ID of your AWS account.
	
	``` json
	{
	    "Version": "2012-10-17",
	    "Statement": [
	        {
	            "Sid": "CreateLogGroup",
	            "Effect": "Allow",
	            "Action": "logs:CreateLogGroup",
	            "Resource": "arn:aws:logs:us-east-2:<ACCOUNT_ID>:*"
	        },
	        {
	            "Sid": "CreateLogStreamAndEvents",
	            "Effect": "Allow",
	            "Action": [
	                "logs:CreateLogStream",
	                "logs:PutLogEvents"
	            ],
	            "Resource": "arn:aws:logs:us-east-2:<ACCOUNT_ID>:log-group:/aws/lambda/*:*"
	        },
	        {
	            "Sid": "AllowedS3GetObject",
	            "Effect": "Allow",
	            "Action": [
	                "s3:List*"
	            ],
	            "Resource": "arn:aws:s3:::web-admins-<ACCOUNT_ID>-us-east-2-data"
	        }
	    ]
	}
	```
	
4. Name the IAM policy by entering **`webadminpermissionboundary`** into the **Name** field.
5. Enter an optional description into the **Description** field and click **Create policy** to create the IAM policy.

!!! question
	* What do you attach the permission boundary to?
	* How does a permissions boundary differ from a standard IAM policy?
	* How could you test the permissions boundary at this point?

## Task 3 <small>Update the Permission Policy for the **webadmin** user to incorporate the Permissions Boundary Condition</small>

### Walk Through
#### Update **`webadmin`** IAM User Policies

1. Browse to the [IAM console](https://console.aws.amazon.com/iam/home).
2. Click **Policies** on the left menu, create a new IAM policy named **`webadminpermissionpolicy`** that references the permissions boundary policy (**`webadminpermissionboundary`**) we just created above by clicking **Create policy**.
3. Switch to the **JSON** tab, base the IAM policy on the IAM policy hint below and click **Review policy**:

	!!! note
		The policy below contains *two additional sections* (the last two sections) that we did not address in the earlier tasks. The additions are focused on denying the ability to change or delete the permission policy and the permissions boundary.

	!!! hint 
		[permissions boundaries](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html): The question marks **`<????>`** in the resource element below should be replaced with something that could act as either a naming or pathing resource restriction.
		
		**Remember** that *path* based resource restrictions **cannot** be created using the *AWS Console*, but *naming* based ones can.
		
		**Remember** to replace the **`<ACCOUNT_ID>`** placeholder below with the correct Account ID of your AWS account.
	
		*If you get stuck* then have a look at the example IAM policy *below* that uses *naming* based resource restrictions to restrict access to resources having a *prefix* of **`web-admins-*`**.

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
	            "Resource": "arn:aws:iam::<ACCOUNT_ID>:policy/<????>"
	        },
	        {
	        	  "Sid": "RoleandPolicyActionswithnoPermissionBoundarySupport",
	            "Effect": "Allow",
	            "Action": [
	            		"iam:UpdateRole",
	                	"iam:DeleteRole"
	            ],
	            "Resource": [
	                "arn:aws:iam::<ACCOUNT_ID>:role/<????>"
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
	                "arn:aws:iam::<ACCOUNT_ID>:role/<????>"
	            ],
	            "Condition": {"StringEquals": 
	                { "iam:PermissionsBoundary": "arn:aws:iam::<ACCOUNT_ID>:policy/webadminpermissionboundary" }
	            }
	        },
	        {
	            "Sid": "LambdaFullAccesswithResourceRestrictions",
	            "Effect": "Allow",
	            "Action": "lambda:*",
	            "Resource": "arn:aws:lambda:us-east-2:<ACCOUNT_ID>:function:<????>"
	        },
	        {
	            "Sid": "PassRoletoLambda",
	            "Effect": "Allow",
	            "Action": "iam:PassRole",
	            "Resource": "arn:aws:iam::<ACCOUNT_ID>:role/<????>",
	            "Condition": {
	                "StringLikeIfExists": {
	                    "iam:PassedToService": "lambda.amazonaws.com"
	                }
	            }
	        },
	        {
	            "Sid": "AdditionalPermissionsforLambda",
	            "Effect": "Allow",
	            "Action": 	[ "kms:ListAliases", "logs:Describe*", "logs:ListTagsLogGroup", "logs:FilterLogEvents", "logs:GetLogEvents" ],
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
	                "arn:aws:iam::<ACCOUNT_ID>:policy/webadminpermissionboundary",
	                "arn:aws:iam::<ACCOUNT_ID>:policy/webadminpermissionpolicy"
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

	??? info "Example **`webadminpermissionpolicy`** IAM Policy using Naming (**`web-admins-*`**) Resource Restrictions"
	
		This example IAM policy uses **naming** based resource restrictions to restrict access to resources having a *prefix* of **`web-admins-*`**.
		
		**Remember** to replace the **`<ACCOUNT_ID>`** placeholder below with the correct Account ID of your AWS account.
		
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
		            "Resource": "arn:aws:iam::<ACCOUNT_ID>:policy/web-admins-*"
		        },
		        {
		        	  "Sid": "RoleandPolicyActionswithnoPermissionBoundarySupport",
		            "Effect": "Allow",
		            "Action": [
		            		"iam:UpdateRole",
		                	"iam:DeleteRole"
		            ],
		            "Resource": [
		                "arn:aws:iam::<ACCOUNT_ID>:role/web-admins-*"
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
		                "arn:aws:iam::<ACCOUNT_ID>:role/web-admins-*"
		            ],
		            "Condition": {"StringEquals": 
		                { "iam:PermissionsBoundary": "arn:aws:iam::<ACCOUNT_ID>:policy/webadminpermissionboundary" }
		            }
		        },
		        {
		            "Sid": "LambdaFullAccesswithResourceRestrictions",
		            "Effect": "Allow",
		            "Action": "lambda:*",
		            "Resource": "arn:aws:lambda:us-east-2:<ACCOUNT_ID>:function:web-admins-*"
		        },
		        {
		            "Sid": "PassRoletoLambda",
		            "Effect": "Allow",
		            "Action": "iam:PassRole",
		            "Resource": "arn:aws:iam::<ACCOUNT_ID>:role/web-admins-*",
		            "Condition": {
		                "StringLikeIfExists": {
		                    "iam:PassedToService": "lambda.amazonaws.com"
		                }
		            }
		        },
		        {
		            "Sid": "AdditionalPermissionsforLambda",
		            "Effect": "Allow",
		            "Action": 	[ "kms:ListAliases", "logs:Describe*", "logs:ListTagsLogGroup", "logs:FilterLogEvents", "logs:GetLogEvents" ],
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
		                "arn:aws:iam::<ACCOUNT_ID>:policy/webadminpermissionboundary",
		                "arn:aws:iam::<ACCOUNT_ID>:policy/webadminpermissionpolicy"
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

6. Name the new IAM policy by entering **`webadminpermissionpolicy`** into the **Name** field.
7. Enter an optional description into the **Description** field and click **Create policy** to create the IAM policy.
8. Attach the **`webadminpermissionpolicy`** IAM policy you just created to the **`webadmin`** IAM user and remove the existing **`webadmintestpolicy`** IAM policy from it:
	1. Click **Users** on the left menu and search for the **webadmin** user by entering **`webadmin`** into the IAM user *Search* field at the top of the **Users** screen.
	2. Click on the **webadmin** user to view it's details.
	3. Select the **Permissions** tab.
	4. Remove the existing attached **`webadmintestpolicy`** IAM policy by clicking on the **X** to the right of it.
	5. Click **Detach** when prompted **Are you sure you want to detach policy `webadmintestpolicy` from user `webadmin` ?**.
	6. Click **Add permissions**.
	7. Select **Attach existing policies directly**.
	8. Search for the newly created IAM policy by entering **`webadminpermissionpolicy`** into the IAM policy *Search* field at the top of the **Grant permissions** screen.
	9. Select the **`webadminpermissionpolicy`** IAM policy and click **Next: Review**.
	10. Click **Add permissions** to add the selected IAM policy.

9. When you are done the **webadmin** user should have only **three** policies attached:

	**`webadminpermissionpolicy`**, **`IAMReadOnlyAccess`** and **`AWSLambdaReadOnlyAccess`**.

#### Test Updated **`webadmin`** IAM User Policies

1. Again, from the different web browser (or a private browsing session) where you are logged into the AWS Console as the **webadmin** user, verify the user can create an IAM policy, create an IAM role (attaching both a permission policy and permissions boundary to the role) and finally create a Lambda function into which you will pass that role.

	Keep in mind the resource restriction (i.e. what you replaced **`<????>`** with).

!!! question
	* Why do we add the Deny for DeletePolicy actions regarding the webadminpermissionboundary & webadminpermissionpolicy?
	* What would happen if we didn't deny the ability to delete permissions boundaries?

## Task 4 <small>Gather info needed for the **VERIFY** phase</small>

### Walk Through

Now that you have setup the IAM user for the web admins, it's time to pass this information on to the next team who will work through the **VERIFY** tasks. You need to gather some details about your setup and then hand this info to the next team.

Here are all of the details you need to pass to another team. If you following the recommended naming conventions than you can use the answers below.  If you were given a form to fill out then enter the info into the form. This needs to be given to another team so they can do the **VERIFY** phase tasks. Your team should collect the **VERIFY** phase form from another team so you can also work through the **VERIFY** tasks. 

* IAM users sign-in link: **https://*`<Account_ID>`*.signin.aws.amazon.com/console**
* IAM User Name:	**webadmin**
* IAM User Password:
* Resource Restriction Identifier: (i.e. **web-admins-**)
* Permissions Boundary Name: **webadminpermissionboundary**
* Permission Policy Name: **webadminpermissionpolicy**

!!! tip 
	Do not hand out this info to the same team that is giving you the info - this way we will end up properly swapping between teams if we have an odd number of teams.

### <small>[Click here to go to the VERIFY phase](./verify.md)</small>