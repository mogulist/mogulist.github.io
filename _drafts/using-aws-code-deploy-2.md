---
layout: post
comments: true
title: AWS CodeDeploy | EC2 인스턴스 만들기
header: AWS CodeDeploy 사용해보기 [2] EC2 인스턴스 만들기
permalink: 
---
node.js 샘플 애플리케이션을 실행할 서버 인스턴스를 한 개를 만든다.

**시작하기 전에**
- Key Pairs를 미리 만들어 둘 수 있다.
  - EC2 인스턴스에 접속할 때 사용하는 public key와 private key 쌍을 의미한다. 한 번 만들어서 여러 인스턴스들에 접속할 때 활용할 수 있다
  - 미리 만들지 않고 진행해도 무방하다. AWS가 자동으로 생성해준다. 단, 자동으로 생성하는 것은 이름이 어려워서 재활용하기 어렵다
- VPC와 Subnet을 미리 만들어 둘 수 있다.
  - 역시 미리 만들지 않아도 AWS가 자동 생성해주기는 한다
- EC2 Instance Profile은 1단계에서 이미 만들었다.
- 혹시 AWS, 서버, 네트워크에 익숙하지 않다면 ["Opentutorials.org 사이트의 생활코딩 강좌 중 아마존 웹서비스(AWS)"](https://opentutorials.org/course/2717) 온라인 강좌들이 큰 도움이 될 것이다. 

-----------
시작해보자

1) EC2 서비스의 Dashboard에서 `Launch Instance` 버튼을 클릭한다
<img src="./img/201704/0426_create_ec2_instance_1.png" width="740px">

2) 제일 위에 보이는 **Amazon Linux AMI**를 선택한다 (다른 어떤 AMI를 선택해도 무방하다)
<img src="./img/201704/0426_create_ec2_instance_2.png" width="860px">

3) 공짜 인스턴스인 **t2.micro**를 선택하고 다음 단계로 이동한다.
<img src="./img/201704/0426_create_ec2_instance_3.png" width="820px">

4) 인스턴스의 네트워크와 접근권한에 대해 설정한다
- **Number of instances**는 생성할 인스턴스의 수인데, 기본값인 1을 그대로 둔다. 
- **Network**와 **Subnet**은 미리 생성한 것을 선택한다
- **Auto-assign Public IP**는 **Enable**로 변경한다.
- **IAM Role**에는 이전 강좌에서 만든 Instance Profile(**MyCodeDeploy-EC2-Instance-Profile**)을 선택한다.
<img src="./img/201704/0426_create_ec2_instance_4.png" width="760px">

5) 하단의 **Advanced Details**를 열면 **User data** 입력란이 나타난다. 다음 스크립트를 복사하여 붙여넣는다. 이 스크립트는 CodeDeploy Agent를 설치하고, node.js와 npm 도 설치한다.<br>
`Next: Add Storage` 버튼을 클릭하여 다음 단계로 이동한다.
{% highlight perl linenos %}
#!/bin/bash
yum -y update
yum install -y ruby
cd /home/ec2-user
curl -O https://aws-codedeploy-ap-northeast-2.s3.amazonaws.com/latest/install
chmod +x ./install
./install auto
cd ~ec2-user
curl --silent --location https://rpm.nodesource.com/setup_6.x | bash -
yum install -y nodejs
{% endhighlight %}
<img src="./img/201704/0426_create_ec2_instance_5.png" width="760px">

6) Storage는 기본 설정 그대로 두고 다음 단계로 이동한다.
<img src="./img/201704/0426_create_ec2_instance_6.png" width="800px">

7) 태그를 추가하는 단계이다. `Add Tag` 버튼을 클릭한다
<img src="./img/201704/0426_create_ec2_instance_7.png" width="800px">

8) **Key**와 **Value** 란에 **Name**과 **MyCodeDeployTest0426**을 입력한다. CodeDeploy 연습한다고 여러 번 CodeDeploy 환경 만드는 걸 하다 보면 여러 EC2 인스턴스들이 생성되는데, 나중에 각 인스턴스의 용도가 햇갈리게 되므로 Value 값을 적절히 주는게 좋다.
<img src="./img/201704/0426_create_ec2_instance_8.png" width="800px">

9) Security Group을 지정한다. 미리 만들어 놓은 Security Group을 선택하거나 AWS가 자동으로 만들도록 할 수 있다. 기본적으로 SSH를 통해 서버에 접속할 수 있는 방화벽 설정만 되어 있다. 소스 부분이 `0.0.0.0/0`으로 되어 있는데, 이는 아무나 22번 포트로 접속할 수 있도록 허용하는 것으로써 바람직하지 않지만 연습하는 단계이므로 그대로 둔다.
<img src="./img/201704/0426_create_ec2_instance_9.png" width="800px">

10) 샘플 node.js는 웹서버 역할을 하므로 인터넷 어디서든 80포트로 접근할 수 있는 방화벽 룰을 추가해야 한다. `Add Rule` 버튼을 누르고 **Type**에서 HTTP를 선택하면 아래와 같이 된다. 식별하기 용이하도록 "my" 접두어를 붙인 Security group 이름으로 변경한다.
<img src="./img/201704/0426_create_ec2_instance_11.png" width="800px">

11) 최종적으로 지금까지 설정한 것을 리뷰하는 페이지이다. 보안이 취약하다는 주의문을 보여준다. `Launch` 버튼을 클릭한다.
<img src="./img/201704/0426_create_ec2_instance_12.png" width="760px">

12) Key Pair를 선택하는 마지막 단계이다. 미리 만들어 놓은 Key Pair를 선택하거나 AWS가 새로 만들도록 할 수도 있다. 아래 예에서는 미리 만든 **myKeyPair**를 선택하였다.
<img src="./img/201704/0426_create_ec2_instance_13.png" width="620px">

13) 인스턴스가 만들어지고 있다는 화면이 나타난다. 하단의 `View Instances`를 클릭하면 EC2의 **Instances** 메뉴로 이동한다.
<img src="./img/201704/0426_create_ec2_instance_14.png" width="700px">

14) **EC2 Instances** 화면을 통해 방금 생성한 인스턴스의 상세한 내용과 현재 상태를 확인할 수 있다.
<img src="./img/201704/0426_create_ec2_instance_15.png" width="800px">

15) 인스턴스 부분에서 마우스 오른쪽 버튼을 클릭하면 컨텍스트 메뉴가 나타난다. 맨 위의 **Connect**를 선택한다.
<img src="./img/201704/0426_create_ec2_instance_16.png" width="330px">

16) 그러면, 아래와 같이 해당 인스턴스에 접속하는 방법을 보여준다.
<img src="./img/201704/0426_create_ec2_instance_17.png" width="800px">
<br><br>
CodeDeploy Agent가 정상적으로 설치되었고 실행 중인지 확인하려면 인스턴스에 SSH로 로그인하여 다음 명령어를 실행하도록 한다.
> $ sudo service codedeploy-agent status<br>
> The AWS CodeDeploy agent is running as PID 2696
