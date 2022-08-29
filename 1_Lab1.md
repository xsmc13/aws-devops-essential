## Lab 1 - AWS에서 프로젝트 구축

### AWS Cloud9 IDE , 통합 개발 환경 구축

AWS Cloud9는 브라우저만으로 코드를 작성, 실행 및 디버깅 할 수있는 클라우드 기반 통합 개발 환경 (IDE)입니다. 코드 편집기, 디버거 및 터미널이 포함되어 있습니다. Cloud9에는 널리 사용되는 프로그래밍 언어 및 AWS Command Line Interface (CLI)에 대한 필수 도구가 사전 패키지로 제공되므로이 워크샵을 위해 파일을 설치하거나 랩톱을 구성 할 필요가 없습니다. Cloud9 환경은 AWS Management Console에 로그인 한 사용자와 동일한 AWS 리소스에 액세스 할 수 있습니다.

통합 개발 환경은 "Seoul(ap-northeast-2)" or Tokyo(ap-northeast-1)로 선택합니다.

### ✅ Cloud9 생성 순서

1. ASW 관리 콘솔에서, **Services(서비스)** 를 클릭하고 개발도구(Developer Tools) 아래 **Cloud9** 을 선택합니다.
2. **Create environment** 을 선택합니다.
3. Cloud9 생성할 개발 환경이름을 `MyDevEnvironment` 로 선택합니다.
4. **Next step** 을 선택합니다.
5. 인스턴스 크기는 **t2.micro** 로 하고,  **30 분** 간 사용하지 않을 때 인스턴스를 종료하도록 설정합니다.
6. **Next step** 을 선택합니다.
7. 설정을 확인 한 후에 **Create environment** 을 클릭합니다. 선택한 Seoul 리전에 Cloud9용 인스턴스가 생성되는데 1-2분 정도 소요됩니다.
   (생성이 되지 않는 경우, Default VPC의 Internet Gateway나 Routing Table 등 기본적인 네트워크 설정이 제대로 되어 있는지 확인합니다)
8. 준비가되면 IDE가 시작 화면으로 열립니다. 아래에는 다음과 유사한 터미널 프롬프트가 표시됩니다.![setup](./img/setup-cloud9-terminal.png) 로컬 컴퓨터에서와 마찬가지로 여기에서 AWS CLI 명령을 실행할 수 있습니다. 다음 명령을 실행하여 사용자가 로그인했는지 확인하십시오.

```console
user:~/environment $ aws sts get-caller-identity
```

계정 및 사용자 정보가 아래와 같이 출력됩니다.
```console
{
    "Account": "123456789012",
    "UserId": "AKIAI44QH8DHBEXAMPLE",
    "Arn": "arn:aws:iam::123456789012:user/user"
}
```

복제, 리포지토리 변경 및 AWS CLI 사용과 같은 활동에 사용할 수 있도록이 워크샵 전반에 걸쳐 AWS Cloud9 IDE를 탭으로 열어 두십시오..

### 💡 Tips

Cloud9의 열린 스크래치 패드 또는 로컬 컴퓨터의 메모장에 메모를 보관하십시오. 단계별 지침에 따라 ID 또는 ARN (Amazon Resource Name)과 같은 것을 기록하라는 메시지가 표시되면이를 스크래치 패드에 복사하여 붙여 넣습니다..

***

### Stage 1: AWS CodeCommit 리포지토리 생성

**_AWS CodeCommit 리포지토리 생성  (console)_**

1. AWS CodeCommit 콘솔을 열어서 작업을 시작합니다. <https://console.aws.amazon.com/codecommit>.

2. 첫 화면에서 좌측메뉴에서 "Getting Started" 를 선택합니다. (If a **_Dashboard_** page appears instead, choose **_Create repository_**.)
3. **_Create repository_** 페이지에서, **_Repository name_** 박스에,  **_WebAppRepo_** 를 입력합니다.
4. **_Description_** 박스에, **_데모 리포지토리_** 라고 적습니다.
5. **_Create_** 를 클릭하고 **_WebAppRepo_** 리포지토리를 생성합니다.

**_Note_** 이 자습서의 나머지 단계에서는 AWS CodeCommit 리포지토리의 이름을 **_WebAppRepo_** 로 지정했다고 가정합니다. 별도로 리포지토리를 만드는 경우 아래 문서 참조 [리포지토리 만들기](http://docs.aws.amazon.com/codecommit/latest/userguide/how-to-create-repository.html).

***

### Stage 2: 리포지토리 복제하기

이 단계에서는 이전 단계에서 생성 한 소스 리포지토리에 연결합니다. 여기서 Git을 사용하여 빈 AWS CodeCommit 리포지토리의 복사본을 복제하고 초기화합니다. 그런 다음 커밋에 주석을 달 때 사용되는 사용자 이름과 이메일 주소를 지정하십시오.

1. CodeCommit 콘솔에서 생성한 리포지토리 우측의 **Clone Url** 에 HTTPS를 클릭하면 주소가 복사됩니다.
2. Cloud9 IDE 터미널로 이동합니다.
3. 로컬 리포지토리로 복제하기 위해 아래 명령어를 실행합니다:

```console
user:~/environment $ git clone https://git-codecommit.<YOUR-REGION>.amazonaws.com/v1/repos/WebAppRepo

```

메시지가 표시되면 Git HTTP 자격 증명을 제공하십시오. 복제가 성공하면 다음 메시지가 표시됩니다. ***주의:빈 저장소를 복제하였기 때문에 파일은 없습니다.***

***

<AWS CodeCommit Clone 할 때 에러나는 경우>

아래 명령어 실행시 403 에러가 나는 경우 (CodeCommit 리포지토리에 접근 권한이 없기 때문)

git clone https://git-codecommit.<YOUR-REGION>.amazonaws.com/v1/repos/WebAppRepo

아래 명령어 실행

$git config --global credential.helper "aws codecommit credential-helper$@"

위 명령어 실행후  git clone  재실행 -> 실행  후 Username & Password 입력

(AWS CodeCommit 자격 증명은 "IAM->보안자격증명->AWS CodeCommit에 대한 HTTPS Git 자격 증명 에서 생성" 에서 생성)

그래도 안되는 경우 
해당 리포지토리 아래 .git 디렉토리의 config 파일에서 credential 아래 부분 모두 삭제 후 다시 실행

### Stage 3: 리모트 리포지토리 

1. IDE 터미널에서 다음 명령을 실행하여 샘플 소스코드를 다운로드하십시오..

```console
user:~/environment $ wget https://github.com/lormadus/aws-devops-essential/raw/master/sample-app/Web-App-Archive.zip
```

2. 압축파일을 해제하고 Web-App-Archive 폴더 아래 파일을 모두 WebAppRepo로 이동시킵니다.

```console
user:~/environment $ unzip Web-App-Archive.zip
user:~/environment $ mv -v Web-App-Archive/* WebAppRepo/
```

![cloud9](./img/Cloud9-IDE-Screen-Sample.png)
3. WebAppRepo 디렉토리로 이동한 후 **_git add_** 을 통해 모든 파일을 리포지토리에 추가합니다.

```console
user:~/environment $ cd WebAppRepo
user:~/environment/WebAppRepo/ $ git add *
```

4. **_git commit_** 명령을 실행합니다.

```console
user:~/environment/WebAppRepo/ $ git commit -m "Initial Commit"
```

**_💡 Tip_** 커밋 과정을 자세히 보려면 *_git log_** 명령어를 사용합니다.

5. **_git config credential_** 를 통해 자격증명을 저장합니다.

```console
user:~/environment/WebAppRepo/ $ git config credential.helper store
```

6. **_git push_** 를 통해 로컬 소스 파일을 AWS CodeCommit 리포지토리에 업로드 합니다 (origin), 
```console
user:~/environment/WebAppRepo/ $ git push -u origin master
```

Provide your Git HTTPs credential when prompted. Credential helper will store it, hence you won't be asked again for subsequent push.

**_💡 Tip_** Push한 후에  [AWS CodeCommit console](https://console.aws.amazon.com/codecommit/home) 에서 
정상적으로 파일들이 업로드 되었는지 확인한다.

![buildsuccess](./img/Lab1-CodeCommit-Success.png)

더 자세한 설명이 필요하면 아래자료를 참고합니다. [Browse the Contents of a Repository](http://docs.aws.amazon.com/codecommit/latest/userguide/how-to-browse.html).

***

### Stage 4: 빌드 서비스 준비하기

1. 먼저 실습을 마치는 데 필요한 역할을 만듭니다. CloudFormation 스택을 실행하여 서비스 역할을 만듭니다.
   AWS CodeCommit 리포지토리와 동일한 리전에서 시작해야합니다.
   
   CloudFormation 스택 생성 전에 스택 이름을 아래와 같이 user** 부분을 추가해 줍니다.  그리고 본인 Repository의 01-aws-devops-workshop-roles.template 파일의
   S3 Bucket 이름 앞에서 user**을 추가해 줍니다.  
   
```   
    "S3Bucket": {
      "Type": "AWS::S3::Bucket",
      "DeletionPolicy":"Retain",
      "Properties": {
        "BucketName" : { "Fn::Join" : ["", [ "user30-cicd-workshop-", { "Ref": "AWS::Region" } , "-", {"Ref" : "AWS::AccountId"} ]] },
```
    01-aws-devops-workshop-roles.template  수정후에 아래와 같이 실행합니다.
```console
user:~/environment/WebAppRepo (master) $ aws cloudformation create-stack --stack-name user**-DevopsWorkshop-roles \
--template-body https://raw.githubusercontent.com/2mileslab/aws-devops-essential/master/templates/01-aws-devops-workshop-roles.template \
--capabilities CAPABILITY_IAM
```

**_Tip_** AWS CloudFormation에 대해 더 알기 원하면 아래 자료 참고합니다. [AWS CloudFormation 사용자 가이드.](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)

2. 완료되면 생성 된 서비스 역할에 대해 기록해 둡니다.. 스텍결과에 대해서 확인하려면 아래 참고합니다 [스택 정보확인](http://docs.aws.amazon.com/cli/latest/reference/cloudformation/describe-stacks.html) .

3. CloudFormation 결과에 대해서는 아래파일 참조 [Outputs tab](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-view-stack-data-resources.html). CloudFormation통해서 S3 버킷이 하나 생성되고 여기에 CodeBuild 를 통한 결과 아티펙트 파일들이 저장된다. . **_Sample Output:_** ![](./img/cfn-output.png)

4. 다음 명령을 실행하여 앞서 실행 한 cloudformation 템플릿에서 Build Role ARN 및 S3 버킷의 값을 가져옵니다.

```console
user:~/environment/WebAppRepo (master) $ sudo yum -y install jq
user:~/environment/WebAppRepo (master) $ echo YOUR-BuildRole-ARN: $(aws cloudformation describe-stacks --stack-name user**-DevopsWorkshop-roles | jq -r '.Stacks[0].Outputs[]|select(.OutputKey=="CodeBuildRoleArn")|.OutputValue')
user:~/environment/WebAppRepo (master) $ echo YOUR-S3-OUTPUT-BUCKET-NAME: $(aws cloudformation describe-stacks --stack-name user**-DevopsWorkshop-roles | jq -r '.Stacks[0].Outputs[]|select(.OutputKey=="S3BucketName")|.OutputValue')
```

5. CLI에서 **create CodeBuild** 프로젝트를 생성합니다.  AWS CLI를 사용하여 빌드 프로젝트를 생성하려면 JSON 형식의 입력이 필요합니다.
 JSON 파일 **_'create-project.json'_** 을 '~/Environment' 폴더에 생성합니다. ![](./img/create-json.png) 아래내용을 create-project.json 파일에 복사해서 넣습니다. ( **_<<>>_** 로 표기된 부분은 본인이 작업 리전, S3버킷 이름, BUILD ARN 등 생성된 값으로 넣어줍니다) 
    

```json
{
  "name": "devops-webapp-project",
  "source": {
    "type": "CODECOMMIT",
    "location": "https://git-codecommit.<<REPLACE-YOUR-REGION-ID>>.amazonaws.com/v1/repos/WebAppRepo"
  },
  "artifacts": {
    "type": "S3",
    "location": "<<REPLACE-YOUR-S3-OUTPUT-BUCKET-NAME>>",
    "packaging": "ZIP",
    "name": "WebAppOutputArtifact.zip"
  },
  "environment": {
    "type": "LINUX_CONTAINER",
    "image": "aws/codebuild/java:openjdk-8",
    "computeType": "BUILD_GENERAL1_SMALL"
  },
  "serviceRole": "<<REPLACE-YOUR-BuildRole-ARN>>"
}
```
    
codebuild 프로젝트 생성을 위한 json 파일에 대해 자세히 알아보려면 아래 파일을 참조합니다. [spec파일 알아보기](http://docs.aws.amazon.com/codebuild/latest/userguide/create-project.html#create-project-cli).


6. Cloud9 터미널에서 방금 생성한 파일이 있는 경로로 이동해서 아래 명령어를 실행합니다.

```console
user:~/environment $ aws codebuild create-project --cli-input-json file://create-project.json
```

7. 결과는 아래와 같이 출력이 됩니다.

```json
{
  "project": {
    "name": "project-name",
    "description": "description",
    "serviceRole": "serviceRole",
    "tags": [
      {
        "key": "tags-key",
        "value": "tags-value"
      }
    ],
    "artifacts": {
      "namespaceType": "namespaceType",
      "packaging": "packaging",
      "path": "path",
      "type": "artifacts-type",
      "location": "artifacts-location",
      "name": "artifacts-name"
    },
    "lastModified": lastModified,
    "timeoutInMinutes": timeoutInMinutes,
    "created": created,
    "environment": {
      "computeType": "computeType",
      "image": "image",
      "type": "environment-type",
      "environmentVariables": [
        {
          "name": "environmentVariable-name",
          "value": "environmentVariable-value",
          "type": "environmentVariable-type"
        }
      ]
    },
    "source": {
      "type": "source-type",
      "location": "source-location",
      "buildspec": "buildspec",
      "auth": {
        "type": "auth-type",
        "resource": "resource"
      }
    },
    "encryptionKey": "encryptionKey",
    "arn": "arn"
  }
}
```

8. 프로젝트가제대로생성되었다면, 결과로 출력된 JSON 파일에서 아래 내용을 확인할 수 있습니다:
  * lastModified 값은 빌드 프로젝트에 대한 정보가 마지막으로 변경된 시간을 Unix 시간 형식으로 나타냅니다.
  * 작성된 값은 빌드 프로젝트가 작성된 시간을 Unix 시간 형식으로 나타냅니다.
  * ARN 값은 빌드 프로젝트의 ARN을 나타냅니다.

**_Note_** 빌드 프로젝트 이름을 제외하고 나중에 빌드 프로젝트의 설정을 변경할 수 있습니다. 자세한 내용은 아래 내용 참고하세요 [Change a Build Project's Settings (AWS CLI)](http://docs.aws.amazon.com/codebuild/latest/userguide/change-project.html#change-project-cli).

***

### Stage 5: 클라우드에서 코드 빌드하기

1. 빌드 사양(Build Spec)은 AWS CodeBuild가 빌드를 실행하는 데 사용하는 빌드 명령 및 YAML 형식의 관련 설정 모음입니다.
    **_buildspec.yml_** 파일을 **WebAppRepo** 폴더 아래에 생성합니다. 아래 내용을 복사해서 붙여넣고 저장합니다. CodeBilde에 대해서 더 알기원하면 아래 파일 참고합니다 [CodeBuild 동작원리](http://docs.aws.amazon.com/codebuild/latest/userguide/concepts.html#concepts-how-it-works).

```yaml
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
    - target/javawebdemo.war
  discard-paths: no
```

결과 샘플:

![buildspec](./img/build-spec.png)

**_Note_** 하나의 리포지토리에서 복수의 Build Spec 파일을 관리하기 원하면 아래파일을 참조합니다. [page](http://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html) 

2. 리포지토리에 buildspec파일 업로드
```console
user:~/environment/WebAppRepo/ $ git add buildspec.yml
user:~/environment/WebAppRepo/ $ git commit -m "adding buildspec.yml"
user:~/environment/WebAppRepo/ $ git push -u origin master

```

3. **_start-build_** 명령어 실행:

```console
user:~/environment/WebAppRepo (master) $ aws codebuild start-build --project-name devops-webapp-project
```

**_Note:_** JSON을 통해 더 고급 구성 설정으로 빌드를 시작할 수 있습니다. 그것에 대해 더 자세히 알고 싶다면 다음 사이트를 방문하십시오. [here](http://docs.aws.amazon.com/codebuild/latest/userguide/run-build.html#run-build-cli).

4. 성공하면 데이터가 성공적으로 출력됩니다. 빌드 ID 값을 기록하십시오. 다음 단계에서 필요합니다.
5. 이 단계에서는 빌드 상태에 대한 요약 된 정보를 볼 수 있습니다.
**_Note:_**  <<ID>> 부분을 출력으로 나온 Build ID값으로 변경하세요.
   
```console
user:~/environment/WebAppRepo (master) $ aws codebuild batch-get-builds --ids <<ID>>
```

6. CloudWatch Logs에서 빌드에 대한 자세한 정보를 볼 수도 있습니다. 이 단계를 완료하려면 다음 문서를 확인하세요 [AWS CodeBuild console](https://console.aws.amazon.com/codebuild/home).
![buildsuccess](./img/Lab1-CodeBuild-Success.png)

7.이번 과정에서는 빌드 결과로 생성된 **_WebAppOutputArtifact.zip_** 파일을 S3 버킷에 생성됨을 확인할 수 있습니다.

**_Note:_** 빌드과정에서의 문제해결을 위해서는 다음 문서를 참조하세요 [빌드 문제해결](http://docs.aws.amazon.com/codebuild/latest/userguide/troubleshooting.html) 

### 결론:

**Lab 1**. 이번 Lab에서는 AWS CodeCommit을 사용하여 버전 제어가 포함된 리포지토리를 성공적으로 생성하고 AWS CodeBuild 서비스를 사용하여 클라우드에서 코드를 빌드하였습니다. 이제 다음 실습으로 넘어가도록 하겠습니다.

[Lab 2 - Automate deployment for testing](2_Lab2.md)
