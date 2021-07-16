# Identity and Access Management - IAM
- allows to manage users and their level of access 
- centralized control , shared access, granular permissions, 
- identity federation (including active directory, facebook, linkedin etc)
- **multifactor authentication - advisable to set up on the root account**
- use google authenticator app for this
- supports PCI DSS compliance
  - Policy for password rotation 
- IAM users - permanent / long term - they have NO permissions when first created
- new users are assigned access id and secret key - this is different from password
- cannot use access id and key to access console - this is used to access APIs and CLI
- roles - assign to users
- group - set of users with common characteristics
- policies - are permissions that an user/group can do - made up of policy documents in json format
- arn - Amazon resource number
- password policy applies to all users - so to change policy for specific user(s), do it from either user level or group level
- all IAM operations are done on global region  (universal) and not one particular region
- for non root user to access billing preferences, it has to be enabled - it can be enabled only from root account - explicitly enable billing access to IAM users
- to create an alarm, go to cloudwatch service and create a new alarm (send email if monthly charges > 5 USD)

**AWS a/c details**
- root account is the account created first with email - it has full admin access - god mode
```
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Effect": "Allow",
           "Action": "*",
           "Resource": "*"
         }
       ]
     }
```
- The 'Eeffect' in the above json can be set to Deny
- **An explicit Deny always overrides any Allow that may be granted to the user in any other policy**
- **All permissions are implicitly denied unless explicitly granted**
- Console sign-in link: https://smalali.signin.aws.amazon.com/console 
- root user - sundeepmalali@gmail.com/***
- admin user - admin/****
             - arn:aws:iam::872833689618:user/admin
             - access key id - AKIAIDLKFLNKOLXWUJ7Q      secret access key - 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
             - has both programmatic and console access
- groups - admin - policy attached - AdministratorAccess - arn:aws:iam::872833689618:group/admin

- command line user details
  - username : cliuser,      access key id:  AKIAJPROTIVX54U7NPBA,  secret access key - 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

**Quiz tips**
- Power user access allows - Access to all AWS services except the management of groups and users within IAM.
- To add new users to your account, - best practices are to create a custom sign-in link and also create new users with requried permissions
