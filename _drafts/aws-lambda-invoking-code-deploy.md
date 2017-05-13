---
layout: post
comments: true
title: AWS Lambda로 CodeDeploy를 실행시키기
header: AWS Lambda로 CodeDeploy를 실행시키기
permalink: 
---
Deploy 생성에 필요한 값들을 변수로 받아와야 일반화시킬 수 있다.
Lambda 설정할 때 Environment Variable에 설정하면 된다.

{% highlight js linenos 11 %}

{% endhighlight %}

{% highlight js linenos 11 %}
var codedeploy = new AWS.CodeDeploy();
var codedeploy = new AWS.CodeDeploy({apiVersion: '2014-10-06'});

AWS.config.apiVersions = {
  codedeploy: '2014-10-06',
  // other service API versions
};
var codedeploy = new AWS.CodeDeploy();
{% endhighlight %}

{% highlight js linenos 11 %}
var params = {
  applicationName: 'STRING_VALUE', /* required */
  autoRollbackConfiguration: {
    enabled: true || false,
    events: [
      DEPLOYMENT_FAILURE | DEPLOYMENT_STOP_ON_ALARM | DEPLOYMENT_STOP_ON_REQUEST,
      /* more items */
    ]
  },
  deploymentConfigName: 'STRING_VALUE',
  deploymentGroupName: 'STRING_VALUE',
  description: 'STRING_VALUE',
  ignoreApplicationStopFailures: true || false,
  revision: {
    gitHubLocation: {
      commitId: 'STRING_VALUE',
      repository: 'STRING_VALUE'
    },
    revisionType: S3 | GitHub,
    s3Location: {
      bucket: 'STRING_VALUE',
      bundleType: tar | tgz | zip,
      eTag: 'STRING_VALUE',
      key: 'STRING_VALUE',
      version: 'STRING_VALUE'
    }
  },
  targetInstances: {
    autoScalingGroups: [
      'STRING_VALUE',
      /* more items */
    ],
    tagFilters: [
      {
        Key: 'STRING_VALUE',
        Type: KEY_ONLY | VALUE_ONLY | KEY_AND_VALUE,
        Value: 'STRING_VALUE'
      },
      /* more items */
    ]
  },
  updateOutdatedInstancesOnly: true || false
};

codedeploy.createDeployment(params, function(err, data) {
  if (err) console.log(err, err.stack); // an error occurred
  else     console.log(data);           // successful response
});
{% endhighlight %}
