---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Workshop Overview

This chapter contains hands-on AWS labs. It retains the existing exercises on private S3 access through VPC endpoints and adds a project-based workshop for **UniCloud DMS**, a serverless document management application.

The UniCloud DMS workshop is designed to guide learners from the existing React/Vite frontend prototype to an AWS-backed application using managed authentication, private object storage, a REST API, and serverless business logic.

{{% notice warning %}}
**Implementation status:** The UniCloud DMS instructions are a guided implementation plan until each AWS step has been performed and verified. Screenshot placeholders are intentional. Replace them with genuine screenshots from the learner's own environment after completing each step.
{{% /notice %}}

#### Workshop content

1. [Workshop overview](5.1-Workshop-overview/)
2. [Prerequisites](5.2-Prerequiste/)
3. [Access S3 from a VPC](5.3-S3-vpc/)
4. [Access S3 from on-premises](5.4-S3-onprem/)
5. [VPC endpoint policies (bonus)](5.5-Policy/)
6. [Clean up the hybrid S3 lab](5.6-Cleanup/)
7. [Build UniCloud DMS on AWS](5.7-UniCloud-DMS/)

The UniCloud DMS workshop covers authentication, DynamoDB metadata, private S3 storage, Lambda, API Gateway, frontend integration, hosting, testing, monitoring, and cleanup. Use the steps in order and keep all resources in a consistent AWS Region unless a step explicitly says otherwise.
