### <span style="color:#00b050">Securing S3 Buckets with Block Public Access - Key Points</span>

#### <span style="color:#00b050">Object ACLs vs. Bucket Policies</span>

- **Object ACLs (Access Control Lists)**:
  - Work on an individual object level.
  - Allow you to make specific files public or set permissions for certain AWS users.
  - Fine-grained control over individual files.

- **Bucket Policies**:
  - Apply to the entire bucket.
  - Used to make all objects in a bucket public.
  - More suitable for broader access management.

#### <span style="color:#00b050">Creating an S3 Bucket</span>
1. **Log in to AWS Console**: Navigate to Services > Storage > S3.
2. **Create a Bucket**:
   - Click on "Create Bucket".
   - Enter a unique bucket name.
   - Choose an AWS region for the bucket.
   - Leave other settings like Object Ownership, Block Public Access, Versioning, and Encryption at their defaults for now.
3. **Upload Files to Bucket**:
   - Click on the created bucket.
   - Use "Add Files" to upload files.
   - After uploading, you will receive a success message with an HTTP 200 status code.

#### <span style="color:#00b050">Making Objects Public</span>
1. **Initial Steps**:
   - By default, objects in S3 buckets are private.
   - Attempting to access an object URL without proper permissions will result in an access denied message.
2. **Adjusting Permissions**:
   - Go to the bucket's "Permissions" tab.
   - Edit "Block Public Access" settings and uncheck the necessary boxes to allow public access.
   - Save changes and confirm by typing "confirm".
3. **Enabling Object ACLs**:
   - Scroll down to "Object Ownership" and edit settings to enable ACLs.
   - Acknowledge the warning and save changes.
4. **Making Objects Public Using ACLs**:
   - Go back to the bucket and select the object.
   - Use the "Actions" menu to choose "Make Public Using Object ACL".
   - Confirm the action to make the object public.
5. **Verifying Public Access**:
   - Click on the object URL to verify that it is now accessible to anyone on the internet.

#### <span style="color:#00b050">Exam Tips</span>
- **Default Privacy**: Buckets and objects are private by default.
- **Public Access**: You must allow public access for both the bucket and individual objects to make them public.
- **Object ACLs**: Used for making individual objects public.
- **Bucket Policies**: Used for making entire buckets public.
- **HTTP 200 Code**: Indicates a successful object upload.

### <span style="color:#00b050">Practical Steps</span>
- **Logging in to AWS Console**: Navigate to Services > Storage > S3.
- **Creating an S3 Bucket**: Enter a unique name and select a region.
- **Uploading Files**: Use "Add Files" to upload and verify successful upload with HTTP 200 status.
- **Adjusting Permissions**: Edit "Block Public Access" and enable Object ACLs.
- **Making Objects Public**: Use the "Actions" menu and confirm public access.
- **Verifying Access**: Check object URLs to ensure they are publicly accessible.

This summary covers the essential steps and security considerations for managing S3 bucket permissions, which are crucial for the AWS Certified Solutions Architect - Associate exam.




