# Permission Boundaries Round

Your customer has deployed a three tier web application in production. Different teams work on different aspects of the architecture but they don't always communicate well. Just recently the team responsible for the web front end set up a Lambda function that inadvertently impacted the application team's resources. The VP of Operations was furious. The VP has tasked you with setting up permissions for the web admins so that they can only impact their own resources while still being able to do their job. 

**AWS Service/Feature Coverage**: 

* AWS IAM [permission boundaries](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html) 
* AWS IAM [identifiers or resource restrictions](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_identifiers.html)
* AWS IAM [users & roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html)
* AWS [Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html)
 
## Agenda

The round is broken down into a [**BUILD**](./build.md) phase followed by a [**VERIFY**](./verify.md) phase, with a [**CLEANUP**](./cleanup.md) phase at the end to remove any created resources.

**BUILD** (60 min): First each team will carry out the activities involved in the **BUILD** phase where they will set up access for the web admins and properly lock down the account. Then each team will hand credentials for an IAM user in their account to another team to act in the **VERIFY** phase. The **VERIFY** phase lasts about 30 min.

**VERIFY** (30 min): Each team will carry out the **VERIFY** activities as if they were part of the web admins team. The **VERIFY** activities will include validating that the requirements were set up correctly in the **BUILD** phase and also investigate if the web admins are able to take actions that they shouldn't be allowed to.

**CLEANUP** (5 min): Each team will carry out the **CLEANUP** activities to remove any created resources.


!!! info "Team or Individual Exercise"
	This workshop can be done as a team exercise or individually. The instructions are written with assumption that you are working as part of a team but you could just as easily do the steps below on your own. If done as part of an AWS sponsored event then you'll be split into teams of around 4-6 people. Each team will do the **BUILD** phase and then hand off their accounts to another team. Then each team will do the **VERIFY** phase. 

Using this workshop as an example, the three elements of a permission boundary are represented below. When your team does the **BUILD** tasks you will act as the admin. When your team does the **VERIFY** tasks you will act as the delegated admin. The delegated admins will create roles that can be considered **"bound"** since they will have permission boundaries attached.  

![mechanism](./images/permission-boundaries.png)

<!--### Point system
There is a point system for both the **BUILD** and **VERIFY**  activities. Each team also starts out with a number of points they can exchange for hints for various sections. 

Points earned during **VERIFY** Phase:

* 5 points for each requirement fulfilled by the team in the **BUILD** phase 
* 15 points for every major exploit found (components of an individual exploit cannot be combined, e.g. a public bucket that allows Read, Write and List is one exploit. 
-->

## Requirements

??? info "Click here for the account architecture"

	Account architecture: ![architecture](./images/architecture.png)

There are many teams working in this AWS account, including the web admins and the application admins.  The ulimate goal of this workshop is to set up the web admins so they can create a Lambda function to read an S3 bucket while making sure they are not able to impact the resources of other teams. The web admins should only have access to the following resources:

1. IAM policies and roles created by the web admins 
2. Lambda functions created by the web admins
3. S3 buckets: The web admins are allowed access to the bucket that starts  with`"web-admins-"` and ends in `"-data"`

## Presentation

If you are doing this workshop as part of an AWS event then there will usually be a 30 minute presentation before the hands-on exercise. Here is the [presentation deck](./presentation.pdf).



#### [Click here to go to the BUILD phase](./build.md)
