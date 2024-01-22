---
layout: post
Categories:  ["tech"]
Description:  " とりあえずaws cliを使ってPythonのAWS Lambda関数を更新するやり方がドキュメントにあったのでメモ。       Python の AWS Lambda デプロイパッケージ - AWS Lambda  "
Tags: ["AWS", "Lambda", "Python", "tech"]
date: "2019-08-04T23:07:00+09:00"
title: "AWS CLIを使ってPythonのAWS Lambda関数を更新する"
author: "dshimizu"
archive: ["2019"]
draft: "true"
---

<body>
<p>とりあえず<a class="keyword" href="http://d.hatena.ne.jp/keyword/aws">aws</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/cli">cli</a>を使って<a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> Lambda関数を更新するやり方がドキュメントにあったのでメモ。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/lambda-python-how-to-create-deployment-package.html">Python の AWS Lambda デプロイパッケージ - AWS Lambda</a></li>
</ul>

</body>

<!-- more -->

<body>
<h3>標準ライブラリのみを使っているLambda関数の更新</h3>


<p>使うコードはLambda関数の設計図の中にあるcloudwatch-alarm-to-slack-<a class="keyword" href="http://d.hatena.ne.jp/keyword/python">python</a>を流用する。</p>

<ul>
    <li><a target="brank" rel="noopener noreferrer" href="https://github.com/d-shimizu/aws-lambda-sample_cloudwatch-alarm-to-slack">d-shimizu/aws-lambda-sample_cloudwatch-alarm-to-slack - GitHub</a></li>
</ul>


<p>関数名はlambda_function.pyとしておかないとLambdaが動かない。</p>

<pre class="terminal"> $ cd ~/aws-lambda-sample_cloudwatch-alarm-to-slack

$ ls
README.md  lambda_function.py.py  test-event.json
 </pre>


<p>単純に関数コードをzip<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%A2%A1%BC%A5%AB%A5%A4%A5%D6">アーカイブ</a>にし、<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/CLI">CLI</a> を使用してアップロードすれば良い。</p>

<pre class="terminal"> $ zip lambda_function.zip lambda_function.py 
  adding: lambda_function.zip (deflated 57%)
 </pre>


<pre class="terminal"> $ aws lambda update-function-code --function-name cloudwatch-alarm-to-slack-python --zip-file fileb://lambda_function.zip --profile admin
{
    "FunctionName": "cloudwatch-alarm-to-slack-python",
    "LastModified": "2019-08-04T00:21:16.455+0000",
    "RevisionId": "********-****-****-****-************",
    "MemorySize": 128,
    "Environment": {
        "Variables": {
            "kmsEncryptedHookUrl": "hooks.slack.com/services/*********/*********/************************",
            "slackChannel": "notification_aws",
            "HookUrl": "hooks.slack.com/services/*********/*********/************************"
        }
    },
    "Version": "$LATEST",
    "Role": "arn:aws:iam::**********:role/hogehoge-lambda-cli-role",
    "Timeout": 10,
    "Runtime": "python3.7",
    "TracingConfig": {
        "Mode": "PassThrough"
    },
    "CodeSha256": "hF1HoyoxurekUvEtl4+Hd5FRuGNvJx7tES1tiUV3jt8=",
    "Description": "An Amazon SNS trigger that sends CloudWatch alarm notifications to Slack.",
    "VpcConfig": {
        "SubnetIds": [],
        "VpcId": "",
        "SecurityGroupIds": []
    },
    "CodeSize": 969,
    "FunctionArn": "arn:aws:lambda:ap-northeast-1:************:function:cloudwatch-alarm-to-slack-python",
    "Handler": "lambda_function.lambda_handler"
}
 </pre>


<p>これで関数が更新された。</p>

<h3>まとめ</h3>


<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/aws">aws</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/cli">cli</a>を使って<a class="keyword" href="http://d.hatena.ne.jp/keyword/Python">Python</a>の<a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a> Lambda関数を更新するやり方について書いた。
ライブラリを使ってなければ単純にzip化して<a class="keyword" href="http://d.hatena.ne.jp/keyword/aws">aws</a> <a class="keyword" href="http://d.hatena.ne.jp/keyword/cli">cli</a>でLambda関数をアップロードすれば良い。</p>

<p><a class="keyword" href="http://d.hatena.ne.jp/keyword/AWS">AWS</a>のcode系サービスを使ってCI/CDをうまく組みたいが、現状、codedeployでは、Lambdaに対しては新しいバージョンへの<a class="keyword" href="http://d.hatena.ne.jp/keyword/%A5%C8%A5%E9%A5%D5%A5%A3%A5%C3%A5%AF">トラフィック</a>の制御をしながらの移行はできるがデプロイはできないみたいなのでどうするのが良いか考えたい。</p>

<ul>
    <li><a target="_brank" rel="noopener noreferrer" href="https://docs.aws.amazon.com/ja_jp/codedeploy/latest/userguide/welcome.html#compute-platform">CodeDeploy とは - AWS CodeDeploy</a></li>
</ul>

</body>
