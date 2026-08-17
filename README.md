```mermaid
sequenceDiagram
    autonumber
    participant Client as Client PC / Browser
    participant WebApp as Web App (MCP Client)
    participant LLM as Ollama (Llama 3.2)
    participant MCPServer as MCP Server (svc-mcp)
    participant External as SaaS / Storage

    Note over WebApp,MCPServer: 1. ツールの事前取得 (起動時/リクエスト前)
    WebApp->>MCPServer: tools/list
    MCPServer-->>WebApp: ツール定義一覧 (JSON Schema)

    Note over Client,External: 2. ユーザー要求とツールの選択
    Client->>WebApp: ユーザープロンプト送信
    WebApp->>LLM: プロンプト + ツール定義一覧
    Note over LLM: 3. LLMによる推論・ツール選択
    LLM-->>WebApp: ツール実行要求 (JSON)

    Note over Client,External: 4. ツールの実行とレスポンス
    WebApp->>MCPServer: tools/call (SSE / JSON)
    MCPServer->>External: データ操作 / API呼出
    External-->>MCPServer: 結果返却
    MCPServer-->>WebApp: 実行結果返却

    Note over Client,External: 5. 最終回答の生成
    WebApp->>LLM: プロンプト + 実行結果
    LLM-->>WebApp: 最終回答 (自然言語)
    WebApp-->>Client: 画面表示
