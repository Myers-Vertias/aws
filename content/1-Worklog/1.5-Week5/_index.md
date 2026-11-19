---
title: "Week 5 Worklog"
date: 2026-10-16
weight: 1
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives

* Study the remaining AWS services in the document management project's architecture.
* Understand the roles of AWS Lambda, Amazon S3, Amazon DynamoDB, IAM, and Amazon CloudWatch.
* Learn how these services can work together in a serverless application.

### Tasks Completed This Week

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| Friday | **Study backend and storage services for the project**<br>• Learn about AWS Lambda and how functions run application tasks without managing servers. Review using Lambda to start and stop EC2 instances to optimize costs.<br>• Study Amazon S3, including buckets and objects, file storage, and access control.<br>• Learn about Amazon DynamoDB as the NoSQL database for document metadata.<br>• Study IAM roles and the permissions Lambda needs to access AWS services following the principle of least privilege.<br>• Learn how Amazon CloudWatch Logs, metrics, and alarms help monitor application activity and detect errors.<br>• Map the intended service flow: API Gateway invokes Lambda; Lambda works with DynamoDB and S3; CloudWatch receives logs and metrics. | 10/16/2026 | 10/16/2026 | [Serverless Journey](https://000078.awsfcaj.com/vi/); [Optimize EC2 Costs with Lambda](https://000022.awsfcaj.com/vi/); [CloudWatch Workshop](https://000008.awsfcaj.com/vi/); [Amazon S3](https://000057.awsfcaj.com/vi/) |

### Week 5 Achievements

* Understood AWS Lambda's role in running backend logic in a serverless architecture.
* Learned that Amazon S3 stores files while Amazon DynamoDB stores metadata in the project architecture.
* Understood how IAM roles give Lambda the permissions needed to access AWS resources.
* Learned how Amazon CloudWatch supports log review, metrics monitoring, and alarms.
* Mapped how these services can work with Cognito, Amplify, and API Gateway studied in Week 4 to form the overall architecture.


