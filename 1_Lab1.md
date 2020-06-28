## Lab 1 - AWS에서 프로젝트 구축

### AWS Cloud9 IDE , 통합 개발 환경 구축

AWS Cloud9는 브라우저만으로 코드를 작성, 실행 및 디버깅 할 수있는 클라우드 기반 통합 개발 환경 (IDE)입니다. 코드 편집기, 디버거 및 터미널이 포함되어 있습니다. Cloud9에는 널리 사용되는 프로그래밍 언어 및 AWS Command Line Interface (CLI)에 대한 필수 도구가 사전 패키지로 제공되므로이 워크샵을 위해 파일을 설치하거나 랩톱을 구성 할 필요가 없습니다. Cloud9 환경은 AWS Management Console에 로그인 한 사용자와 동일한 AWS 리소스에 액세스 할 수 있습니다.

통합 개발 환경은 "Seoul(ap-northeast-2)`로 선택합니다.

### ✅ Cloud9 생성 순서

1. ASW 관리 콘솔에서, **Services(서비스)** 를 클릭하고 개발도구(Developer Tools) 아래 **Cloud9** 을 선택합니다.
2. **Create environment** 을 선택합니다.
3. Cloud9 생성할 개발 환경이름을 `user**-MyDevEnvironment` 로 선택합니다.
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
3. **_Create repository_** 페이지에서, **_Repository name_** 박스에,  **_user@@-WebAppRepo_** 를 입력합니다.
4. **_Description_** 박스에, **_User@@ demonstration repository_**라고 적습니다.
5. **_Create_** 를 클릭하고 **_user@@-WebAppRepo_** 리포지토리를 생성합니다.

**_Note_** The remaining steps in this tutorial assume you have named your AWS CodeCommit repository **_WebAppRepo_**. If you use a name other than **_WebAppRepo_**, be sure to use it throughout this tutorial. For more information about creating repositories, including how to create a repository from the terminal or command line, see [Create a Repository](http://docs.aws.amazon.com/codecommit/latest/userguide/how-to-create-repository.html).

***

### Stage 2: Clone the Repo

In this step, you will connect to the source repository created in the previous step. Here, you use Git to clone and initialize a copy of your empty AWS CodeCommit repository. Then you specify the user name and email address used to annotate your commits.

1. From CodeCommit Console, you can get the **https clone url** link for your repo.
2. Go to Cloud9 IDE terminal prompt
3. Run git clone to pull down a copy of the repository into the local repo:

```console
user:~/environment $ git clone https://git-codecommit.<YOUR-REGION>.amazonaws.com/v1/repos/WebAppRepo

```

Provide your Git HTTPs credential when prompted. You would be seeing the following message if cloning is successful. ***warning: You appear to have cloned an empty repository.***

***

### Stage 3: Commit changes to Remote Repo

1. Download the Sample Web App Archive by running the following command from IDE terminal.

```console
user:~/environment $ wget https://s3.amazonaws.com/devops-workshop-0526-2051/v1/Web-App-Archive.zip
```

2. Unarchive and copy all the **_contents_** of the unarchived folder to your local repo folder.

```console
user:~/environment $ unzip Web-App-Archive.zip
user:~/environment $ mv -v Web-App-Archive/* WebAppRepo/
```

After moving the files, your local repo should like the one below. ![cloud9](./img/Cloud9-IDE-Screen-Sample.png)
3. Change the directory to your local repo folder. Run **_git add_** to stage the change:

```console
user:~/environment $ cd WebAppRepo
user:~/environment/WebAppRepo/ $ git add *
```

4. Run **_git commit_** to commit the change:

```console
user:~/environment/WebAppRepo/ $ git commit -m "Initial Commit"
```

**_💡 Tip_** To see details about the commit you just made, run **_git log_**.

5. Run **_git config credential_** to store the credential.

```console
user:~/environment/WebAppRepo/ $ git config credential.helper store
```

6. Run **_git push_** to push your commit through the default remote name Git uses for your AWS CodeCommit repository (origin), from the default branch in your local repo (master):

```console
user:~/environment/WebAppRepo/ $ git push -u origin master
```

Provide your Git HTTPs credential when prompted. Credential helper will store it, hence you won't be asked again for subsequent push.

**_💡 Tip_** After you have pushed files to your AWS CodeCommit repository, you can use the [AWS CodeCommit console](https://console.aws.amazon.com/codecommit/home) to view the contents.

![buildsuccess](./img/Lab1-CodeCommit-Success.png)

For more information, see [Browse the Contents of a Repository](http://docs.aws.amazon.com/codecommit/latest/userguide/how-to-browse.html).

***

### Stage 4: Prepare Build Service

1. First, let us create the necessary roles required to finish labs. Run the CloudFormation stack to create service roles.
  Ensure you are launching it in the same region as your AWS CodeCommit repo.

```console
user:~/environment/WebAppRepo (master) $ aws cloudformation create-stack --stack-name DevopsWorkshop-roles \
--template-body https://s3.amazonaws.com/devops-workshop-0526-2051/v1/01-aws-devops-workshop-roles.template \
--capabilities CAPABILITY_IAM
```

**_Tip_** To learn more about AWS CloudFormation, please refer to [AWS CloudFormation UserGuide.](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)

2. Upon completion take a note on the service roles created. Check [describe-stacks](http://docs.aws.amazon.com/cli/latest/reference/cloudformation/describe-stacks.html) to find the output of the stack.

3. For Console, refer to the CloudFormation [Outputs tab](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-view-stack-data-resources.html) to see output. A S3 Bucket is also created. Make a note of this bucket. This will be used to store the output from CodeBuild in the next step. **_Sample Output:_** ![](./img/cfn-output.png)

4. Run the following commands to get the value of Build Role ARN and S3 bucket from cloudformation template launched earlier.

```console
user:~/environment/WebAppRepo (master) $ sudo yum -y install jq
user:~/environment/WebAppRepo (master) $ echo YOUR-BuildRole-ARN: $(aws cloudformation describe-stacks --stack-name DevopsWorkshop-roles | jq -r '.Stacks[0].Outputs[]|select(.OutputKey=="CodeBuildRoleArn")|.OutputValue')
user:~/environment/WebAppRepo (master) $ echo YOUR-S3-OUTPUT-BUCKET-NAME: $(aws cloudformation describe-stacks --stack-name DevopsWorkshop-roles | jq -r '.Stacks[0].Outputs[]|select(.OutputKey=="S3BucketName")|.OutputValue')
```

5. Let us **create CodeBuild** project from **CLI**. To create the build project using AWS CLI, we need JSON-formatted input.
    **_Create_** a json file named **_'create-project.json'_** under 'MyDevEnvironment'. ![](./img/create-json.png) Copy the content below to create-project.json. (Replace the placeholders marked with **_<<>>_** with  values for BuildRole ARN, S3 Output Bucket and region from the previous step.) 
    

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
    
  To know more about the codebuild project json [review the spec](http://docs.aws.amazon.com/codebuild/latest/userguide/create-project.html#create-project-cli).


6. Switch to the directory that contains the file you just saved, and run the **_create-project_** command:

```console
user:~/environment $ aws codebuild create-project --cli-input-json file://create-project.json
```

7. Sample output JSON for your reference

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

8. If successful, output JSON should have values such as:
  * The lastModified value represents the time, in Unix time format, when information about the build project was last changed.
  * The created value represents the time, in Unix time format, when the build project was created.
  * The ARN value represents the ARN of the build project.

**_Note_** Except for the build project name, you can change any of the build project's settings later. For more information, see [Change a Build Project's Settings (AWS CLI)](http://docs.aws.amazon.com/codebuild/latest/userguide/change-project.html#change-project-cli).

***

### Stage 5: Let's build the code on cloud

1. A build spec is a collection of build commands and related settings in YAML format, that AWS CodeBuild uses to run a build.
    Create a file namely, **_buildspec.yml_** under **WebAppRepo** folder. Copy the content below to the file and **save** it. To know more about [how CodeBuild works](http://docs.aws.amazon.com/codebuild/latest/userguide/concepts.html#concepts-how-it-works).

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

As a sample shown below:

![buildspec](./img/build-spec.png)

**_Note_** Visit this [page](http://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html) to know more about build spec and how you can use multiple build specs in the same repo.

2. Commit & push the build specification file to repository
```console
user:~/environment/WebAppRepo/ $ git add buildspec.yml
user:~/environment/WebAppRepo/ $ git commit -m "adding buildspec.yml"
user:~/environment/WebAppRepo/ $ git push -u origin master

```

3. Run the **_start-build_** command:

```console
user:~/environment/WebAppRepo (master) $ aws codebuild start-build --project-name devops-webapp-project
```

**_Note:_** You can start build with more advance configuration setting via JSON. If you are interested to learn more about it, please visit [here](http://docs.aws.amazon.com/codebuild/latest/userguide/run-build.html#run-build-cli).

4. If successful, data would appear showing successful submission. Make a note of the build id value. You will need it in the next step.
5. In this step, you will view summarized information about the status of your build.

```console
user:~/environment/WebAppRepo (master) $ aws codebuild batch-get-builds --ids <<ID>>
```

**_Note:_** Replace <<ID>> with the id value that appeared in the output of the previous step.

6. You will also be able to view detailed information about your build in CloudWatch Logs. You can complete this step by visiting the [AWS CodeBuild console](https://console.aws.amazon.com/codebuild/home).
![buildsuccess](./img/Lab1-CodeBuild-Success.png)

7. In this step, you will verify the **_WebAppOutputArtifact.zip_** file that AWS CodeBuild built and then uploaded to the output bucket. You can complete this step by **visiting** the **AWS CodeBuild console** or the **Amazon S3 console**.

**_Note:_** Troubleshooting CodeBuild - Use the [information](http://docs.aws.amazon.com/codebuild/latest/userguide/troubleshooting.html) to help you identify, diagnose, and address issues.

### Summary:

This **concludes Lab 1**. In this lab, we successfully created repository with version control using AWS CodeCommit and built our code on the cloud using AWS CodeBuild service. You can now move to the next Lab,

[Lab 2 - Automate deployment for testing](2_Lab2.md)
