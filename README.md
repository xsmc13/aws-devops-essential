# AWS DevOps Essentials

## An Introductory Workshop on CI/CD Practices

몇 시간 만에 다양한 AWS 서비스를 효과적으로 활용하여 개발자 생산성을 개선하고 신제품 기능 출시 시간을 단축하는 방법을 빠르게 학습하십시오. 이 세션에서는 AWS CodeCommit (관리 소스 제어 서비스), AWS CodeBuild (완전히 AWS CodeCommit)를 포함한 타사 솔루션을 사용하여 지속적인 통합 및 제공에 대한 모범 사례를 점진적으로 채택하고 수용하는 규범적인 접근 방식을 시연합니다. 관리 형 빌드 서비스), Jenkins (오픈 소스 자동 빌드 서버), CodePipeline (완전히 관리되는 지속적 전달 서비스) 및 CodeDeploy (자동 응용 프로그램 배포 서비스). 또한 소프트웨어 릴리스 프로세스를 빠르고 자동화하며 안정적으로 만드는 데 도움이되는 모범 사례 및 생산성 팁을 중점적으로 다룰 것입니다.

전체 아키텍처에 대한 설명은 아래 다이어그램을 참조하십시오.

![DevOps Workshop Architecture](img/CICD_DevOps_Demo.png)

## Prerequisites

* **Configure AWS CodeCommit:**  AWS CodeCommit을 설정하는 가장 쉬운 방법은 AWS CodeCommit에 대한 HTTPS Git 자격 증명을 구성하는 것입니다, 보안 자격 증명 탭을 선택하고 AWS CodeCommit의 HTTPS Git 자격 증명에서 생성을 선택하십시오. ![HTTPS Git Credential](./img/codecommit-iam-gc1.png)

**💡 Note:** Git HTTP 자격 증명을 기록해 두십시오. 변경 사항을 복제하여 Repo로 푸시하는 데 사용됩니다.
           또한 HTTPS Git 자격 증명을 구성하는 방법에 대한 자세한 지침을 찾을 수 있습니다 [here](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-gc.html)
* **IAM Permissions:** 마지막으로 AWS 계정의 경우 충분한 권한이 있는지 확인하십시오. 다음 서비스에 대한 권한이 있어야합니다:

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

### **중요:**
각 사용자별 Region 확인
<img src="https://raw.githubusercontent.com/lormadus/aws-devops-essential/master/img/vpc.png">

실습에서 원하는 지역을 선택하십시오. 4 개의 Code * 서비스와 Cloud9 서비스가 모두있는 지역을 선택하십시오. [지역 서비스 목록] (https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services) 을 찾을 수 있습니다. 실습시에는 동일한 지역에서 진행합니다.. 
**해당 지역의 VPC 또는 인터넷 게이트웨이 제한에 도달하지 않았는지 확인하십시오. 5 개의 VPC / IGW가 이미있는 경우 계속 진행하거나 대체 지역을 선택하기 전에 하나 이상을 삭제하십시오..** 

# Labs
이 워크샵은 여러 실습으로 나뉩니다. 다음 단계로 진행하기 전에 각 실습을 완료해야합니다.

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
