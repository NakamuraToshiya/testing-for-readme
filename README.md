# testing-for-readme
flowchart TD
    subgraph Flags["フラグ状態"]
        F1["flag_pii_enabled = true"]
        F2["flag_strict_mode = false"]
        F3["flag_retry_exhausted = false"]
    end

    Start(["ジョブ受信"]) --> OR1{"OR判定:\nPII検知 または strict_mode"}
    F1 -.-> OR1
    F2 -.-> OR1

    OR1 -->|Yes| AND1{"AND判定:\nマスキング成功 かつ\nスキーマ検証OK"}
    OR1 -->|No| FastPath["通常処理へスキップ"]

    AND1 -->|Yes| Save["S3へ保存・完了"]
    AND1 -->|No| RetryCheck{"再試行可能？\n(retry_count < max_retry)"}

    RetryCheck -->|Yes| Requeue["SQSへ再投入\n(retry_count + 1)"]
    RetryCheck -->|No| DLQCheck{"AND判定:\nDLQ有効 かつ\nflag_retry_exhausted = true"}
    F3 -.-> DLQCheck

    DLQCheck -->|Yes| ToDLQ["DLQへ退避"]
    DLQCheck -->|No| Alert["エラーログ出力・通知"]
