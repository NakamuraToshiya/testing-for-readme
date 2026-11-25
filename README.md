```mermaid
flowchart TD
    subgraph Flags["システムフラグ状態"]
        F1["メンテナンスモード: OFF"]
        F2["セキュリティチェック: ON"]
    end

    Start(["ジョブ受信"]) --> MCheck{"メンテナンス中？"}
    F1 -.-> MCheck
    MCheck -->|Yes| EndMaint(["処理中断"])
    MCheck -->|No| SCheck{"セキュリティ検査必要？"}
    F2 -.-> SCheck
    SCheck -->|Yes| Security["セキュリティ検査"]
    SCheck -->|No| Main

    Security --> Main["メイン処理実行"]
    Main --> Result{"実行結果判定"}

    Result -->|SUCCESS| Done(["完了"])
    Result -->|FATAL_ERROR| Alert(["アラート通知"])
    
    Result -->|RETRYABLE_ERROR| Retry{"再試行回数 < 3 ?"}
    Retry -->|Yes| Requeue(["SQSへ再投入"])
    Requeue --> Main
    Retry -->|No| DLQ(["DLQへ退避"])
