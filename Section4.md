Section 4: IAM & AWS CLI

Identity and Access Management (IAM)

Root Account
    -   Has full access to all AWS services including:
        -   Able to manage IAM user
    -   Not suggested to use as main account to do everything

Instead of using Root account to do everything
Use IAM to:
    -   Create & manage users, groups, and roles
    -   Manage access to AWS services & resources
    -   Analyze access controls

Principal
    -   Make a request for an action or operation on an AWS resource
    -   Can be a person, application, federated user, or assumed role

IAM Users 
    -   A individual of user account that has their own credentials
IAM User Groups
    -   IAM Users can be grouped into IAM user groups to share the same policies in the assigned group
IAM Roles
    -   IAM Users & IAM User Groups can be assigned into IAM Roles without sharing credentials with others
    -   Permission are only valid while operating under the assumed role

Federated User
    -   Usually pointed to those user who are act as client who use our service that developed by developers


Security Policy Categories
Policy Types
    -   Set maximum permissions
        -   IAM permissions boundaries
        -   AWS Organizations Service Control Policies
    -   Grant permissions
        -   IAM identity-based policies
        -   IAM resource-based policies

Console Access => AWS Management Console
Programmatic Access => AWS CLI, AWS SDK