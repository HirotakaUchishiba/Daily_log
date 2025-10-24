# 技術質問・相談記録

## 目的
- どんな質問があり自分はどう答えたかを残す（Inbox）
- 何がどう間違っていたか/正解は何か、どう学んで解に至ったかを記す（Done）
- `learning` の該当ノートと紐づけ（根拠の明示）

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
TABLE file.ctime as "📅 作成", topic as "トピック", tech as "技術", outcome, status, learned_refs
FROM "interviews"
WHERE file.name != "README.md"
SORT file.mtime DESC
LIMIT 20
```

## 未解決（要フォロー）
```dataview
LIST
FROM "interviews"
WHERE outcome != "pending" AND (status != "resolved" OR !status)
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
1. Inboxノートには「質問内容・当時の口頭回答・課題・調査プラン」までを書く
2. 解が固まったら Done として「何が間違い/正解/学びの経路/根拠リンク」を追記し、`status: resolved` と `learned_refs` を設定
3. 物理移動は任意（表示は Dataview が outcome/status で集約）