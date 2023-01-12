# Infrastructure

## AWS Zones

- Primary Zone : `us-east-2`
  - AZS : `us-east-2a`, `us-east-2b`, `us-east-2c`
- Secondary Zone : `us-west-1`
  - AZS : `us-west-1b`, `us-west-1c`

## Servers and Clusters

### Table 1.1 Summary

| Asset                   | Purpose                                                       | Size        | Qty | DR                      |
| ----------------------- | ------------------------------------------------------------- | ----------- | --- | ----------------------- |
| S3 Bucket               | for storing terraform configurations                          | -           | 2   | Replicated in DR Region |
| EC2 Instance            | for deploying flask web application                           | t3.micro    | 6   | 3 in each region        |
| VPC                     | for providing private cloud in Multi-AZs with IP, Subnet etc. | -           | 2   | 1 in each region        |
| Key pairs               | for connecting to ec2  instances remotely                     |             | 2   | 1 in each region        |
| Kubernetes Cluster      | for host monitoring environment                               |             | 2   | 1 in each region        |
| Kubernetes Cluster Node | for running monitoring stack                                  | t3.medium   | 4   | 2 in each region        |
| ALB                     | Load balancing the application traffic                        | -           | 2   | 1 in each region        |
| RDS Cluster             | Managed MySQL DB clusters                                     | -           | 2   | 1 in each region        |
| RDS Aurora Instances    | Store application data                                        | db.t2.small | 4   | 2 in each region        |
| AMI image               | for virtual machine images to be used by EC2 instances        | -           | 2   | 1 in each region        |
| GitHub                  | for hosting the application code and IaC code                 | -           | 1   | -                       |

### Descriptions

- **S3 Buckets:** These buckets will store the state of our terraform code as well as backups of db’s.
- **EC2 Instances:** These instances will be used for deploying flask web application. It has Ubuntu OS and runs the flask app with prometheus exporter.
- **VPC (Virtual Private Cloud):** This provides a private cloud with IPs, Availability Zones, Subnets for other services.
- **Key pairs:** It is used to connect to EC2 instances via SSH
- **Kubernetes Cluster & Nodes:** Cluster has nodes which host the Prometheus & Grafana monitoring environment.
- **Application Load Balancer:** Load balancers will be used to distribute the load on application servers. It provides traffic redirection amongst servers.
- **RDS Cluster & Instances:** Cluster will be used to store our application data. The 2 instances (reader & writer) store application data and can be flipped over in the event of fail-over
- **AMI Image:** An Amazon Machine Image (AMI) is a [master image](https://searchservervirtualization.techtarget.com/definition/golden-image) for the creation of virtual servers -- known as [EC2](https://www.techtarget.com/searchaws/definition/Amazon-Elastic-Compute-Cloud-Amazon-EC2) instances in the Amazon Web Services ([AWS](https://www.techtarget.com/searchaws/definition/Amazon-Web-Services)) environment. The machine images are like templates that are configured with an 
  operating system and other software that determine the user's operating 
  environment.
- **GitHub**: It is used for hosting and version control of application source code and IaC (terraform).

## DR Plan

### Pre-Steps:

- Create/Use AMI images needed for the application deployment
- create S3 buckets in regions to maintain the terraform state.
- use *Terraform* (IaC tool) to deploy infrastructure to multiple regions with the same configurations.
- Ensure the infrastructure is set up and working in the DR site.

## Steps:

- In case of **fail-over application** situation change site DNS to point to Zone-2 load balancer instead of Zone-1
- In case of **fail-over database** situation, primary writer instance of db cluster in the region will flip the writer instance with the reader instance. Also, deleting the regional db cluster in primary region will promote the replica db cluster in secondary region as regional db cluster
