# Git的使用

## 从命令行创建一个仓库

```bash
echo "# Notes" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:AutMaple/Notes.git
git push -u origin main
```

## 推送内容到一个仓库

```bash
git remote add origin git@github.com:AutMaple/Notes.git
git branch -M main
git push -u origin main
```

