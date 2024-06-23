### Amazon Machine Images (AMIs)

In this lecture, we will cover the fundamentals of Amazon Machine Images (AMIs), their types, and how to use them. We'll explore EBS volumes vs. Instance Store, review instance store volumes, and EBS volumes, and then conclude with exam tips.

### What is an AMI?

- **Definition:** An Amazon Machine Image (AMI) provides the information required to launch an instance.
- **Usage:** You must specify an AMI when you launch an instance. It includes the configuration for the instance's operating system, architecture, and storage for root device volumes.

### Five Things to Base Your AMI On

1. **Region:** The geographical area where your instances are hosted.
2. **Operating System:** The OS that will run on your instance (e.g., Amazon Linux, Ubuntu, Windows).
3. **Architecture:** Whether the instance is 32-bit or 64-bit.
4. **Launch Permissions:** Controls who can launch instances from the AMI.
5. **Storage for Root Device Volumes:** Determines whether the root volume is EBS-backed or instance store-backed.

### EBS Volumes vs. Instance Store

- **EBS (Elastic Block Store):**
    - The root device for an instance launch from the AMI is an EBS volume created from an Amazon EBS snapshot.
    - **Persistent storage:** Data persists even if the instance is stopped.
    - **Flexible:** You can stop, start, and reboot without losing data.
    - **Durability:** You can set root device volumes to be kept upon instance termination.
- **Instance Store:**
    - The root device for an instance launch from the AMI is an instance store volume created from a template stored in Amazon S3.
    - **Ephemeral storage:** Data is lost if the underlying host fails or the instance is terminated.
    - **Rebooting:** You can reboot without losing data, but stopping is not allowed.
    - **Temporary:** Suitable for temporary storage that is not critical.

### Demo and Practical Tips

1. **Creating Instances with Different Storage:**
    - Create an instance with EBS volume and set it to delete or retain on termination.
    - Create an instance with instance store-backed storage.
2. **Deleting Instances:**
    - **EBS-backed:** If set to delete on termination, the volume is deleted with the instance. Otherwise, it persists.
    - **Instance Store-backed:** Data is lost if the instance is stopped or the host fails.
3. **Managing Volumes:**
    - **Modify EBS volumes:** Change size, type, and configuration without stopping the instance.
    - **Instance Store:** Cannot stop; only terminate.

### Exam Tips

1. **Instance Store Volumes:**
    - Also known as ephemeral storage.
    - Cannot be stopped; data is lost if the underlying host fails.
    - Suitable for temporary, non-critical storage.
2. **EBS Volumes:**
    - Can be stopped and started without losing data.
    - By default, root device volumes are deleted on termination, but this can be configured to retain the volume.
    - Persistent and reliable storage for critical data.
3. **AMIs:**
    - An AMI is a blueprint for an EC2 instance, including OS, architecture, storage type (EBS or instance store), and configuration.

By understanding these key points, you will be well-prepared to answer questions about AMIs, EBS, and instance store volumes on the AWS Solutions Architect Associate exam.

---

# Reference

[Prebaking vs. bootstrapping AMIs - Overview of Deployment Options on AWS](https://docs.aws.amazon.com/whitepapers/latest/overview-deployment-options/prebaking-vs.-bootstrapping-amis.html)

[What's the difference between instance store and EBS?](https://repost.aws/knowledge-center/instance-store-vs-ebs)