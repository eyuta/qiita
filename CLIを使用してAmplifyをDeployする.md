# CLI を使用して Amplify を Deploy する

## Version

```sh
AWS CLI: 2.0.40 (1系でも動作する)
```

## 概要

[start-job](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/amplify/start-job.html) を使用します

```sh
$ aws amplify start-job \
  --app-id $APP_ID \
  --branch-name $BRANCH_NAME \
  --job-type $JOB_TYPE

```

## 詳細

### Optionについて

※詳細については公式ドキュメントを参照してください

- --app-id
    - Amplify Consoleから取得する場合
        1. `All apps > App Name`
        2. URL の一番うしろに `APP_ID` がついている (d1xxxxxxxxxxxx)
          ex: <https://ap-northeast-1.console.aws.amazon.com/amplify/home?region=ap-northeast-1#/d1xxxxxxxxxxxx>
    - CLIから取得する場合
        - [list-apps](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/amplify/list-apps.html)を使用する
            ```sh
            $ aws amplify list-apps
            {
                "apps": [
                    {
                        "appId": "d1xxxxxxxxxxxx",
                        "appArn": "arn:aws:amplify:ap-northeast-1:1234567890:apps/d1xxxxxxxxxxxx",
                        "name": "hoge",
                        "tags": {},
                        "platform": "WEB",
                        "createTime": "2020-07-26T22:00:38.677000+09:00",
                        "updateTime": "2020-07-26T22:00:38.677000+09:00",
                        "environmentVariables": {
                            "_LIVE_PACKAGE_UPDATES": "[{\"pkg\":\"@aws-amplify/cli\",\"type\":\"npm\",\"version\":\"latest\"}]"
                        },
                        "defaultDomain": "d1xxxxxxxxxxxx.amplifyapp.com",
                        "enableBranchAutoBuild": false,
                        "enableBranchAutoDeletion": false,
                        "enableBasicAuth": false,
                        "customRules": [],
                        "enableAutoBranchCreation": false
                    },
            }
            ```
- --branch-name
    - デプロイ対象のブランチ名
- --job-type
    - 以下の値を指定する
        - `RELEASE`
            - 最新のソースでDeployする場合に使用する
        - `RETRY`
            - 既存のjob を再実行する場合に使用する
            - 別途 `--job-id` を指定する必要がある。
                - Amplify Consoleから取得する場合
                    1. `All apps > App Name > Branch Name`
                    2. Build 〇〇の数字が `--job-id` にあたる
                      ![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/37c37475-bd85-9528-ff68-26a3cf0b052f.png)
                - CLIから取得する場合
                    - [list-jobs](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/amplify/list-jobs.html) を使用することで特定のアプリのjobの一覧を取得できる
                        ```sh
                       $ aws amplify list-jobs \
                          --app-id $APP_ID \
                          --branch-name $BRANCH_NAME
                        {
                          "jobSummaries": [
                              {
                                  "jobArn": "arn:aws:amplify:ap-northeast-1:123456789:apps/dxxxxxxxxxxxxx/branches/hogehoge/jobs/0000000230",
                                  "jobId": "10",
                                  "commitId": "HEAD",
                                  "commitTime": "2020-08-14T12:52:03.156000+09:00",
                                  "startTime": "2020-08-14T12:52:03.978000+09:00",
                                  "status": "SUCCEED",
                                  "endTime": "2020-08-14T13:00:03+09:00"
                              },
                              ...
                      }
                        ```
        - `WEB_HOOK`
            - web hook 経由でDeployする場合に使用する
        - `MANUAL`
            - 不明

## 最後に

自動ビルドを使わず、CodePipelineからAmplifyをデプロイするために使用しました
蛇足になりますが、 `start-job` を使用する場合は  `All apps > App Name > General` のブランチのAuto-Buildを切る必要があります
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/110860/860b49b2-3667-68ff-dd96-0deed3544800.png)
