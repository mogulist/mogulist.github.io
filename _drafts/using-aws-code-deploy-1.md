---
layout: post
comments: true
title: AWS CodeDeploy 사용 해보기
header: AWS CodeDeploy 사용 해보기
permalink: 
---

3개의 인스턴스를 갖는 Deployment Group 에 아주 간단한 node.js 코드를 디플로이한다


전재조건
- EC2 Key Pair를 생성해두었다. (EC2 인스턴스에 SSH 로그인하기 위하여 필요함)

Security Group

CodeDeployRolePolicies: IAM Policy
CodeDeployTrustRole: IAM Role
InstanceRole : IAM Role
InstanceRoleInstanceProfile : IAM InstanceProfile
InstanceRolePolicies : IAM Policy

### Service Role 만들기
---------
IAM의 Role 메뉴를 선택하고 Create Role 버튼을 클릭한다
<img src="img/2017-04-25_create_service_role_1.png" width="800px*>



### IAM Instance Profile 만들기
---------

