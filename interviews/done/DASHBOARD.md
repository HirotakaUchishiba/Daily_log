# 解決・定着済み
```dataview
TABLE file.ctime as "📅 作成", topic as "トピック", tech as "技術"
FROM "interviews"
WHERE file.name != "README.md" AND status = "resolved"
SORT file.ctime DESC
LIMIT 10
```

## 根拠リンク付き（学習で裏取り済み）
```dataview
TABLE learned_refs as "根拠/補強学習", confidence as "自信度"
FROM "interviews"
WHERE status = "resolved" AND learned_refs AND length(learned_refs) > 0
SORT file.mtime DESC
```

## 自信度が低い（継続ウォッチ）
```dataview
LIST
FROM "interviews"
WHERE status = "resolved" AND (confidence = "low" OR !confidence)
SORT file.mtime DESC
```
