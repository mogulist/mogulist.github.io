---
layout: post
comments: true
title: AWS CodeDeploy 이해하기
header: AWS CodeDeploy 이해하기
permalink: aws-code-deploy-1-understanding
---

AWS CodeDeploy는 빌드 산출물을 서버에 디플로이하고 업데이트된 애플리케이션을 실행시켜주는 Continuous Delivery의 최종 단계를 담당하는 서비스이다. 상세한 활용 방법을 보기 전에 먼저 살짝 CodeDeploy를 이해해보도록 하자

### AWS CodeDeploy의 기본적인 동작 방식
-----------
CodeDeploy를 활용하기 전에 CodeDeploy와 관련된 개념들을 이해하고 무엇을 할 수 있는지 알아 둘 필요가 있다. 다음 그림은 CodeDeploy의 기본적인 동작 방식을 보여준다. 
 1. S3의 특정 revision 아카이브 파일을 EC2 인스턴스로 가져온다
 2. 아카이브 파일의 압축을 푼다. 
 3. Deploy Agent는 압축을 푼 소스에서 AppSpec.yml에 명세된 지시사항을 처리한다
    1. WAS를 정지 시킨다
    2. 소스의 내용을 WAS의 폴더로 복사한다
    3. WAS를 시작시킨다
<img src="img/170425-codedeploy-overall-01.png" width="890px">

CodeDeploy 서비스는 S3나 GitHub로부터 빌드 산출물 아카이브를 가져와서 서버에 디플로이한다. CodeDeploy는 빌드 기능은 없기 때문에 빌드가 필요한 애플리케이션은 반드시 CodeBuild나 별도의 빌드 과정을 통해서 빌드가 완료된 상태여야 하며, S3 Bucket을 활용하는 경우 빌드 산출물은 하나의 파일로 아카이브된 상태여야 한다.

CodeDeploy가 실행되기 위해서는 서버에 CodeDeploy Agent가 반드시 실행 중이어야 한다. 서버는 AWS EC2 인스턴스들이거나 AWS AutoScaling Group, 또는 AWS가 아닌 서버(on-premise 서버)일 수도 있다. 중요한 것은 CodeDeploy Agent가 실행 중이어야 한다는 것.

CodeDeploy는 기존 서버 인스턴스의 내용을 덮어씌우는 방식(In-place deployment)으로 디플로이할 수도 있고, 새로운 서버 인스턴스를 프로비저닝하고 여기에 디플로이한 후 기존 인스턴스를 통채로 대체하는 방식(Blue/Green deployment)으로 디플로이할 수도 있다. 서버 인스턴스가 여러 개 있는 경우, 하나씩 차례대로 디플로이하게 할 수도 있고, 반반씩 디플로이할 수도 있고, 전체를 한꺼번에 디플로이할 수도 있다.

CodeDeploy가 위와 같이 디플로이를 진행하다가 어떤 문제가 발생하면 이전 상태로 롤백시켜준다. 자동으로 롤백할 수도 있고, 일부 서버 인스턴스에서만 디플로이하여 테스트해보니 문제가 있다고 판단되면 사람이 롤백시킬 수도 있다.

AppSpec.yml은 CodeDeploy를 위한 작업 지시서이다. AppSpec.yml에는 빌드 산출물의 어떤 디렉토리나 파일들을 서버 인스턴스의 어느 디렉토리로 복사해야 하는지, 빌드 산출물을 디플로이하기 전에 WAS를 일시정지시켜야 하면 어떤 스크립트를 실행시켜야 하고, 디플로이한 후에 WAS를 재시작시켜야 하면 어떤 스크립트를 실행해야 하는지가 명세되어 있다. 


### Application, Deployment Group, Deployment
-----------
CodeDeploy를 설정하기 위해서는 Application, Deployment Group, Deployment의 세 가지 컴포넌트의 차이를 이해해야 한다. 디플로이와 관련된 설정을 반복적으로 재활용하기 좋게 하기 위하여 나눠 놓은 것이다. 

아래 그림에서 처럼, Application 은 어떤 소프트웨어를 배포하는지 여부와 관련된 설정을 담고 있다. Deployment Group은 디플로이할 대상이 되는 서버 인스턴스들을 의미하며 디플로이 방식과 관련된 설정도 갖는다. 마지막으로 Deployment는 실제 디플로이가 실행되는 단위로써 소프트웨어의 어떤 리비전을 어느 Deployment Gruop에게 디플로이하고 실행하는 지에 대한 정보를 갖는다.

<img src="img/170425-codedeploy-overall-02.png" width="880px">

"상품관리"라는 하나의 소프트웨어 애플리케이션이 있는 경우, 이 애플리케이션을 위한 운영 서버 인스턴스들, 스테이징 서버 인스턴스들, Feature-X를 위한 테스트 서버 인스턴스들, Feature-Y를 위한 테스트 서버 인스턴스들 등, 다양한 인스턴스들이 존재할 수 있을 것이다. 각각을 미리 Deployement Group으로 설정을 만들어 둔다면, 즉 운영 Deployment Gruop, 스테이징 Deployment Group, Feature-X Deployment Group과 같이 만들어 둔다면, 리비전과 Deployment Group을 지정함으로써 신속히 Deployment를 만들어 디플로이 작업을 실행할 수 있다. 이와 같은 편의를 위하여 Deployment Group이 별도록 존재하는 것이다.

다음 그림처럼, 하나의 Application은 여러 Deployment Group을 가질 수 있다. 리비전과 Deployment Group을 조합하여 Deployment가 만들어지고 디플로이 작업이 실행된다. 동일한 리비전과 동일한 Deployment Group을 지정하여 여러 번 디플로이할 수 있므며 이 때마다 Deployment는 새롭게 생성되고 실행된다.

<img src="img/170425-codedeploy-overall-03.png" width="880px">

다음 그림은 여러 브랜치가 있는 경우를 보여준다. 하나의 소프트웨어라도 여러 브랜치를 가질 수 있다. Master 브랜치는 Production Deployment Group에, Develop 브랜치는 Staging, Test1, Test2와 같은 여러 Deployment Group에, Feature-X 브랜치는 Feature-X Deployment Group에 지정되어 디플로이되고 실행되도록 할 수 있을 것이다.

<img src="img/170425-codedeploy-overall-04.png" width="820px">


### CodeDeploy 준비 및 실행 절차
----------
다음은 CodeDeploy를 준비하고 실행하는 절차를 보여주는데 다음과 같은 사항들이 전재된 상황에서의 절차이다.

- EC2 인스턴스에 배포하는 경우로 한정한다. (소스코드를 EC2 인스턴스 뿐만 아니라 Auto scaling group에도 배포할 수 있음)
- 사용자는 AWS 계정이 있으며 관련 서비스에 접근권한이 있다.

<img src="img/170425-codedeploy-steps-01.png" width="760px">

준비 과정을 미리 훑어보도록 하자.

가장 먼저 적절한 접근권한이 준비되어야 한다. IAM Instance Profile을 생성하고 EC2 인스턴스들에게 할당하여 EC2 인스턴스가 S3 Bucket에 저장되어 있는 빌드 산출물을 가져올 수 있게 한다. Service Role을 생성하고 CodeDeploy에게 할당하여 CodeDeploy가 EC2 인스턴스들에 접근할 수 있게 한다.

두번째로는 AWS EC2 인스턴스들을 준비한다. 하나 이상의 EC2 인스턴스들을 생성한 후 반드시 CodeDeploy Agent를 EC2 인스턴스들에 설치하고 실행시킨다.

세번째로는 AppSpec.yml 파일 소스코드의 루트 디렉토리에 생성한 후 디플로이 과정을 명세한다. 또한 WAS를 정지시키고 실행시키는 것과 관련된 스크립트를 만들어서 소스코드에 추가하고AppSpec.yml의 관련 후크에도 등록한다. AppSpec.yml 파일이 없으면 디플로이가 시작되자마자 멈춘다.

네번째로는 AWS S3 Bucket을 준비한다. (이 절차 중 가장 간단하다)

다섯번째로는 빌드 산출물을 zip 파일로 만들어서 AWS S3 Bucket에 업로드한다. CodeBuild와 같은 Continuous Integration 도구를 활용한다면 자동으로 AWS S3 Bucket에 빌드 산출물을 업로드해줄 것이지만, 지금은 오로지 CodeDeploy만 사용하는 경우만 다룬다.

이제 준비 과정이 완료되었다. 마지막 여섯번째로 CodeDeploy 콘솔(또는 어드민)을 활용하여 CodeDeploy Application과 Deployment Group을 생성한다. 그런 다음 S3 Bucket에 업로드한 파일(리비전)과 Deployment Group을 지정하면서 실제 디플로이를 실행한다.