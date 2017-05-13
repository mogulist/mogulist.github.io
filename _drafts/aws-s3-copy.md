---
layout: post
comments: true
title: AWS S3 간 오브젝트 복사하기
header: AWS CodeBuild의 빌드 산출물을 다른 리존의 CodeDeploy에게 보내기 (S3 간 오브젝트 복사하기)
permalink: 
---
## 배경
----

빌드한 산출물(build artifact)을 여러 서로 다른 리존(region)에 deploy 하거나, CodeBuild가 지원되지 않는 리존의 EC2 인스턴스에 deploy 하려면, 다른 리존의 CodeDeploy에게 빌드 산출물을 전달해주어야 한다. CodeBuild가 빌드하고 단위테스트한 결과는 CodeDeploy에게 전달되어 deploy되는데, CodeBuild와 CodeDeploy는 같은 리존의 S3 bucket에서만 읽고 쓸 수 있다. 따라서 두 서비스의 리존이 다른 경우 다음 그림처럼 각 리존에 S3 bucket이 따로 존재해야 하고, S3 간 파일이 복사되어야 한다.

<img src="">

리존 X의 S3 bucket에 파일이 생성되면 신속하게 리존 Y의 S3 bucket으로 파일이 복사되어야 하는데, 이 기능은 S3에 기본적으로 있다. 그런데, CodeBuild가 빌드 산출물을 S3 bucket에 저장할 때 리존 내에서만 유효한 암호화를 하기 때문에, 다른 리존의 S3로 복사하는 경우, CodeDeploy가 빌드 산출물을 인식하지 못한다. 수동으로 리존 X의 S3에서 빌드 산출물을 다운받아서 리존 Y의 S3에 업로드하면 암호화되어 있지 않아서 CodeDeploy 잘 읽어서 deploy 한다. CodeBuild가 S3에 빌드 산출물을 저장할 때 전혀 암호화하지 않으면 좋겠으나, 현재로써는 암호화하지 않게 하는 옵션을 선택할 수 없는 것 같다. 그렇다고 CD/CI 파이프라인에서 수동으로 파일 복사한다는 것은 있을 수 없다. 

다음 그림처럼 Lambda 함수를 이용하면 된다. 다행히 GitHub에 괜찮은 오픈소스 aws-lambda-copy-s3-object 가 있다.
<img src="">

## aws-lambda-copy-s3-object 활용하기
---

serverless 프레임워크를 사용하면 더욱 편리하게 사용할 수 있겠으나, AWS console을 활용하여 등록하는 방법으로 설명한다.

`aws-lambda-copy-s3-object`는 지정한 S3 버킷(소스 S3 버킷)에 오브젝트(파일이라고 봐도 무방함)가 생성되면 지정한 다른 S3 버킷(타겟 S3 버킷)으로 복사해주는 Lambda 함수이다. 소스와 타겟이 같은 리존이어도 되고 다른 리존이어도 된다. 

이 것을 설치하고 활용하는데 가장 중요한 부분은 소스와 타겟을 잘 설정하는 것이다.
- **소스 S3 버킷**은 Lambda 함수 설정할 때 `Triggers` 탭에서 Event 소스로 S3 서비스를 선택하고 설정한다.
- **타겟 S3 버킷**은 **Lambda 함수가 아닌 타겟 S3 버킷**에서 설정한다. 타겟 S3 버킷의 `S3 properties` 중 `Tags` 설정하는 부분에 태그를 지정하는 방식으로 설정한다.

### 소스코드 받고 수정하기
---
GitHub에서 소스코드를 받는다.
```git
$ git clone https://github.com/eleven41/aws-lambda-copy-s3-objects.git
```

index.js 파일의 12번째 줄의 `var config` 부분( - 표시된 줄)을 '+' 표시된 줄에서 처럼 `signatureVersion: 'v4'` 를 추가해준다. 수정하지 않으면 `AWS::S3::Errors::InvalidRequest - Please use AWS4-HMAC-SHA25` 오류가 발생한다.

{% highlight html linenos 11 %}
   function createS3(regionName) {
 -    var config = { apiVersion: '2006-03-01' };
 +    var config = { apiVersion: '2006-03-01', signatureVersion: 'v4' };
{% endhighlight %}

### 패키징 하기
---
`npm install`로 필요한 노드 모듈을 다운 받은 후 하나의 zip 파일로 묶는다.

### AWS Lambda Console 설정하기
---



 Oregon에 만든 S3 bucket의 파일이 자동으로 Seoul에 만든 S3 bucket으로 복사되게 하는 기능이 기본적으로 S3에 있다. 그러나, S3의 기본 기능으로 다른 리존에 복제된 파일(또는 오브젝트)은 AWS의 일부 서비스에서는 활용할 수 없는 경우가 있다. S3의 암호화   