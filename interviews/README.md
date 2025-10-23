# 技術質問・相談記録

## 目的
- どんな質問があり自分はどう答えたかを残す
- 正誤の評価と、何がどう間違っていたか/正解は何かを明確化
- 正解パターンをどう習得したか（`learning` の該当ノートと紐づけ）

## ディレクトリ
- [[inbox]]: まずはここに全て集める（outcome: pending）
- [[done]]: 学習・再検証後に解決（status: resolved）

## 最近の更新
```dataview
LIST
FROM "interviews"
WHERE file.name != "README.md"
SORT file.mtime DESC
LIMIT 10
```

## まずはここに入れる → [[inbox]]
```dataview
LIST
FROM "interviews/inbox"
WHERE outcome = "pending" OR !outcome
SORT file.ctime DESC
LIMIT 10
```

## 進捗ビュー
```dataview
TABLE file.ctime as "📅 作成", outcome, status, learned_refs
FROM "interviews"
WHERE file.name != "README.md"
SORT file.mtime DESC
LIMIT 20
```

## 未解決（要フォロー）
```dataview
LIST
FROM "interviews"
WHERE (status != "resolved" OR !status) AND (outcome != "pending" OR outcome)
SORT file.mtime DESC
```

## 学習リンク未設定（要リンク）
```dataview
LIST
FROM "interviews"
WHERE file.name != "README.md"
AND (learned_refs = [] OR !learned_refs)
SORT file.mtime DESC
```

## 運用ルール
1. 1質問1ファイル。テンプレートで作成し冒頭の項目を埋める（下記）。
2. 誤答は「何がどう間違っていたか」と「正解」を必ず記録。
3. 学習が完了したら `learned_refs` に `learning` ノートを追加し `status: resolved` に更新。

## テンプレート（失敗/不完全）
```markdown
---
type: interview | peer | code-review
tech: ["AWS","SQS"]
topic: "SQSの一貫性"
outcome: incorrect | partial
status: open | resolved
confidence: low | medium | high
learned_refs: []   # 例: ["[[learning/AWS/SQS/2024-01-16-sqs-dlq]]"]
reviewed_on:
---

# {技術名} 質問 - {日付}

## 質問内容
-

## 自分の回答（当時）
-

## 評価（正誤）
- 結果: incorrect | partial
- 何がどう間違っていたか:
-
- 正解（要点）:
-

## 次のアクション
- [ ] 資料を読む
- [ ] 手を動かす
- [ ] 先輩に再確認

## 学習リンク（学んだ記録）
- （`learned_refs` にも同じリンクを入れる）
```

## テンプレート（成功/再現OK）
```markdown
---
type: interview | peer | code-review
tech: ["React","Hooks"]
topic: "useEffect依存配列"
outcome: correct
status: resolved
confidence: medium | high
learned_refs: []   # 例: ["[[learning/React/Hooks/2024-01-20-hooks-deps]]"]
reviewed_on:
---

# {技術名} 質問 - {日付}

## 質問内容
-

## 自分の回答（当時）
-

## 評価（正誤）
- 結果: correct
- 補足（より良い説明や追加論点）:
-

## 学習リンク（根拠・補強）
-
```
```

### interviews/failed-questions/README.md（置き換え）
```markdown
# 答えられなかった/不完全だった質問

## 未解決（優先対応）
```dataview
LIST
FROM "interviews/failed-questions"
WHERE status != "resolved" OR !status
SORT file.mtime DESC
```

## 技術別の未解決
```dataview
TABLE topic, outcome, status
FROM "interviews/failed-questions"
WHERE status != "resolved" OR !status
FLATTEN tech
GROUP BY tech
SORT rows.file.mtime DESC
```

## 学習リンク未設定
```dataview
LIST
FROM "interviews/failed-questions"
WHERE (learned_refs = [] OR !learned_refs)
SORT file.mtime DESC
```

## 最近の更新
```dataview
LIST
FROM "interviews/failed-questions"
SORT file.mtime DESC
LIMIT 10
```
```

### interviews/answered-questions/README.md（置き換え）
```markdown
# 正しく答えられた/再現できた質問

## 最新（正解/再現OK）
```dataview
TABLE file.ctime as "📅 作成", topic as "トピック", tech as "技術"
FROM "interviews/answered-questions"
SORT file.ctime DESC
LIMIT 10
```

## 学習リンク付き（根拠あり）
```dataview
TABLE learned_refs as "根拠/補強学習", confidence as "自信度"
FROM "interviews/answered-questions"
WHERE learned_refs AND length(learned_refs) > 0
SORT file.mtime DESC
```

## 自信度が低いもの（要補強）
```dataview
LIST
FROM "interviews/answered-questions"
WHERE confidence = "low" OR !confidence
SORT file.mtime DESC
```
```

### テンプレート（ファイル）置き換え案
- `interviews/templates/failed-question-template.md`
```markdown
---
type: interview | peer | code-review
tech: []
topic:
outcome: incorrect | partial
status: open
confidence: low | medium | high
learned_refs: []
reviewed_on:
---

# {技術名} 質問 - {日付}

## 質問内容
-

## 自分の回答（当時）
-

## 評価（正誤）
- 結果: incorrect | partial
- 間違いの要因:
-
- 正解（要点）:
-

## 次のアクション
- [ ] 資料を読む（具体名）
- [ ] 演習する（何を）
- [ ] 再確認（誰に/いつ）

## 学習リンク（学んだ記録）
- （`learned_refs` に同じリンクも入れる）
```

- `interviews/templates/answered-question-template.md`
```markdown
---
type: interview | peer | code-review
tech: []
topic:
outcome: correct
status: resolved
confidence: medium | high
learned_refs: []
reviewed_on:
---

# {技術名} 質問 - {日付}

## 質問内容
-

## 自分の回答（当時）
-

## 評価（正誤）
- 結果: correct
- 補足/改善余地:
-

## 学習リンク（根拠・補強）
-
```

補足
- 既存ノートは上記フロントマターのキーだけ追加すればそのまま使えます（最低限: `outcome`, `status`, `learned_refs`）。
- `learned_refs` には `[[learning/...]]` のwikiリンクを入れてください（Dataviewが拾えます）。
- カウント集計はDataviewの集約仕様差があるため、まずは「リスト/テーブル＋フィルタ」で実用性を優先しています。必要なら後で集約ビューも追加できます。