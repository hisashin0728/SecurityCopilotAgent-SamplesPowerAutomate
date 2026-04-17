# SecurityCopilotAgent-SamplesPowerAutomate
Security Copilot カスタムエージェントを PowerAutomate 経由で呼び出すサンプル集

# 設定方法
## 1.Secuerity Copilot プラグインとして登録する
- Microsoft Security Copilot を開き、プラグインとしてアップロードをします
- <img width="1346" height="174" alt="image" src="https://github.com/user-attachments/assets/4aadbb01-be9e-4f26-a77f-c78aa9767efc" />
- 「ソースの管理」より、「プラグインのアップロード」を選択してください
- <img width="1071" height="108" alt="image" src="https://github.com/user-attachments/assets/923265c7-2be2-4bd8-8d5d-2ed5ab935832" />
- プラグインを利用する対象ユーザー（自分のみ or ワークスペース内の全ユーザー）を選択し、「Copilot for Security ポータル」を選んで、YAML ファイルをアップロードしてください。
 - [WeeklyDefenderIncidentReport](https://github.com/hisashin0728/SecurityCopilotAgent-SamplesPowerAutomate/blob/main/WeeklyDefenderIncidentReport/WeeklyDefenderIncidentReport_md.yaml)
 - [WeeklyEntraIdRiskUserAnalyticsReport](https://github.com/hisashin0728/SecurityCopilotAgent-SamplesPowerAutomate/blob/main/WeeklyEntraIdRiskUserAnalyticsReport/WeeklyEntraIdRiskUserAnalyticsReport_md.yaml)
 - [WeeklyIntuneStatusReport](https://github.com/hisashin0728/SecurityCopilotAgent-SamplesPowerAutomate/blob/main/WeeklyDefenderIncidentReport/WeeklyDefenderIncidentReport_md.yaml)

## 2.エージェントの認証設定を行う
- プラグイン画面から YAML ファイルをアップロードすると、カスタムエージェントが見えてきますが、このエージェントが用いる認証権限を渡す必要があります
- 2026.4 現在、カスタムエージェントは Entra Agent ID (IDベースの認証）に未対応となっており、OAuth 認証で渡したユーザー権限で動作します
- 「エージェント」画面から、指定のカスタムエージェントを開き、適切なユーザー権限で認証してください
- 認証が終わると、エージェントが使えるようになります

## 3.エージェントのテスト
- エージェントがインストールされると、スキル呼び出し or エージェントから手動で実行させることができます
- 「WeeklyDefenderIncidentReport」エージェントの場合、以下のような画面が見えます
 - <img width="800" alt="image" src="https://github.com/user-attachments/assets/2872eeed-2d39-4a1a-a9ba-c695efc92405" />
- エージェントはプロンプトから直接指定で呼び出すことができます
 - ``/WeeklyDefenderIncidentReportMdAgent``
 - <img width="1419" height="933" alt="image" src="https://github.com/user-attachments/assets/18153350-950e-40c0-ac6d-1d3c37b480b1" />

## 4.PowerAutomate の実装
- プロンプトで Custom Agent を実行できることを確認できたら、PowerAutomate のフローを作成してください
- 以下例です
 -  <img width="596" height="804" alt="image" src="https://github.com/user-attachments/assets/513ed134-5362-49fc-bceb-66c95e6f5d07" />
 - ``Recurrence``
  - スケジュールで定期的に実行するように設定
  - スケジュール間隔はお好みで 
 - ``SecurityCopilotPrompt``
  - PowerAutomate のコネクタで、Security Copilot のプラグインが用意されています
  - プロンプトで ``WeeklyDefenderIncidentReportMdAgent`` を入れてください
 - ``Compose``
  - オプションです
  - もし、Email 通知時にマークダウン生成結果を添付ではなく、表示させたい場合は以下を用いてください

```json
concat(
'<html>
<head>
<style>
pre {
    font-family: Consolas, "Courier New", monospace;
    font-size: 12px;
    background-color: #f6f8fa;
    padding: 12px;
    border-radius: 6px;
    white-space: pre-wrap;
}
</style>
</head>
<body>
<pre>',
outputs('SecurityCopilotprompt')?['body/EvaluationResultContent'],
'</pre>
</body>
</html>'
)
```
  
 - ``SendEmail``
  - 指定先にメール通知してください
  - メール本体（BODY）を上記 ``Compose`` 結果、添付ファイルに Security Copilot の Output を入れるのが良いと思います
