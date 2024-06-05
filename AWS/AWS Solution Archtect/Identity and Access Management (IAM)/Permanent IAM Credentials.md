### <span style="color:#ff0000">Permanent IAM Credentials</span>


#### <span style="color:#ff0000">Building Blocks of IAM</span>

1. **Users**:
   - Should represent a single physical person.
   - Users should not be shared among multiple people.

2. **Groups**:
   - Users are organized into groups based on job functions (e.g., administrators, developers, HR).
   - Policies should be assigned to groups, and users will inherit permissions from their groups.

3. **Roles**:
   - Used internally within AWS to grant permissions to AWS services.
   - Example: Allowing EC2 instances to access S3 buckets.

#### <span style="color:#ff0000">Best Practices</span>

- **Policy Assignment**:
  - Assign IAM policies to groups rather than individual users for easier management.
  - Users should inherit permissions from groups.

- **Principle of Least Privilege**:
  - Grant users and groups the minimum amount of privileges needed to perform their job.
  - Different roles (e.g., developers, finance) should have appropriate permissions according to their needs.

#### <span style="color:#ff0000">IAM in AWS Console
</span>
- **Creating Users and Groups**:
  - Create a user group first, then add users to the group.
  - Users inherit permissions from the group.

- **Policy Management**:
  - AWS provides pre-defined policies for common job functions.
  - Custom policies can be created and managed.
  - Managed policies by AWS have an AWS icon.

- **Tags**:
  - Tags are key-value pairs that help in resource management and auditing.
  - Example: Tagging resources by department or employee ID.

- **Password Policies**:
  - Customize password policies, including requirements for character types and password reuse prevention.

- **Programmatic Access**:
  - Access key ID and secret access key are used for CLI or API access, not for console login.
  - Ensure keys are saved as they are only viewable once.

#### <span style="color:#ff0000">Identity Providers</span>

- **Identity Federation**:
  - Connect IAM to corporate directories (e.g., Active Directory).
  - SAML and OpenID Connect are supported for federation.

#### <span style="color:#ff0000">Exam Tips</span>

1. **IAM is Global**:
   - IAM applies globally, not regionally.

2. **Root Account**:
   - The root account has full access and should be secured. It should not be used for daily operations.

3. **User Creation**:
   - New users have no permissions by default. Permissions must be assigned via groups or policies.

4. **Access Keys**:
   - Used for programmatic access, different from console login credentials.

5. **Password Rotation**:
   - Implement and customize password rotation policies.

6. **IAM Federation**:
   - Use Identity Federation to integrate AWS with existing user accounts (e.g., Active Directory).


---



### <span style="color:#ff0000">Exam Tip (IAM) Section</span>

#### <span style="color:#ff0000">Securing the Root Account</span>

1. **Enable Multifactor Authentication (MFA)**:
   - Protect the root account with MFA.
   - Aim to stop using the root account for daily administration.

2. **Create an Administrator Group**:
   - Assign appropriate permissions to the group.
   - Create user accounts for administrators.
   - Add these users to the administrator group.
   - Avoid using the root account by using administrator accounts instead.

#### <span style="color:#ff0000"> IAM Policy Documents</span>

- **Structure and Format**:
  - Policies are written in JSON (JavaScript Object Notation).
  - A simple policy example: 
    - `Effect: Allow`
    - `Action: *`
    - `Resource: *`
  - This policy grants full admin access.

- **Understanding Policies**:
  - Be able to read and interpret JSON policy documents.
  - Practice with various policy documents and read AWS documentation.

#### <span style="color:#ff0000">Key Points for the Exam</span>

1. **IAM is Universal**:
   - Applies globally, not regionally.
   - Users and groups are created universally.

2. **Root Account**:
   - Created when setting up AWS.
   - Has complete admin access.
   - Should be secured and not used for daily operations.

3. **User Creation**:
   - New users have no permissions initially.
   - Add users to groups to inherit permissions.
   - Directly assigning policies to users is not best practice.

4. **Access Keys**:
   - Access key and secret access key are for programmatic access.
   - Different from usernames and passwords.
   - Save them securely as they are viewable only once.

5. **Password Rotation Policies**:
   - Create and customize password rotation policies.

6. **IAM Federation**:
   - Combine existing user accounts with AWS.
   - Use SAML for integration with services like Microsoft Active Directory.

