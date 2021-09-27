	- manage users, groups, policies, roles, user credential, password policies, MFA and API keys
	
# IAM Users
	- entity representing person or service
	- users can be assigned
		□ an access key ID and secret access key
			® for programmatic access to AWS API, CLI, SDKs
			® are not same as password
			® cannot be used to login into AWS console
			® can be generated once; must be regenerated if lost
		□ a password 
			® to access management console
					
	- user cannot access anything by default
	- up to 5000 users can be created in a AWS account
	- each user account has unique name called Amazon Resource Number (ARN)
		
##### - root user
		□ created with email address used to sign up for AWS
				® known as root user credentials
		□ full administrative permissions
		□ best practices for root user
				® do not use root account for day to day functions
				® do not share root user credential
				® create an IAM user and assign administrative permissions
				® enable Multifactor Authentication 
					
##### - service accounts
			□ IAM user account that represent applications
			
##### - federating existing users
		□ users with existing accounts in a organisation and are allowed to login in AWS
		□ if users already have identities in a corporate directory
			® federate using SAML 2.0 
		□ if users already have internet identities 
			® federate using identity provider like OpenID Connect
	
#	IAM Groups
		- collection of users 
		- policies can be attached to groups
		- a group is  not an identity and cannot be identified as a principal in an IAM policy
		- groups cannot be nested
		- groups can be used to assign permissions to users
		- use principal of least privilege when assigning permissions
	
#####	Creating users and group in AWS console
			1. Go to IAM > Users > Add users
			2. On the details page provide username, password and choose permissions
			3. On permission add user to group by creating a new group
			4. Attach Tag as per requirement
			5. Review and create user
		
#####	Creating use and group in AWS CLI
		- Create user group
			§ aws iam create-group --group-name <group name>
			§ aws iam list-groups
		- Attaching policy 
			§ aws iam attach-group-policy --group-name Admins --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
			§ aws iam list-attached-group-policies --group-name Admins
		- Creating user
			§ aws iam create-user
			
#	IAM Roles
		- defined set of permissions for AWS service request
		- permissions can be delegated to resources without using username and password
		- can be used to obtain temporary security credentials to make API calls
		
##		Role delegation:
			§ Two policies:
					® permission policy - grants user required permissions
						◊ must be attached to the user in the trusted account
					® trust policy - specifies trusted accounts
			§ Wildcards cannot be specified as a principal
		
##		IAM roles in EC2 instances:
			§ can be used to grant applications to AWS API request
			§ one role can be assigned per instance
			§ role can be assigned during creation or after creation
			§ application retrieves temporary security credentials from the instance metadata
	
#	IAM Policies
		- defines permission 
		- can be applied to users, groups and roles
		- written in JSON 
		- permissions are implicitly denied - default
		- most restrictive policy is applied
		
		- IAM policy simulator
			§ tool to understand, test, and validate effects of policies
		
		- evaluation logic
			§ by default all requests are denied
			§ an explicit allow overrides the implicit deny
			§ an explicit deny overrides any explicit allows
	
#####		- Types of policies
#####			a. Identity based policies
				- control what actions the identity can perform on which resources, and under what conditions.
				- can be managed or inline policies
					® managed policies are standalone identity based policies that can be attached to multiple users, groups and roles 
					® inline policies that are created and managed and are embedded directly into a single user, group or role.
					
#####		b. Resource based policies
				- control what actions a specified principal can perform on that resource and under what condition.
				- are inline policies applied to individual resource
				- Type of resource based policies
					® Trust policy - attached to the an IAM role
						◊ trust policies define which accounts, users, roles, and federated users can assume the role.
	
####		- JSON example
			{
			  "Version": "2012-10-17",
			  "Statement": {
			    "Effect": "Allow",
			    "Action": "dynamodb:*",
			    "Resource": "arn:aws:dynamodb:us-east-2:123456789012:table/Books"
			  }
			}
	
#	AWS Security Token Service (STS)
		- allows to request temporary, limited privilege credential for users 
		- global service
			§ default endpoint sts.amazonaws.com
			§ region in which temporary credentials are requested must be enable
		- supports AWS CloudTrail
		
		- temporary security credential includes
			§ access key - access key ID and secret ID
			§ session token
			§ expiry date
			§ users or app that can use the credentials
			
		- STS versus long-term access key credentials
			§ STS are short term and expiry date can be set
				□ using credential after expiry does not allow access
			§ STS is generated dynamically and provided to the user at request
			§ User with permission can request for new credential after expiry of temporary security credential
	
		- session token can be request using API:
			§ AssumeRole
			§ AssumeRoleWithSAML
			§ AssumeRoleWithWebIdentity
			§ GetSessionToken
			§ GetFederationToken
		
		- users source for request can be: federated user (AD), federation with mobile app, and cross account access
	
#	Principals
		- entity that can take a action on an AWS resource
		- IAM users, roles, and applications are principals
		- includes federated users and assumed roles
	
#	Requests
		- action performed by principals
		- sent via console, CLI, SDKs, or APIs
		- requests gather information about:
			§ principal
			§ actions or operations principal want to perform
			§ aggregate permissions associated with principal
			§ environment data
				□ IP address, user agent, SSL status
			§ resource data
	
#	Authentication
#####	- Types of authentication
#####	§ console authentication: 
			® using username and password in AWS management console
			® users can be allowed to change password
					
#####	§ API or CLI authentication: 
			® using access key and secret key
			® one user can be assigned two active access keys at a time
			® can be used to make programmatic calls to AWS
			® keys can be created, modified, viewed or rotated
			® secret is return during the time of creation only
			® user access key can be disabled
					
#####	§ Service certificates
			® authentication using TLS certificates
			® AWS Certificate Manger should be used to manage certificates
		
			
		
		
#	Authorisation
		- permissions of principal checked against policies
		- only root user can access all resources by default
	
#	Resources
		- entity in a service
		- each AWS service defines a set of actions that can be performed on the resource
		- examples
			§ EC2 instance
			§ S3 bucket
			
#	Role based access control (RBAC)
		- defines permission based on user role
		- applied by creating different policies for different job functions
		
#	Attribute based access control (ABAC)
		- authorisation strategy that defines permissions based on attributes.
			§ attributes are tags in AWS
			§ tags can be attached to resources, users and roles
			
		- example
			§ create three roles with tags access-project tag key
			§ set the tag value of the first role to Heart, second to Sun, and the third to Lightning
			§ a single policy can be used to allow access when role and resources with same value for access-project
	
#	ABAC versus RBAC
		- ABAC permissions scale with innovation
		- requires fewer policies
		- team can change and grow quickly
		- granular permission are possible
		- use employee attributes form corporate directory
	
	
#	Security best practices
		- Lock away your AWS account root user access keys
			§ do not use AWS root user access key
			§ create IAM user for administrative permissions
			§ never share root user account credentials
			§ use strong password 
			§ enable multi factor authentication (MFA) on root user account
			
		- Create individual IAM users
			§ do not use root account for regular administrative tasks
			§ create individual user account for them who need access
			§ AWS recommends create new users without permissions and required them to change password immediately
			
		- Use user groups to assign permissions to IAM users
			§ create user group according to the permission required
			§ define sets of permissions and assign it to the user group
			
		- Grant least privilege
			§ understand access level groupings
			§ validating policies
			§ generate a policy based on access activity
			§ use last accessed information
			§ review account events in AWS CloudTrail
			
		- Get started using permissions with AWS managed policies
			§ create custom policy with only required permissions
			§ create a process to allow to request more permissions
			§ managed policies cover common use cases and readily available for use
			§ managed policies can be attached to an IAM identity
			
		- Validate your policies
		
		- Use customer managed policies instead of inline policies
			§ inline policies exist only on an IAM identity
			§ managed policies can be viewed in one place in console
			
		- Use access levels to review IAM permissions
			§ review and  monitor IAM policies
			§ make sure policies grant least privilege
			§ Access level can be: List, Read, Write, Permissions, Management or Tagging
			
		
		- Configure a strong password policy for your users
			§ create custom password policy
			§ policy must require to create strong password and rotate them periodically
			§ define minimum length, required characters
			
		- Enable MFA
			§ enable for all users
			§ for privileged IAM users AWS recommends using hardware MFA devices or U2F security keys
			
		- Use IAM roles for applications that run on Amazon EC2 instances
			§ Applications that run on the EC2 instance can use the role's credentials when they access AWS resources
			
		- Use roles to delegate permissions
			
		- Do not share access keys
		
		- Rotate credentials regularly
		
		- Remove unnecessary credentials
		
		- Use policy conditions for extra security
			§ define conditions under which policies allow access to resource
			
		- Monitor activity in your AWS account
			§ use services like CloudFront, CloudTrail, CloudWatch, Config and S3 to monitor AWS activity
		
	
LINK: [https://docs.aws.amazon.com/iam/index.html]

