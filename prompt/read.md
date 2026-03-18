
hãy cho tôi biết các file này nghĩa là gì? nếu tôi muốn xây dựng một workflow làm việc cho openclaw thì nên làm những gì? ở file nào? bên cạnh đó tôi muốn openclaw không chỉ là 1 AI agents, nó có thể là nhiều AI agents làm nhiều tác vụ khác nhau giúp tôi. mỗi cái cần một sự chuyên biệt riêng. Vậy tôi cần làm gì?

tôi đã sử dụng 2 lệnh sau để đưa openclaw sang ổ cứng rời:
```
mv ~/.openclaw/workspace /Volumes/samsung_1tb/openclaw/
ln -s /Volumes/samsung_1tb/openclaw/workspace ~/.openclaw/workspace
```

sau khi đưa sang tôi thấy có các file sau:
```
hieu@hieus-MacBook-Pro openclaw % tree
.
└── workspace
    ├── AGENTS.md
    ├── BOOTSTRAP.md
    ├── HEARTBEAT.md
    ├── IDENTITY.md
    ├── SOUL.md
    ├── TOOLS.md
    └── USER.md
```
