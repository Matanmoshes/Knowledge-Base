### <span style="color:#ffc000">Deep Dive into Spot Instances and Spot Fleets - Key Points</span>

>[AWS Documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/spot-requests.html)
#### Introduction
- **Objective**: Understand spot instances and spot fleets.
- **Components**:
  - Spot instances overview
  - Spot prices and spot blocks
  - Use cases for spot instances
  - Termination of spot instances
  - Spot fleets and launch pools
  - Strategies for using spot fleets
  - Exam tips

#### <span style="color:#ffc000">Spot Instances Overview</span>
- **Definition**: Utilize unused EC2 capacity at up to 90% discount compared to on-demand prices.
- **Use Case**: Stateless, fault-tolerant, or flexible applications (e.g., big data, containerized workloads, CI/CD, high-performance computing, testing and development).

#### <span style="color:#ffc000">Spot Prices</span>
- **Max Spot Price**: You set a maximum price you're willing to pay. Instances are provisioned if the spot price is below your max price.
- **Price Variability**: Spot prices vary based on capacity and region, down to individual availability zones.
- **Termination**: If spot price exceeds your max price, you have two minutes to stop or terminate your instances.

#### <span style="color:#ffc000">Spot Blocks</span>
- **Definition**: Prevent spot instances from being terminated for a specified duration, useful for workloads needing uninterrupted processing.

#### <span style="color:#ffc000">Use Cases for Spot Instances</span>
- **Suitable For**:
  - Big data and analytics
  - Containerized workloads
  - CI/CD and testing
  - Image and media rendering
  - High-performance computing
- **Not Suitable For**:
  - Persistent workloads (e.g., web servers, databases)
  - Critical jobs requiring constant uptime

#### <span style="color:#ffc000">Termination of Spot Instances</span>
- **Spot Requests**: Define the maximum price, number of instances, AMI, and request type (one-time or persistent).
- **Persistent Requests**: Need to cancel the spot request before terminating instances to avoid re-provisioning.
- **Steps**:
  1. Cancel the spot request.
  2. Terminate the instances.

![[Pasted image 20240611173018.png]]
#### <span style="color:#ffc000">Spot Fleets</span>
- **Definition**: Collection of spot instances and optionally on-demand instances to meet a specified target capacity.
- **Function**: Launches instances to meet target capacity within price constraints, attempts to maintain capacity if instances are interrupted.

#### <span style="color:#ffc000">Launch Pools</span>
- **Definition**: Define EC2 instance types, operating systems, and availability zones in different pools.
- **Function**: Spot fleet chooses the best implementation based on defined strategy.

#### <span style="color:#ffc000">Spot Fleet Strategies</span>
1. **capacityOptimized**: Instances come from the pool with the optimal capacity.
2. **diversified**: Instances are distributed across all pools.
3. **lowestPrice**: Instances come from the pool with the lowest price (default strategy).
4. **InstancePoolIsToUseCount**: Instances are distributed across a specified number of pools (used with lowestPrice).

#### <span style="color:#ffc000">Exam Tips</span>
1. **Cost Savings**: Spot instances save up to 90% compared to on-demand instances.
2. **Use Cases**: Suitable for non-persistent workloads and stateless applications.
3. **Spot Blocks**: Prevent termination for a specified duration.
4. **Spot Fleets**: Collection of spot and on-demand instances to meet capacity.
5. **Strategies**: Understand the different strategies for spot fleets and their applications.

#### <span style="color:#ffc000">Summary</span>
- Spot instances provide significant cost savings for non-critical, flexible applications.
- Spot fleets and launch pools help optimize instance usage and costs.
- Understand the different strategies and use cases for effective application in AWS environments.

