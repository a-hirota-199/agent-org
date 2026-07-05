# agent-org — 移植可能なエージェント組織

ファイル一式で持ち運べる「エージェントの組織」。clone して起動コマンド1行で、上司（manager）＋部下（lead／worker）の組織がどの環境でも立ち上がる。

## 起動方法

```bash
git clone <このリポジトリ> && cd agent-org
cp .env.example .env       # 必要な認証情報があれば設定
claude --agent manager     # 上司をメインセッションとして起動
```

## 組織構成（標準3層）

```
manager（上司。--agent で起動）
├── lead（中間管理職。worker を束ねる）
│   └── worker ×N（実働。同一定義から並列起動）
└── worker（小案件では manager 直轄の2層に縮退）
```

manager は案件の性質に応じて動員パターン（fan-out並列／パイプライン／レビュー役付き／競作）を選択し、宣言してから実行する。ユーザはお題を渡すだけでよい。

## ディレクトリ構成

| パス | 組織での役割 |
|---|---|
| `CLAUDE.md` | 就業規則（行動規範・情報共有規約） |
| `.claude/agents/` | 職務記述書（manager／lead／worker） |
| `.claude/skills/` | 業務マニュアル（動員パターン4種＋意思決定の記録） |
| `.claude/workflows/` | 定型業務（運用しながら追加する） |
| `.claude/settings.json` | 権限規程（permissions。業務内容に合わせて調整する） |
| `.mcp.json` | 外部接続の定義（認証は `.env` で注入） |
| `work/` | 案件ごとの成果物置き場（git 管理外） |

## 役割の増やし方

1. `.claude/agents/worker.md` を複製して `worker-<役割>.md` を作り、description と「専門性」の節を書く
2. 同じ役割を複数人で並列させる場合、ファイルは1枚でよい（起動数で人数を調整する）
3. 部下を持たせたい役割にのみ、frontmatter の `tools` に `Agent` を含める（lead 化）

## 設計上の制約（前提）

- サブエージェントの階層は main＋5階層が仕様上限（本組織は3層標準）
- 部下同士は直接通信できない。連携は上司経由、データは `work/` のファイル経由
