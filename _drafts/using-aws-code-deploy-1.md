---
layout: post
comments: true
title: AWS CodeDeploy 사용해보기 
header: AWS CodeDeploy 사용해보기 [1] 권한 설정하기
permalink: 
---

3개의 인스턴스를 갖는 Deployment Group 에 아주 간단한 node.js 코드를 디플로이한다


전재조건
- EC2 Key Pair를 생성해두었다. (EC2 인스턴스에 SSH 로그인하기 위하여 필요함)
- Security Group
- CodeDeployRolePolicies: IAM Policy
- **CodeDeployTrustRole: IAM Role ? **
- InstanceRole : IAM Role
- **InstanceRoleInstanceProfile : IAM InstanceProfile**
- **InstanceRolePolicies : IAM Policy**
- CodeDeploy와 EC2 인스턴스는 같은 리전에 있어야 한다. Seoul 리전에 설치한다

첫번째 권한 설정 관련하여 다음 3가지를 해본다.
- 1.1 CodeDeploy를 위한 Service Role 만들기
- 1.2 IAM Instance Profile을 위한 Permission Policy 만들기
- 1.3 EC2를 위한 IAM Instance Profile 만들기

### 1.1 CodeDeploy를 위한 Service Role 만들기

---------
이 Service Role은 CodeDeploy가 EC2 인스턴스들이나 Auto Scaling Group과 같은 서비스를 접근할 수 있게 해준다.

**IAM**의 **Role** 메뉴를 선택하고 `Create Role` 버튼을 클릭한다.
<img src="img/201704/0425_create_service_role_1.png" width="400px">

**AWS Service Role** 섹션에서 스크롤다운하여 **AWS CodeDeploy**를 찾고, `Select` 버튼을 클릭한다.
<img src="img/201704/0425_create_service_role_2.jpg" width="860px">

**AWSCodeDeployRole**을 선택하고 다음 단계로 넘어간다.
<img src="img/201704/0425_create_service_role_3.jpg" width="900px">

Service Role 이름을 정하고 마친다. 아래 예에서는 **"MyCodeDeployRole"**이라고 하였다. 내가 의도적으로 만든 것임을 표시하기 위하여 "My" 접두어를 붙였다.
<img src="img/201704/0425_create_service_role_4.png" width="900px">

생성된 Role을 확인할 수 있다.
<img src="img/201704/0425_create_service_role_5.png" width="740px">

Role Name 부분을 클릭하면 다음과 같은 상세 정보를 볼 수 있다. 이 중에서 **Role ARN**은 따로 기록해두도록 한다. 뒤 과정에서 필요하다. 물론 나중에 다시 IAM의 Role 메뉴로 와서 Role ARN을 확인해도 된다.
<img src="img/201704/0425_create_service_role_6.png" width="880px">


### 1.2 IAM Instance Profile을 위한 Permission Policy 만들기
---------
EC2 인스턴스는 S3 버킷이나 GitHub 저장소에 접근할 수 있는 권한이 있어야 한다. AWS CodeDeploy와 호환되는 EC2 인스턴스를 실행시키려면, Instance profile 이라는 IAM role 을 생성해야 한다. 이 Role은 CodeDeploy가 S3나 GitHub에 있는 애플리케이션에 접근할 수 있도록 한다. 

먼저 Permission policy를 생성한다. 다음 단계에서 Instance Profile Role을 새로 생성하는 과정에 이 Permission policy를 추가할 것이다.

**IAM**의 **Policies** 메뉴로 이동하고 `Create Policy` 버튼을 클릭한다 
<img src="./img/201704/0425_create_instance_profile_1.png" width="600px">

가장 하단의 **Create Your Own Policy**를 선택한다
<img src="./img/201704/0425_create_instance_profile_2.png" width="840px">

**Permission Policy Name**에 `MyCodeDeploy-EC2-Permissions`을 입력한다. 
<img src="./img/201704/0425_create_instance_profile_3.png" width="480px">

다음 내용을 **Policy Document** 란에 붙여 넣는다. 모든 지역(region)에 있는 S3에 접근하여 Get과 List 관련 액션을 할 수 있는 권한이다.
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "s3:Get*",
        "s3:List*"
      ],
      "Effect": "Allow",
      "Resource": "*"
    }
  ]
}
```

화면 하단의 `Validate Policy` 버튼을 클릭해본다. 아래 그림처럼 상단에 "This Policy is valid"라고 나오면 정상이다. `Create Policy` 버튼을 누른다.
<img src="./img/201704/0425_create_instance_profile_4.png" width="700px">

Instance Profile을 성공적으로 생성하였다.
<img src="./img/201704/0425_create_instance_profile_5.png" width="630px">


### 1.3 EC2를 위한 IAM Instance Profile 만들기
---------
EC2 인스턴스들이 AWS 서비스들을 접근할 수 있도록 하는 IAM Instance Profile Role을 만든다.

`Create new role` 버튼을 누른다. 다음 화면에서 **Amazon EC2**를 선택한다.
<img src="./img/201704/0425_create_instance_profile_6.png" width="860px">

매우 많은 policy들이 보이는데, 검색란에 "my"를 입력하여 조금 전 내가 만든 `MyCodeDeploy-EC2-Permissions`를 찾고 선택한 후 다음 단계로 이동한다.
<img src="./img/201704/0425_create_instance_profile_7.png" width="920px">

Role 이름을 **MyCodeDeploy-EC2-Instance-Profile**로 입력하고 `Create role` 버튼을 눌러서 Role 생성 작업을 마친다.
<img src="./img/201704/0425_create_instance_profile_8.png" width="880px">

이로써 **MyCodeDeployRole**과 **MyCodeDeploy-EC2-Instance-Profile**의 2 개 Role이 생성되었다.
<img src="./img/201704/0425_create_instance_profile_9.png" width="500px">

-------
이 글의 내용은 AWS CodeDeploy User Guide, [Getting Started](http://docs.aws.amazon.com/codedeploy/latest/userguide/getting-started-codedeploy.html)의 [Step 3](http://docs.aws.amazon.com/codedeploy/latest/userguide/getting-started-create-service-role.html)와 [Step 4](http://docs.aws.amazon.com/codedeploy/latest/userguide/getting-started-create-iam-instance-profile.html)를 참고하였음