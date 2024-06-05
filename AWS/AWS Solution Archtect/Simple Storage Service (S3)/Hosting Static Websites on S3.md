### <span style="color:#00b050">Hosting Static Websites on S3 - Key Points</span>

#### <span style="color:#00b050">Overview</span>
- **Static Websites on S3**:
  - S3 can be used to host static websites (e.g., HTML websites).
  - Static websites do not change and do not require database connections.
  - S3 automatically scales to meet demand, making it ideal for high-traffic scenarios like movie previews.

#### <span style="color:#00b050">Steps to Host a Static Website on S3</span>
1. **Prepare Your Files**:
   - Download the resources: `index.html`, `error.html`, and bucket permissions JSON file.
2. **Create an S3 Bucket**:
   - Navigate to S3 in the AWS Console.
   - Click **Create Bucket**.
   - Enter a unique bucket name.
   - Select a region (e.g., US East North Virginia).
   - Uncheck "Block all public access" and acknowledge the warning.
   - Create the bucket.
3. **Enable Static Website Hosting**:
   - Go to the **Properties** tab of your bucket.
   - Scroll down to **Static website hosting** and click **Edit**.
   - Enable static website hosting.
   - Set `index.html` as the **Index document**.
   - Set `error.html` as the **Error document**.
   - Save changes.
4. **Upload Your Files**:
   - Go to the **Objects** tab.
   - Click **Add files** and upload `index.html` and `error.html`.
   - Click **Upload**.
5. **Make the Bucket Public**:
   - Go to the **Permissions** tab.
   - Scroll down to **Bucket policy** and click **Edit**.
   - Copy the bucket policy from the resources and paste it into the policy editor.
   - Ensure the policy includes the correct bucket name.
   - Save changes.

#### <span style="color:#00b050">Testing Your Website</span>
- **Access the Website**:
  - Go to the **Properties** tab and find the **Bucket website endpoint**.
  - Click the endpoint to view your website.
  - Test the error page by entering a non-existent URL.

#### <span style="color:#00b050">Exam Tips</span>
1. **Static Websites**:
   - Associate static websites with S3.
   - Remember that S3 hosts static content only, not dynamic content.
2. **Bucket Policies**:
   - Use bucket policies to make entire buckets public.
   - This is more efficient than making individual objects public.
3. **Automated Scaling**:
   - S3 scales automatically with demand, making it suitable for unpredictable traffic.
   - Ideal for scenarios where you need to handle high load without worrying about infrastructure.

This summary provides a clear understanding of how to host a static website on S3, along with key points to remember for the AWS Certified Solutions Architect - Associate exam.