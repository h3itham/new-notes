#### General

1. What is the difference between VM and container?
2. What books do you read?
3. What is the delta backup mean?
4. What is the difference between sync and async in development?
5. What is the database Engine mean?
6. What are the different stages in CI pipelines?
7. What is the artifact in any CI/CD Pipeline?
8. What is Prometheus?
9. What are the advantages of MongoDB or NoSQL database?
10. Why are we running databases on Kubernetes?
11. What is DevOps?
12. What are the benefits of DevOps?
13. What are the anti-patterns in DevOps?
14. What is the difference between unit testing and integration testing?
15. What is a multistage Docker container?
16. Why do you want to be a DevOps Engineer?
17. What is cloud computing?
18. What is the difference between cloud and virtualization?
19. What are the different types of hypervisors?
20. What is API?
21. What is a cloud model?
22. What is Ceph technology?
23. What is VLAN in one word?
24. What is cloud-init?
25. What are the core operations of DevOps with application development and with infrastructure?
26. What are the types of HTTP request methods?
27. What would you check if a Linux build server suddenly starts getting slow?
28. What is the build process in DevOps?
29. Explain the term 'Canary release'.
30. Explain Blue-Green Deployment.
31. What is the post-mortem meeting with reference to DevOps?
32. Name three important DevOps KPIs.
33. What is the need for DevOps?
34. What is a cluster?
35. What does TDD mean in software?
36. What do you consider while choosing a DevOps tool?
37. What is commit in git?
38. What is merge in github?
39. What is caching? How does it work? Why is it important?
40. What are stateless and stateful applications?
41. What is reliability?
42. What is availability?
43. What is unit test mean?
44. What are lambda functions?
45. How do containers communicate in Kubernetes?
46. What is CNI in Kubernetes?
47. What does shift left mean?
48. What is GitOps?

#### AWS

1. What is CIDR Block?
2. What is IAM user?
3. What is IAM Role?
4. What is AMI?
5. How can we send a request to Amazon S3?
6. What are the different types of instances?
7. What is the difference between storage types in AWS?
8. What are the pricing model parts in S3?
9. What is the difference between identity-based policy and resource-based policy?
10. How can we prevent object accidental delete in S3 bucket?
11. What is STS?
12. How can you send email to sysadmin if EC2 instance stopped?
13. How can you give user from outside AWS temporary access to object in S3 bucket?
14. What is API Gateway?
15. What is the default storage class in S3?
16. What are roles?
17. What is the edge location used for?
18. Name the AWS service that exists only to redundantly cache data and images.
19. Explain Geo Restriction in CloudFront.
20. What is the difference between CloudWatch and CloudTrail?
21. In which situation would you select provisioned IOPS over standard RDS storage?
22. What is the difference between Launch Configuration and Launch Template?
23. Name some AWS services which are not region specific.

#### AWS Scenario based questions

1. How would you design a VPC architecture for a 2-tier HA and scalable application?
2. How to restrict outbound internet access for resources in one subnet but allow in another?
3. How to allow internet access for instances in private subnets?
4. How to enable EC2s to communicate using private IPs?
5. How to implement strict network access control for VPC resources?
6. How to set up an isolated environment for sensitive workloads in VPC?
7. How to allow secure access to AWS services (like S3) from within VPC?
8. Steps to setup a secured VPC with subnets, NACLs, Security Groups, etc.

#### Linux

1. How can you list your CPU info?
2. How can you see your current kernel version?
3. What is the difference between softlink and hardlink?
4. How to secure SSH access on a server?
5. What will you do if user can't write in a specific directory?
6. How to schedule a backup for a folder at a specific period?
7. What to do if a critical application has crashed for diagnostics?
8. What is rsync command?
9. What is the difference between process and threads?
10. What is the find command?
11. What is /etc/resolv.conf file?
12. What is the difference between relative and absolute path?
13. What is the difference between process and daemon?
14. What is network bonding on linux?
15. What is umask command?
16. How would you recursively remove empty directories in a directory?
17. What do you understand about the standard streams?
18. How can you mount new partitions?
19. How can you unmount devices?

#### CICD

1. What is CI/CD pipeline?
2. When should I use Github Action or Jenkins?
3. What is the difference between declarative and scripted pipeline in Jenkins?
4. How do you schedule a Jenkins job?
5. What strategies would you use to scale Jenkins?
6. How do you use Multi-branch pipeline and write Jenkinsfile for each environment?
7. What scripting language is Jenkins pipeline syntax based on?
8. What are different ways to trigger pipeline?
9. What is the difference between parameter and environment variables in Jenkins?
10. What are different ways to backup Jenkins server?
11. If there is a broken build in Jenkins project, what will you do?

#### Docker

1. What is the main problem that Docker solves?

#### Kubernetes

1. What is Kubernetes?
2. What is the purpose of Haproxy-ingress and Nginx-ingress?
3. What exit code indicates an application error?
4. What exit code indicates the application tried to access a non-existent file?
5. What is a static pod in Kubernetes?
6. What is the difference between Kubernetes controller and operator?
7. What is orchestration in software and DevOps?
8. What is the difference between Docker and Kubernetes?
9. Why use pod rather than container in Kubernetes?
10. Can you create pod with two containers and how?
11. Why choose Kubernetes for orchestration?
12. What is the master node architecture?
13. What is the worker node architecture?
14. What are Persistent Volume and Persistent Volume Claim used for?
15. What is the k8s Operator?
16. How does Kubernetes scheduler determine where to place pods?
17. How does Kubernetes handle secret management? What are best practices?
18. How would you implement zero downtime deployment in Kubernetes?
19. Strategies for managing stateful applications in Kubernetes?
20. How would you optimize resource usage in k8s?
21. How can you secure a k8s cluster?
22. How can you ensure high availability for etcd in k8s cluster?
23. What is the role of service mesh?
24. How would you approach Kubernetes capacity planning?
25. What is a headless service?
26. Steps to upgrade Kubernetes cluster?

#### Terraform

1. What is Terraform?
2. What problems does Terraform solve?
3. What does the `init` command do?
4. How to create multiple instances in Terraform?
5. How to create multiple instances with unique names?
6. Why can't we use Ansible like Terraform for infrastructure creation?
7. What is the strength point of Terraform?
8. What does `terraform validate` do?
9. How to delete a specific resource in Terraform?
10. What are the different modules in Terraform?
11. How can we manage Terraform code in different environments?
12. What are the drawbacks of Terraform?
13. What are local variables?
14. What is the lifecycle resource in Terraform?
15. How can we provide Terraform with input information?
16. What is Null resource in Terraform?
17. What is Terraform Cloud?
18. List some Terraform CLI commands.
19. How to ignore duplicate resource error during terraform apply?
20. How to store sensitive data in Terraform?
21. Explain state file locking.
22. How will you control and handle rollbacks when something goes wrong?
23. How does Terraform ensure idempotency of resource provisioning?
24. Describe the lifecycle of a Terraform resource.
25. Explain Terraform workspaces and when to use them.
26. What is the state file and its importance?
27. What is Terraform backend?
28. What is a module in Terraform?
29. What is the importance of Terragrunt for Terraform?
30. Explain state locking in Terraform.
31. How to lock Terraform module version?
32. How would you upgrade a plugin in Terraform?
33. What is Terraform file structure?

#### Jenkins

1. What are Jenkinsfile, Pipeline, and Multibranch?
2. What are different types of Jenkins jobs?
3. Explain the difference between Declarative and Scripted pipeline in Jenkins.
4. How do you trigger a Jenkins pipeline automatically?
5. What is the purpose of the post section in a Jenkins pipeline?
6. How do you integrate Jenkins with Docker/Kubernetes?
7. What is Jenkins shared libraries?
8. How can you implement rollback strategies in Jenkins?

#### Gitlab CI/CD

1. Why are jobs in Gitlab CI/CD self-contained?
2. What is a stage in Gitlab CI/CD?
3. Can jobs in the same stage run in parallel?

#### System Design interview

1. You're given a list of 30 million HTTP endpoints. How would you design a script or system that classifies each endpoint as either 'working' or 'not working' efficiently, scalably, and with fault tolerance?
