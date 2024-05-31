### Controlling User Actions with IAM Policy Documents


#### Permissions with IAM

- **IAM Policy Documents**: Permissions in AWS are assigned using policy documents written in JSON (JavaScript Object Notation).
- **Basic Structure**:
  - **Version**: Specifies the version of the policy language.
  - **Statement**: The main element containing the policy specifics.
    - **Effect**: Determines if the policy allows or denies access (e.g., "Allow").
    - **Action**: Specifies what actions are allowed or denied (e.g., "*").
    - **Resource**: Specifies which resources the actions apply to (e.g., "*").
  - Example: A policy that allows all actions on all resources, granting full administrator access.

#### Assigning Policies

- Policies can be assigned to:
  - **Users**
  - **Groups**
  - **Roles**
- **Best Practices**:
  - Assign policies to groups rather than individual users for easier management.
  - Users inherit permissions from the groups they are part of.

#### AWS Console

- **Accessing IAM**:
  - IAM is a global service, not regional.
  - Users, policies, and roles created in IAM apply across all regions.
- **Viewing Policies**:
  - Managed policies by AWS have an AWS icon.
  - Custom policies can be created and will not have the AWS icon.
  - The console provides a list of policies, including AWS-managed and custom ones.
  - You can view and edit the JSON of any policy.

#### Exam Tips

- Understand the structure and components of IAM policy documents.
- Familiarize yourself with reading and interpreting JSON policy statements.
- Practice navigating the IAM console and viewing different policies.
- Pay attention to the specifics of permissions, particularly around S3 and other common services.

#### Practical Steps

- Before the exam, explore the IAM console and review various policies.
- Practice creating and assigning policies to users and groups.
- Ensure you are comfortable with the syntax and structure of JSON policy documents.

This summary provides the essential points and practical steps to understand IAM policy documents and prepare for the AWS Certified Solutions Architect - Associate exam.