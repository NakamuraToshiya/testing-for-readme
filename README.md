flowchart LR
    classDef gate fill:#eef4ff,stroke:#2b5c8f,stroke-width:2px,color:#111;
    classDef signal fill:#f8f9fa,stroke:#555,stroke-width:1.5px,color:#111;

    A["条件A\n(入力)"]:::signal --> G_AND{{"& (AND)\n両方成立"}}:::gate
    B["条件B\n(入力)"]:::signal --> G_AND

    C["条件C\n(入力)"]:::signal --> G_OR{{"≥1 (OR)\nいずれか成立"}}:::gate
    G_AND --> G_OR

    G_OR --> G_NOT["¬ (NOT)\n反転"]:::gate
    G_NOT --> OUT["出力アクション"]:::signal
