---
layout: post
comments: true
title: serverless 를 활용하여 AWS Lambda 함수 개발하기
header: serverless 프레임워크를 활용하여 AWS Lambda 함수 개발하기
permalink: develop-aws-lambda-with-serverless
---

참고문서: "[Serverless Quick Start](https://serverless.com/framework/docs/providers/aws/guide/quick-start/)"

### 준비
-----
AWS에 계정이 있고 EC2 인스턴스에 로그인할 수 있는 credential이 준비되어 있어야 한다.

## 초간단 실행해보기
----
npm으로 serverless를 컴퓨터에 설치한다: `$ npm install -g serverless`

`my-service` 라는 node.js 기반 serverless 프로젝트 폴더와 템플릿을 만들고, 프로젝트 폴더로 이동한다.
{% highlight bsh linenos %}
$ serverless create --template aws-nodejs --path my-service
$ cd my-service 
{% endhighlight %}

`my-service` 폴더에 `serverless.yml` 과 `handler.js` 파일이 생성되었다. 기본 파일을 먼저 Lambda에 deploy 하여 실행해보자. 
`serverless deploy` 커맨드는 AWS에 Lambda 함수를 deploy한다. `-v` 옵션은 `--verbose`와 동일하게 deploy 과정을 상세하게 보여준다.  
serverless는 현재 쉘에서 지정된 AWS 사용자 profile의 리존에 Lambda 함수를 deploy 한다. 다음은 명시적으로 Seoul 리존에 deploy 하기 위하여 `--region ap-northeast-2` 옵션을 주었다. 커맨들를 실행시키면 2~5분 동안 deploy가 실행되면서 다음과 같은 내용을 보여준다.

{% highlight ruby linenos %}
$ serverless deploy -v --region ap-northeast-2
Serverless: Creating Stack...
Serverless: Checking Stack create progress...
CloudFormation - CREATE_IN_PROGRESS - AWS::CloudFormation::Stack - my-service-dev
CloudFormation - CREATE_IN_PROGRESS - AWS::S3::Bucket - ServerlessDeploymentBucket
CloudFormation - CREATE_IN_PROGRESS - AWS::S3::Bucket - ServerlessDeploymentBucket
CloudFormation - CREATE_COMPLETE - AWS::S3::Bucket - ServerlessDeploymentBucket
CloudFormation - CREATE_COMPLETE - AWS::CloudFormation::Stack - my-service-dev
Serverless: Stack create finished...
Serverless: Packaging service...
Serverless: Uploading CloudFormation file to S3...
Serverless: Uploading function .zip files to S3...
Serverless: Uploading service .zip file to S3 (409 B)...
Serverless: Updating Stack...
Serverless: Checking Stack update progress...
CloudFormation - CREATE_COMPLETE - AWS::CloudFormation::Stack - my-service-dev
CloudFormation - UPDATE_IN_PROGRESS - AWS::CloudFormation::Stack - my-service-dev
CloudFormation - CREATE_IN_PROGRESS - AWS::IAM::Role - IamRoleLambdaExecution
CloudFormation - CREATE_IN_PROGRESS - AWS::Logs::LogGroup - HelloLogGroup
CloudFormation - CREATE_IN_PROGRESS - AWS::IAM::Role - IamRoleLambdaExecution
CloudFormation - CREATE_IN_PROGRESS - AWS::Logs::LogGroup - HelloLogGroup
CloudFormation - CREATE_COMPLETE - AWS::Logs::LogGroup - HelloLogGroup
CloudFormation - CREATE_COMPLETE - AWS::IAM::Role - IamRoleLambdaExecution
CloudFormation - CREATE_IN_PROGRESS - AWS::Lambda::Function - HelloLambdaFunction
CloudFormation - CREATE_IN_PROGRESS - AWS::Lambda::Function - HelloLambdaFunction
CloudFormation - CREATE_COMPLETE - AWS::Lambda::Function - HelloLambdaFunction
CloudFormation - CREATE_IN_PROGRESS - AWS::Lambda::Version - HelloLambdaVersionZ1xjQuNo5i6o67QadTgGgxwcoIAIX82nVdzJtVMQI
CloudFormation - CREATE_IN_PROGRESS - AWS::Lambda::Version - HelloLambdaVersionZ1xjQuNo5i6o67QadTgGgxwcoIAIX82nVdzJtVMQI
CloudFormation - CREATE_COMPLETE - AWS::Lambda::Version - HelloLambdaVersionZ1xjQuNo5i6o67QadTgGgxwcoIAIX82nVdzJtVMQI
CloudFormation - UPDATE_COMPLETE_CLEANUP_IN_PROGRESS - AWS::CloudFormation::Stack - my-service-dev
CloudFormation - UPDATE_COMPLETE - AWS::CloudFormation::Stack - my-service-dev
Serverless: Stack update finished...
Service Information
service: my-service
stage: dev
region: ap-northeast-2
api keys:
  None
endpoints:
  None
functions:
  hello: my-service-dev-hello

Stack Outputs
HelloLambdaFunctionQualifiedArn: arn:aws:lambda:ap-northeast-2:101724933417:function:my-service-dev-hello:1
ServerlessDeploymentBucketName: my-service-dev-serverlessdeploymentbucket-1vj53b5zhwcre
{% endhighlight %}

`invoke` 커맨드로 Lambda 함수를 실행시킨다. `-f` 옵션 뒤에 Lambda 함수 이름을 지정해야 하는데, 위 deploy 결과 중 `functions` 부분에 있는 `hello`가 함수 이름이다. 
{% highlight bsh linenos %}
$ serverless invoke -f hello -l
{% endhighlight %}

예제 Lambda 함수가 실행되면서 다음과 같은 내용을 보여준다.
{% highlight ruby linenos %}
{
    "statusCode": 200,
    "body": "{\"message\":\"Go Serverless v1.0! Your function executed successfully!\",\"input\":{}}"
}
--------------------------------------------------------------------
START RequestId: 9f69af38-36ea-11e7-8cda-c946bf4dc0b8 Version: $LATEST
END RequestId: 9f69af38-36ea-11e7-8cda-c946bf4dc0b8
REPORT RequestId: 9f69af38-36ea-11e7-8cda-c946bf4dc0b8	
Duration: 2.20 ms  Billed Duration: 100 ms  Memory Size: 1024 MB  Max Memory Used: 19 MB	
{% endhighlight %}

직접 Lambda 함수를 AWS Console에 들어가서 실행하려고 했다면 훨씬 시간이 많이 걸렸을 것이다. serverless를 이용하면 정말 빠르고 편하다.

## AWS 콘솔에서 확인하기
---
AWS의 Lambda 서비스를 선택한다.
<img src="img/201705/aws-lambda-serverless-00.png">

Lambda 함수들이 보이면 그 중에서 `my-service-dev-hello` 를 선택한다.
<img src="img/201705/aws-lambda-serverless-01.png">

Code 탭에서는 소스코드를 확인할 수 있다.
<img src="img/201705/aws-lambda-serverless-02.png">

Configuration 탭에서는 아래와 같은 내용을 확인할 수 있다.
<img src="img/201705/aws-lambda-serverless-03.png">

Triggers 탭에서는 이 Lambda 함수가 실행되는 조건을 정하는데, 아직 아무것도 지정되어 있지 않다.
<img src="img/201705/aws-lambda-serverless-04.png">

Tags 탭에는 STAGE 태그가 추가되어 있다. 템플릿이 기본적으로 추가하는 것이다.
<img src="img/201705/aws-lambda-serverless-05.png">

Monitoring 탭에서는 Lambda 함수의 실행 결과를 그래프로 볼 수 있다. 오른쪽 상단의 `View logs in CloudWatch`를 클릭한다.
<img src="img/201705/aws-lambda-serverless-06.png">

CloudWatch에 있는 로그 스트림 중에서 가장 최신 것을 선택한다.
<img src="img/201705/aws-lambda-serverless-07.png">

다음과 같이 Lambda 함수가 실행된 결과를 볼 수 있다. (시간이 한국 시간이 아니라 햇갈린다)
<img src="img/201705/aws-lambda-serverless-08.png">

CloudFormation 서비스로 가보면 아래와 같이 `my-service-dev`가 있는 것을 확인할 수 있다
<img src="img/201705/aws-lambda-serverless-09.png">

## 소스코드 확인하기
---
node.js를 위한 Lambda 함수의 기본 틀을 보여준다.
{% highlight js linenos %}
'use strict';

module.exports.hello = (event, context, callback) => {
  const response = {
    statusCode: 200,
    body: JSON.stringify({
      message: 'Go Serverless v1.0! Your function executed successfully!',
      input: event,
    }),
  };

  callback(null, response);

  // Use this code if you don't use the http event with the LAMBDA-PROXY integration
  // callback(null, { message: 'Go Serverless v1.0! Your function executed successfully!', event });
};
{% endhighlight %}

Serverless를 통해서 deploy 한 Lambda 함수를 AWS에서 삭제하기
{% highlight ruby linenos %}
$ sls remove
Serverless: Getting all objects in S3 bucket...
Serverless: Removing objects in S3 bucket...
Serverless: Removing Stack...
Serverless: Checking Stack removal progress...
..........
Serverless: Stack removal finished...
{% endhighlight %}

serverless remove를 활용하지 않고, AWS의 Lambda 콘솔을 통해서 Lambda 함수를 삭제하면, 다시 deploy 하려고 할 때 다음과 같이 `Function Not Found` 오류가 발생하면 deploy 되지 않는다.
{% highlight ruby linenos %}
$ serverless deploy -v
Serverless: Packaging service...
Serverless: Uploading CloudFormation file to S3...
Serverless: Uploading function .zip files to S3...
Serverless: Uploading service .zip file to S3 (420 B)...
Serverless: Updating Stack...
Serverless: Checking Stack update progress...
CloudFormation - UPDATE_IN_PROGRESS - AWS::CloudFormation::Stack - aws-lambda-run-deployment-dev
CloudFormation - UPDATE_IN_PROGRESS - AWS::Lambda::Function - HelloLambdaFunction
CloudFormation - UPDATE_FAILED - AWS::Lambda::Function - HelloLambdaFunction
CloudFormation - UPDATE_ROLLBACK_IN_PROGRESS - AWS::CloudFormation::Stack - aws-lambda-run-deployment-dev
CloudFormation - UPDATE_COMPLETE - AWS::Lambda::Function - HelloLambdaFunction
CloudFormation - UPDATE_ROLLBACK_COMPLETE_CLEANUP_IN_PROGRESS - AWS::CloudFormation::Stack - aws-lambda-run-deployment-dev
CloudFormation - UPDATE_ROLLBACK_COMPLETE - AWS::CloudFormation::Stack - aws-lambda-run-deployment-dev
Serverless: Deployment failed!
 
  Serverless Error ---------------------------------------
 
     An error occurred while provisioning your stack: HelloLambdaFunction
     - Function not found: arn:aws:lambda:us-east-1:101724933417:function:aws-lambda-run-deployment-dev-hello.
{% endhighlight %}

Seoul 리존에 deploy 하기
```
$ sls deploy --region ap-northeast-2 -v
```


S3 Put Event
{% highlight ruby linenos %}
{
  "Records": [
    {
      "eventVersion": "2.0",
      "eventTime": "1970-01-01T00:00:00.000Z",
      "requestParameters": {
        "sourceIPAddress": "127.0.0.1"
      },
      "s3": {
        "configurationId": "testConfigRule",
        "object": {
          "eTag": "0123456789abcdef0123456789abcdef",
          "sequencer": "0A1B2C3D4E5F678901",
          "key": "HappyFace.jpg",
          "size": 1024
        },
        "bucket": {
          "arn": bucketarn,
          "name": "sourcebucket",
          "ownerIdentity": {
            "principalId": "EXAMPLE"
          }
        },
        "s3SchemaVersion": "1.0"
      },
      "responseElements": {
        "x-amz-id-2": "EXAMPLE123/5678abcdefghijklambdaisawesome/mnopqrstuvwxyzABCDEFGH",
        "x-amz-request-id": "EXAMPLE123456789"
      },
      "awsRegion": "us-east-1",
      "eventName": "ObjectCreated:Put",
      "userIdentity": {
        "principalId": "EXAMPLE"
      },
      "eventSource": "aws:s3"
    }
  ]
}
{% endhighlight %}