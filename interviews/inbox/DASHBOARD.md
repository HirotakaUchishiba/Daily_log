## 受けた質問
```dataview
TABLE file.ctime as "📅 作成", topic as "トピック", tech as "技術"
FROM "interviews/inbox"
WHERE outcome = "pending" OR !outcome
SORT file.ctime DESC
```
