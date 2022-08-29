
## Lab 3 - Setup CI/CD using AWS CodePipeline

### Stage 1: Create a Pipeline (Console)

콘솔에서 파이프 라인을 만들려면 소스 파일 위치와 작업에 사용할 공급자에 대한 정보를 제공해야합니다.

파이프 라인 마법사를 사용하면 AWS CodePipeline이 스테이지 이름 (소스, 빌드, 스테이징)을 생성합니다. 이 이름은 변경할 수 없습니다. 그러나 이름을 변경하려면 빌드 및 스테이징을 삭제할 수 있습니다. 삭제하고 추가하는 경우 보다 구체적인 이름 (예 : BuildToGamma 또는 DeployToProd)을 지정할 수 있습니다.

또한 기존 파이프 라인 구성을 내보내 다른 지역에서 파이프 라인을 만드는 데 사용할 수 있습니다.

1. **AWS Management Console** 에 로그인 후 **AWS CodePipeline** 콘솔을 엽니다 [http://console.aws.amazon.com/codepipeline](http://console.aws.amazon.com/codepipeline).

2. **CodePipeline Home** 페이지에서 **Create pipeline** 을 클릭합니다.

3. **Step 1: Choose pipeline settings** 페이지의 **Pipeline name** 박스에서 이름을 **user\**-WebAppPipeline** 과 같이 입력합니다.

4. **Service role** 에서 **Existing service role** 선택 후 Role이름을 선택창에서 **user\**-DevopsWorkshop** 를 선택합니다.

5. **Artifact store** 에서 **Custom location** 을 선택합니다. S3 버킷을 선택창에서 **user\**-cicd-workshop** 를 선택 후 **Next step** 을 누릅니다.

**_Note_**
단일 AWS 계정 내에서 리전에서 생성한 각 파이프 라인의 이름은 고유해야합니다. 다른 지역의 파이프 라인에 이름을 재사용 할 수 있습니다.

파이프 라인을 생성 한 후에는 파이프 라인 이름을 변경할 수 없습니다. 다른 제한 사항에 대한 정보는 다음 페이지에서 확인하세요 [Limits in AWS CodePipeline](https://docs.aws.amazon.com/codepipeline/latest/userguide/limits.html).

6. **Step 2: Source** 페이지에서 **Source provider** 에서, 소스 코드가 저장된 저장소 유형을 선택하고 필요한 옵션을 지정하십시오:
  - **AWS CodeCommit**: **Repository name** 에서는 AWS CodeCommit 이름은 Lab1 에서 생성한 리포지토리를 선택합니다. 
**Branch name** 에서는 **master** 브랜치를 선택합니다.
- **Change Detection Mode** 에서는 기본값이 Amazon CloudWatch Events selection 을 선택합니다. **Next step**을 클릭합니다.

7. **Step 3: Build** 페이지에서는, 아래대로 따라 하십시오.
  - **AWS CodeBuild** 를 선택 후, **existing build project** Lab1에서 생성한 프로젝트를 선택하세요.
  - **Next step** 를 선택하세요.

8. **Step 4: Deploy** 페이지에서, 아래대로 설정하시고 Next step을 클릭하세요:
  - 배포 제공자(Deployment Provider)는 드롭 다운 목록에서 다음 기본 제공자를 선택하십시오.:
    + **AWS CodeDeploy:** AWS CodeDeploy application 에서 생성한 Application 이름과 Lab2에서 생성한 Deployment group 을 선택 후 **Next step** 을 클릭하세요.

9. **Step 5: Review** 페이지에서, 파이프라인 설정을 확인 후 **Create pipeline** 을 눌러 파이프라인을 생성하세요.

아래 이미지는 성공적으로 생성 된 파이프 라인을 보여줍니다.
![pipeline-complete](./img/Lab3-Stage1-Complete.PNG)

10. 파이프 라인을 생성 했으므로 콘솔에서 파이프 라인을 볼 수 있습니다. 몇 분 후에 파이프 라인이 자동으로 시작됩니다. 그렇지 않으면 수동으로 클릭하여 테스트하십시오. **Release** 버튼을 누르시면 됩니다.

아래 이미지는 성공적으로 실행 된 파이프 라인을 보여줍니다.
![pipeline-released](./img/Lab3-Stage1-Complete-released.PNG)

***

### Stage 2: Create CodeDeploy Deployment group for Production

1. 다음을 실행하여 배포 그룹을 생성하고 이를 지정된 애플리케이션 및 사용자의 AWS 계정과 연결합니다. CodeDeploy 의 ARN을 교체해야합니다.

```console
user:~/environment/WebAppRepo (master) $ echo YOUR-CODEDEPLOY-ROLE-ARN: $(aws cloudformation describe-stacks --stack-name DevopsWorkshop-roles | jq -r '.Stacks[0].Outputs[]|select(.OutputKey=="CodeDeployRoleArn")|.OutputValue')

user:~/environment $ aws deploy create-deployment-group --application-name user30-DevOps-WebApp  \
--deployment-config-name CodeDeployDefault.OneAtATime \
--deployment-group-name DevOps-WebApp-ProdGroup \
--ec2-tag-filters Key=Name,Value=user30-ProdWebApp01,Type=KEY_AND_VALUE \
--service-role-arn <<REPLACE-WITH-YOUR-CODEDEPLOY-ROLE-ARN>>
```

**_Note:_** 다른 그룹 이름과 프로덕션 태그를 사용하여 인스턴스를 배포 그룹에 연결합니다..

***

### Stage 3: AWS 콘솔에서 파이프라인 수정

AWS CodePipeline 콘솔을 사용하여 파이프 라인에서 단계를 추가, 편집 또는 제거 할 수있을뿐만 아니라 단계에서 작업을 추가, 편집 또는 제거 할 수 있습니다.

수동으로 파이프 라인을 편집하여 배포 단계를 추가하는 방법을 소개합니다.

1. 파이프 라인 세부 정보 페이지에서 **Edit** 을 선택합니다. 파이프 라인에 대한 편집 페이지가 열립니다.
2. 스테이지를 추가하기 위해 **Deploy** 다음에 **+ Add stage** 를 선택합니다.
3. 스테이지 이름에 **Production** 을 입력하고, 그런 다음 하나의 작업을 추가하십시오. 별표가 표시된 항목이 필요합니다.
4. **+ Add action group** 을선택하고 **Edit Action** 섹센에서 이름을 **ProductionDeployment** 적고, **AWS CodeDeploy** 을 선택합니다.
5. **AWS CodeDeploy:** 에서 애플리케이션 이름에 기존 AWS CodeDeploy 애플리케이션의 이름을 입력하거나 선택하십시오. **production deployment group** 에 앞서 작성한 이름을 넣는다.
7. **Input artifacts**: 에서 **BuildArtifact** 를 선택한다.
8. **Save**. 통해서 저장한다.
9. 마지막으로 위에 **Save** 버튼을 눌러 변경사항을 저장한다.

![pipeline-edit](./img/Lab3-Stage3-Editing2.PNG)
***

### Stage 4: 수동 승인 과정 추가

AWS CodePipeline에서 필요한 AWS Identity and Access Management 권한이있는 사람이 작업을 승인 또는 거부 할 수 있도록 파이프 라인 실행을 중지하려는 지점에서 파이프 라인의 단계에 승인 작업을 추가 할 수 있습니다.

작업이 승인되면 파이프 라인 실행이 다시 시작됩니다. 파이프 라인이 조치에 도달하여 중지 한 후 7 일 이내에 조치를 승인하거나 거부하지 않는 경우 조치가 실패한 결과와 동일하며 파이프 라인 실행이 계속되지 않습니다.

1. **Create SNS topic** for Approval notification. And note the **topic ARN** from the result.

```console
user:~/environment $ aws sns create-topic --name WebApp-Approval-Topic --region <YOUR-REGION>
```

2. **Subscribe** to the topic using your email id. **Replace** the **ARN** and **email id** placeholders accordingly.

```console
user:~/environment $ aws sns subscribe --topic-arn <<REPLACE-YOUR-TOPIC-ARN>> \
--protocol email \
--notification-endpoint <<REPLACE-YOUR-EMAIL-ID>>
```

3. An Email would be sent for **confirmation** on the subscription. **Acknowledge** the subscription to receive mails from topic.

![pipeline-edit](./img/Lab4-Stage4-Step3-Confirm-MustDoOrErrorOccurs.PNG)

4. On the pipeline details page, choose **Edit**. This opens the editing page for the pipeline. Choose **+ Add stage** at the point in the pipeline **between Deploy** and **Production** stage, and type a name **Approval** for the stage.
5. Choose the **+ Add action group**.
6. On the **Edit action** page, do the following:
7. In **Action name**, type a name to identify the action like **EmailApproval**.
8. In **Action provider**, choose **Manual approval**.
9. In **SNS topic ARN**, choose the name of the topic created to send notifications for the approval action.
10. (Optional) In **Comments**, type any additional information you want to share with the reviewer.
11. Choose **Save**.
12. Save changes to pipeline by clicking **Save** button on top.
13. To test your action, choose **Release change** to process that commit through the pipeline, commit a change to the source specified in the source stage of the pipeline.

***

### Stage 5: Approve or Reject an Approval Action in AWS CodePipeline

If you receive a notification that includes a direct link to an approval action, choose the **Approve or reject** link, sign in to the console if necessary, and then continue with step 7 below. Otherwise, use all the following steps.

1. Open the **AWS CodePipeline console** at https://console.aws.amazon.com/codepipeline/.
2. On the **All Pipelines** page, choose the name of the pipeline.
3. _Locate_ the stage with the approval action.
4. Hover over the information icon to view the comments and URL, if any. The information pop-up message will also display the URL of content for you to review, if one was included.
5. If a URL was provided, choose the **Manual approval** link in the action to open the target Web page, and then review the content.
6. Return to the pipeline details view, and then choose the **Review** button.
7. In the **Approve or reject** the revision window, type comments related to your review, such as why you are approving or rejecting the action, and then choose the **Approve** or **Reject** button.

![pipeline-edit](./img/Lab4-Stage5-ApprovalPipeline.PNG)

Once you approve, the pipeline continues and completes successfully.
![pipeline-edit](./img/Lab4-CompletePipeline.png)

### Summary

This **concludes Lab 3**. In this lab, we successfully created CodePipeline for continuous code build and deployment. We also modified CodePipeline to include manual approval action before deploying code to production environment. We also successfully completed continuous deployment of application to both test and production servers. You can now move to the next Lab,

[Lab 4 (Optional) - Using Lambda as Test Stage in CodePipeline](4_Lab4.md)
