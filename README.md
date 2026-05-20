# VS Code の Codex（GitHub Copilot Chat / Codex CLI）向け圧縮指示

長い会話セッションを圧縮させるための実用的なプロンプト例です。

## 基本パターン（日本語）
```text
これまでの会話を以下の形式で圧縮要約してください。
以降の会話はこの要約を前提に進めます。
# 圧縮ルール
- 冗長な説明・挨拶・思考過程は削除
- コードは最終版のみ残す（試行錯誤の中間版は破棄）
- ファイル名・関数名・変数名は原文のまま保持
- 決定事項と未解決事項を分けて記載
# 出力形式
## プロジェクト概要
## 現在のファイル構成
## 決定事項（採用した技術・設計）
## 未解決の課題
## 直近のコード（最終版のみ）
```

## 英語版（Codex に推奨）
英語のほうがトークン効率が良く、Codex も英語学習データが多いため精度が上がります。
```text
Compress our conversation so far into a compact context.
I will use this summary as the base for our next turns.
# Rules
- Drop greetings, reasoning steps, and discarded code
- Keep only the FINAL version of any code
- Preserve exact filenames, function names, variable names, paths
- Separate "decided" vs "open questions"
- Use bullet points, no prose
# Output sections
## Project Goal
## Stack & Versions
## File Tree (current)
## Key Decisions
## Open Issues / TODO
## Latest Code (final only, with filenames)
```

## さらに強力な「ハンドオフ」用プロンプト
新しいセッションに引き継ぐ前提の指示です。
```text
Act as if you are writing a handoff document for another AI
that has zero context. Output ONLY the minimum information
needed to continue this work without asking me questions.
Format:
- SYSTEM_CONTEXT: (1-2 lines)
- CONSTRAINTS: (bullets)
- CURRENT_STATE: (what's done)
- NEXT_STEP: (what to do next, concrete)
- CODE_ARTIFACTS: (final files only, in code blocks)
No explanations, no apologies, no meta commentary.
```

## Codex CLI 特有のコツ
VS Code の Codex（特に Codex CLI / `codex` コマンド）の場合:

| テクニック | 効果 |
|-----------|------|
| `/compact` コマンド（対応版なら） | 組み込みの圧縮機能 |
| `AGENTS.md` に方針を書く | 毎回の説明が不要になる |
| 圧縮後に `/clear` → 要約を貼り直す | コンテキスト枠をリセット |
| ファイルに要約を保存させる | `save summary to .codex/context.md` |

## 運用フロー例
```text
1. 作業がひと段落したら：
   "Summarize this session to .codex/session-YYYYMMDD.md
    using the handoff format above."
2. 新セッション開始時：
   "Read .codex/session-YYYYMMDD.md and continue from NEXT_STEP."
```

## 圧縮を促す短縮プロンプト（毎回使える）
```text
TL;DR this thread. Code: final only. No prose.
```

```text
Compress to handoff doc. Filenames preserved. Decisions + TODO only.
```

---

**ポイント**: Codex は「**何を残し、何を捨てるか**」を明示すると圧縮精度が大きく上がります。「短くして」だけだと重要なファイルパスや決定事項まで失われがちです。

具体的にどんな作業をしていて圧縮したいか教えてくれれば、そのケース専用のプロンプトも作れますよ。