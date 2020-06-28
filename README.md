# AWS DevOps Essentials

## An Introductory Workshop on CI/CD Practices

몇 시간 만에 다양한 AWS 서비스를 효과적으로 활용하여 개발자 생산성을 개선하고 신제품 기능 출시 시간을 단축하는 방법을 빠르게 학습하십시오. 이 세션에서는 AWS CodeCommit (관리 소스 제어 서비스), AWS CodeBuild (완전히 AWS CodeCommit)를 포함한 타사 솔루션을 사용하여 지속적인 통합 및 제공에 대한 모범 사례를 점진적으로 채택하고 수용하는 규범적인 접근 방식을 시연합니다. 관리 형 빌드 서비스), Jenkins (오픈 소스 자동 빌드 서버), CodePipeline (완전히 관리되는 지속적 전달 서비스) 및 CodeDeploy (자동 응용 프로그램 배포 서비스). 또한 소프트웨어 릴리스 프로세스를 빠르고 자동화하며 안정적으로 만드는 데 도움이되는 모범 사례 및 생산성 팁을 중점적으로 다룰 것입니다.

전체 아키텍처에 대한 설명은 아래 다이어그램을 참조하십시오.

![DevOps Workshop Architecture](img/CICD_DevOps_Demo.png)

## Prerequisites

* **Configure AWS CodeCommit:** The easiest way to set up AWS CodeCommit is to configure HTTPS Git credentials for AWS CodeCommit. On the user details page in IAM console, choose the **Security Credentials** tab, and in **HTTPS Git credentials for AWS CodeCommit**, choose **Generate**. ![HTTPS Git Credential](./img/codecommit-iam-gc1.png)
        **💡 Note:** Make Note of the Git HTTP credentials handy. It will be used for cloning and pushing changes to Repo.
          Also, You can find detail instruction on how to configure HTTPS Git Credential [here](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-gc.html)
* **IAM Permissions:** Finally, for the AWS account ensure you have sufficient privileges. You must have permissions for the following services:

AWS Identity and Access Management

Amazon Simple Storage Service

AWS CodeCommit

AWS CodeBuild

AWS CloudFormation

AWS CodeDeploy

AWS CodePipeline

AWS Cloud9

Amazon EC2

Amazon SNS

Amazon CloudWatch Events

***

### **Important:**
Preferred regions for lab
- North Virginia US-EAST-1
- Oregon US-WEST-2

If you want to your region choice for the lab. Kindly the select the region which has all four Code* services and Cloud9 service. You can find the [region services list](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/). Stick to the same region throughout all labs. 
**Make sure you have not reached the VPC or Internet Gateway limits for that region. If you already have 5 VPCs/IGWs, delete at least one before you proceed or choose an alternate region.** 

# Labs
This workshop is broken into multiple labs. You must complete each Lab before proceeding to the next.

1. [Lab 1 - Build project on the cloud](1_Lab1.md) 
2. [Lab 2 - Automate deployment for testing](2_Lab2.md)
3. [Lab 3 - Setup CI/CD using AWS CodePipeline](3_Lab3.md)
4. [Lab 4 (Optional) - Using Lambda as Test Stage in CodePipeline](4_Lab4.md)




## Clean up

1. Visit [CodePipeline console,](https://console.aws.amazon.com/codepipeline/home) select the created pipeline. Select the Edit and click **Delete**.
2. Visit [CodeDeploy console,](https://console.aws.amazon.com/codedeploy/home) select the created application. In the next page, click **Delete Application**.
3. Visit [CodeBuild console,](https://console.aws.amazon.com/codebuild/home) select the created project. Select the Action and click **Delete**.
4. Visit [CodeCommit console,](https://console.aws.amazon.com/codecommit/home) select the created repository. Go to setting and click **Delete repository**.
5. Visit [Lambda console,](https://console.aws.amazon.com/lambda/home) select the created function. Select the Action and click **Delete**.
6. Visit [Cloudformation console,](https://console.aws.amazon.com/cloudformation/home) select the created stacks. Select the Action and click **Delete Stack**.
7. Visit [Cloud9 console,](https://console.aws.amazon.com/cloud9/home) select the created Environment. Select the Action and click **Delete**.
8. Visit [Simple Notification Service console,](https://console.aws.amazon.com/sns/home) select Topics. Select the created topic.  Select the Action and click **Delete topics**. Next select Subscriptions. Select the created subscription. Select the Action and click **Delete subscriptions**.

## License

This library is licensed under the Apache 2.0 License. 
