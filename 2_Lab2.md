
## Lab 2 - 테스트를 위한 자동 배포

### Stage 1: 테스트를 위한 환경 설정

1. 다음 AWS CLI 명령을 사용하여 CloudFormation 스택을 실행하십시오:   (Terraform 으로 인프라를 구성한 경우 아래 작업 불필요)
   스택이름과 템플릭 주소 수정  (ProdWebApp01, DevWebApp01 TAG 정보 수정 => user30-ProdWebApp01, user30-DevWebApp01)
   S3 버킷 이름 수정 (235,236 라인)

```console
user:~/environment/WebAppRepo (master) $ aws cloudformation create-stack --stack-name user**-DevopsWorkshop-Env \
--template-body https://raw.githubusercontent.com/2mileslab/aws-devops-essential/master/templates/02-aws-devops-workshop-environment-setup.template \
--capabilities CAPABILITY_IAM
```

**_Note_**
  - 위 스택을 통해 VPC1개, 1개의 서브넷, 라우팅 테이블, 인터넷 게이트웨이, 두개의 EC2 인스턴스가 생성됩니다. EC2 인스턴스는 AWS CodeDeploy agent를 User Data 통해서 설치하게 됩니다.

  - Amazon Linux, RHEL, Ubuntu나 윈도우에서 CodeDeploy 에이전트를 설치하려면 다음 문서를 참조 바랍니다. 
  [Codedeploy 에이전트 설치 참고 문서](http://docs.aws.amazon.com/codedeploy/latest/userguide/codedeploy-agent-operations-install.html) 
 
***

### Stage 2: CodeDeploy Application 및 Deployment Group 생성

1. CodeDeploy 애플리케이션 생성을 위해 아래 명령어를 실행합니다.

```console
user:~/environment/WebAppRepo (master) $ aws deploy create-application --application-name DevOps-WebApp
```

2. 다음을 실행하여 배포 그룹을 생성하고 지정된 그룹을 지정된 애플리케이션 및 사용자의 AWS 계정과 연결합니다. 서비스 롤은 CloudFormation으로 생성한 **DeployRoleArn 값** 으로 변경합니다.

```console
user:~/environment/WebAppRepo (master) $ echo YOUR-CODEDEPLOY-ROLE-ARN: $(aws cloudformation describe-stacks --stack-name user**-DevopsWorkshop-roles | jq -r '.Stacks[0].Outputs[]|select(.OutputKey=="CodeDeployRoleArn")|.OutputValue')

// 아래 --ec2-tag-filters 의 Name 키로 등록된 값에 배포가 된다. 즉 EC2 인스턴스 태그 Name의 값에 DevWebApp01 이라고 되어 있으며 해당 인스턴스에 배포가 된다.

user:~/environment/WebAppRepo (master) $ aws deploy create-deployment-group --application-name DevOps-WebApp \
--deployment-config-name CodeDeployDefault.OneAtATime \
--deployment-group-name DevOps-WebApp-BetaGroup \
--ec2-tag-filters Key=Name,Value=user**-DevWebApp01,Type=KEY_AND_VALUE \
--service-role-arn <<REPLACE-WITH-YOUR-CODEDEPLOY-ROLE-ARN>>
```

**_Note:_** 태그를 사용하여 인스턴스를 배포 그룹에 연결합니다.

3. 진행 상황을 CodeDeploy 콘솔에서 확인 가능 [CodeDeploy 콘솔 화면](https://console.aws.amazon.com/codedeploy/home).

![deploy](./img/Lab2-CodeDeploy-Success.png)

***

### Stage 3: 배포를 위한 애플리케이션 준비

1. AppSpec 파일이 없으면 AWS CodeDeploy는 애플리케이션 개정판(revision)의 소스 파일을 대상에 매핑하거나 다양한 배포 단계에서 스크립트를 실행할 수 없습니다.

2. Cloud9 에서 아래 내용을 **_WebAppRepo_** 디렉토리에 **_appspec.yml_** 파일을 생성후 붙여 넣습니다. 

```yml
version: 0.0
os: linux
files:
  - source: /target/javawebdemo.war
    destination: /tmp/codedeploy-deployment-staging-area/
  - source: /scripts/configure_http_port.xsl
    destination: /tmp/codedeploy-deployment-staging-area/
hooks:
  ApplicationStop:
    - location: scripts/stop_application
      timeout: 300
  BeforeInstall:
    - location: scripts/install_dependencies
      timeout: 300
  ApplicationStart:
    - location: scripts/write_codedeploy_config.sh
    - location: scripts/start_application
      timeout: 300
  ValidateService:
    - location: scripts/basic_health_check.sh

```

샘플 파일:

![appspec](./img/app-spec.png)

3. 이 스크립트들은 **_appspec.yml_** 파일과 배포 과정에서 함께 실됩니다.

4. CodeDeploy를 통해 응용 프로그램을 배포 할 예정이므로 CodeDeploy에 필요한 추가 파일을 패키지해야합니다.
 **_buildspec.yml_** 파일을 열어서 아래 artifact 부분에 업데이트 합니다.

```yml
version: 0.1

phases:
  install:
    commands:
      - echo Nothing to do in the install phase...
  pre_build:
    commands:
      - echo Nothing to do in the pre_build phase...
  build:
    commands:
      - echo Build started on `date`
      - mvn install
  post_build:
    commands:
      - echo Build completed on `date`
artifacts:
  files:
    - appspec.yml
    - scripts/**/*
    - target/javawebdemo.war

```

5. buildspec.yml 파일을 수정 후 저장합니다.

6. 리포지토리에 추가하고 업로드 합니다.

```console
user:~/environment/WebAppRepo/ $ git add buildspec.yml
user:~/environment/WebAppRepo/ $ git add appspec.yml
user:~/environment/WebAppRepo/ $ git commit -m "changes to build and app spec"
user:~/environment/WebAppRepo/ $ git push -u origin master

```

***

### Stage 4: 애플리케이션 재배포

1. **_start-build_** 명령어를 실행합니다:

```console
user:~/environment/WebAppRepo (master) $ aws codebuild start-build --project-name devops-webapp-project
```

2. 빌드가 성공했는지 확인하려면 CodeBuild 콘솔을 방문하십시오. 빌드가 성공적으로 완료되면 새로 생성된 **_WebAppOutputArtifact.zip_** 파일이 S3 Bucket 에 업로드 된 것을 확인할 수 있습니다.

3. S3 버킷에 업로드된 **WebAppOutputArtifact.zip** 파일의 **_eTag_** 를 확인하세요. etag는 S3 서비스에서 해당 파일을 클릭하면 확인할 수 있습니다. 혹은 아래 명령어로도 확인이 가능합니다.

```console
user:~/environment/WebAppRepo (master) $ echo YOUR-S3-OUTPUT-BUCKET-NAME: $(aws cloudformation describe-stacks --stack-name user**-DevopsWorkshop-roles | jq -r '.Stacks[0].Outputs[]|select(.OutputKey=="S3BucketName")|.OutputValue')
user:~/environment/WebAppRepo (master) $ aws s3api head-object --bucket <<REPLACE-YOUR-S3-OUTPUT-BUCKET-NAME>> \
--key WebAppOutputArtifact.zip

```

etag 확인 샘플:

![etag](./img/etag.png)

4. 아래 명령어를 통해 배포를 실행합니다. <<REPLACE-YOUR-S3-OUTPUT-BUCKET-NAME>> 부분을 Lab1에서 생성한 **_S3 버킷 이름_** 으로 변경합니다.  <<REPLACE-YOUR-ETAG-VALUE>> 부분도 앞에서 생성한 파일의 **_eTag_** 값도 변경합니다.

```console
user:~/environment/WebAppRepo (master) $ aws deploy create-deployment --application-name DevOps-WebApp \
--deployment-group-name DevOps-WebApp-BetaGroup \
--description "My very first deployment" \
--s3-location bucket=<<REPLACE-YOUR-S3-OUTPUT-BUCKET-NAME>>,key=WebAppOutputArtifact.zip,bundleType=zip,eTag=<<REPLACE-YOUR-ETAG-VALUE>>
```

5. 연결된 EC2 인스턴스에 위에서 지정한 버킷에서 읽을 수있는 적절한 IAM 권한이있는 경우 승인이 됩니다.그렇지 않은 경우 배포 중에 DownloadBundle 단계에서 액세스가 거부됩니다.

6. AWS 관리 콘솔에서 **CodeDeploy console* 에서 배포 상태를 확인합니다.

![deployment-success](./img/Lab2-CodeDeploy-deploymentSuccess.png)

7. 배포 콘솔의 상태를 확인하십시오. 배포에 실패한 경우 오류 메시지를보고 배포 문제를 해결하십시오.

8. 배포 상태가 성공하면  **_DevWebApp01_** EC2 서버에 성공적으로 배포 된 웹 응용 프로그램을 볼 수 있어야합니다.

9. 제대로 배포되었는지는 DevWebApp01 인스턴스의 **public DNS name** 을 웹브라우저를 통해 확인해 볼 수 있습니다.

![webpage](./img/webpage-success.png)

### Summary

This **concludes Lab 2**.  이 Lab에서는 CodeDeploy 응용 프로그램 및 배포 그룹을 성공적으로 만들었습니다. 또한 배포에 필요한 추가 구성 요소를 포함하도록 buildspec.yml을 수정했습니다. 또한 테스트 서버에 응용 프로그램을 성공적으로 배포했습니다. 이제 다음 실습으로 이동할 수 있습니다.

[Lab 3 - Setup CI/CD using AWS CodePipeline](3_Lab3.md)
