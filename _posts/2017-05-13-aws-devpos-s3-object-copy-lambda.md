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

<img src="./img/201705/aws-codepipeline-different-regions-01.png" width="780px">

리존 X의 S3 bucket에 파일이 생성되면 신속하게 리존 Y의 S3 bucket으로 파일이 복사되어야 하는데, 이 기능은 S3에 기본적으로 있다. 그런데, CodeBuild가 빌드 산출물을 S3 bucket에 저장할 때 리존 내에서만 유효한 암호화를 하기 때문에, 다른 리존의 S3로 복사하는 경우, CodeDeploy가 빌드 산출물을 인식하지 못한다. 수동으로 리존 X의 S3에서 빌드 산출물을 다운받아서 리존 Y의 S3에 업로드하면 암호화되어 있지 않아서 CodeDeploy가가 잘 읽어서 deploy 한다. CodeBuild가 S3에 빌드 산출물을 저장할 때 전혀 암호화하지 않으면 좋겠으나, 현재로써는 암호화하지 않게 하는 옵션을 선택할 수 없는 것 같다. 그렇다고 CD/CI 파이프라인에서 수동으로 파일 복사한다는 것은 있을 수 없다. 

Lambda 함수를 이용하면 된다. 다음 그림은 Lambda가 Orgeon 리존의 S3 버킷에 빌드 산출물 파일이 새로 생성되면 트리거되어 Seoul의 지정된 S3 버킷으로 복사하는 예이다.
<img src="./img/201705/aws-codepipeline-different-regions-02.png" width="780px">

다음과 같이 Lambda 함수를 Oregon의 CodePipeline에 넣을 수도 있다. 빌드가 완료되면, CodePipeline이 Lambda 함수를 실행시킨다
<img src="./img/201705/aws-codepipeline-different-regions-03.png" width="780px">

S3의 파일을 복사하는 Lambda 함수를 만들어야 하는데 아직 Lambda 함수를 만들어보지 못했더라도 괜찮다. 다행히 GitHub에 괜찮은 오픈소스 `aws-lambda-copy-s3-object` 가 있고 약간만 수정하고 설정하면 잘 동작한다. ^^

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

`index.js` 파일의 12번째 줄의 `var config` 부분( - 표시된 줄)을 아래 소스코드에서 '+' 표시된 줄에서 처럼 `signatureVersion: 'v4'` 를 추가해준다. 수정하지 않으면 `AWS::S3::Errors::InvalidRequest - Please use AWS4-HMAC-SHA25` 오류가 발생한다.

{% highlight html linenos 11 %}
   function createS3(regionName) {
 -    var config = { apiVersion: '2006-03-01' };
 +    var config = { apiVersion: '2006-03-01', signatureVersion: 'v4' };
{% endhighlight %}

### 패키징 하기
---
`npm install`로 필요한 노드 모듈을 다운 받는다. 그런 다음 하나의 zip 파일로 묶는다.

### AWS Lambda Console 설정하기
---
**주의!!** 반드시 소스 S3와 동일한 리존으로 설정한다. 소스 S3가 Seoul에 있다면, Lambda 함수도 Seoul에 만들어야 한다.
혹시 소스 S3와 타겟 S3를 아직 만들지 않았다면 S3 서비스로 이동하여 만든다. 기왕이면 소스와 타겟 S3를 서로 다른 리존에 만든다.
<img src="./img/201705/aws-region-select.png" width="250px">

AWS Lambda 서비스를 선택하고, `Create a Lambda function` 버튼을 클릭한다.
<img src="./img/201705/aws-lambda-copy-s3-setting-00.png" width="360px">

`Blank Function`을 선택한다.
<img src="./img/201705/aws-lambda-copy-s3-setting-01.png" width="500px">

소스 S3 버킷에 새로운 파일이 생성되는 트리거를 지정하는 절차가 시작된다. 아래 그림에서 노랑 형광펜으로 칠한 부분을 클릭한다.
<img src="./img/201705/aws-lambda-copy-s3-setting-021.png" width="450px">

Lambda를 트리거 시킬 수 있는 서비스들이 보인다. 마우스를 스크롤 다운하면 우리에게 필요한 `S3`가 나온다. `S3`를 클릭한다.
<img src="./img/201705/aws-lambda-copy-s3-setting-022.png" width="250px">

S3와 관련된 설정 화면이 나타난다. 
<img src="./img/201705/aws-lambda-copy-s3-setting-023.png" width="320px">

`Bucket` 드랍다운 박스를 열면, 현재 리존의 S3에 만들어져 있는 버킷들이 보인다. 이 중에서 선택한다. 만약 원하는 버킷이 보이지 않는다면, 소스 S3 버킷을 만들지 않았거나, 다른 리존에 만든 것이다.
<img src="./img/201705/aws-lambda-copy-s3-setting-024.png" width="320px">

`Event type`의 드랍다운 박스를 열면 S3와 관련된 이벤트들이 보인다. `Object Created (All)`을 선택한다. 
<img src="./img/201705/aws-lambda-copy-s3-setting-025.png" width="320px">

`Prefix`에는 소스 버킷의 서브 디렉토리나 파일이름의 머리말에 해당하는 부분을 기재한다. `Suffix`는 파일 확장자 등 파일의 뒤쪽에 대한 필터를 기재한다. 아래 예제는 `herbert-test-source/build_artifacts/`에 어떤 파일이든 생성되면 Lambda 함수를 실행시킨다는 의미이다. `Enable trigger`를 선택한다. `Next` 버튼을 클릭하여 다음 단계로 이동한다.
<img src="./img/201705/aws-lambda-copy-s3-setting-02.png" width="740px">

Lambda의 Name과 Description을 입력한다. `Runtime`은 node.js 6.10 버전을 선택한다.
<img src="./img/201705/aws-lambda-copy-s3-setting-03.png" width="590px">

`Lambda function code` 부분에서 `Code entry type` 드랍다운박스를 열고 `Upload a .ZIP file`을 선택한다.
<img src="./img/201705/aws-lambda-copy-s3-setting-031.png" width="590px">

다음과 같이 화면이 변경된다. `Function package`의 `Upload` 버튼을 클릭하여 이전 단계에서 만들어 둔 패키지 ZIP 파일을 업로드 한다. 환경 변수는 특별히 필요한 것이 없으므로 비워둔다.
<img src="./img/201705/aws-lambda-copy-s3-setting-04.png" width="740px">

`Handler` 기본값은 그대로 둔다. `Role`은 미리 만들어 둔 IAM Role이 없다면, `Create new role from template`을 선택한다. 그런 다음 `Role name`을 적당히 입력한다. `Policty templates`는 비워둔다.
<img src="./img/201705/aws-lambda-copy-s3-setting-032.png" width="520px">

만약 미리 만들어 둔 IAM Role이 있다면, `Role`에서 `Choose an existing role`을 선택하고 `Existing role`에서 선택하면 된다.
<img src="./img/201705/aws-lambda-copy-s3-setting-05.png" width="740px">

이 소스코드의 [원저자 설명](https://github.com/eleven41/aws-lambda-copy-s3-objects)을 보면서 설정하다 보면, 바로 여기 `Tags` 란에 아래와 같이 설정하는 실수를 범하기 쉽다. <span style="color:red">`Tags` 란은 그냥 비워둔다.</span> 
<img src="./img/201705/aws-lambda-copy-s3-setting-06.png" width="740px">

### 소스 S3 버킷에서 타겟 설정하기
---
S3 서비스로 이동하고 버킷들 중에서 소스 S3 버킷을 선택한다. 그러면, 아래와 같은 4개의 탭이 보인다. `Properties` 탭을 선택한다.
<img src="./img/201705/aws-lambda-copy-s3-setting-07.png" width="500px">

Versioning, Logging 등 여러 설정 항목들이 사각형 안에 보이는데, 이 중에서 `Tags`를 클릭한다. 그러면, 아래와 같은 화면이 나타난다. `Add tag` 버튼을 클릭한다.
<img src="./img/201705/aws-lambda-copy-s3-setting-08.png" width="460px">

`Key`에는 <span style="color:red">정확하게 "TargetBucket" 을 입력</span>하고, `Value`에는 타겟 S3 버킷의 이름을 기재한다. 
<img src="./img/201705/aws-lambda-copy-s3-setting-09.png" width="410px">

### 테스트 하기
---
소스 S3 버킷에 아무 파일이 업로드하고, 타겟 버킷에 같은 파일이 복사되는지 확인한다.

정상적으로 람다가 수행되면 Cloudformation에서 아래와 같은 로그를 확인할수 있다
<img src="./img/201705/aws-lambda-copy-s3-setting-10.png" width="900px">

### Troubleshooting
---
**이슈**
```
  Unable to import module 'index': Error at Module.load (module.js:487:32) 
    at tryModuleLoad (module.js:446:12) at Function.Module._load (module.js:438:3)
```
**원인**: 소스코드를 zip 파일로 패키징하기 전에 `npm install`을 하지 않아서 필요한 모듈들이 함께 패키징되지 않았기 때문이다.
