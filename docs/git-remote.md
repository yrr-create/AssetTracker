# Git Remote Setup

当前本地仓库路径：

```text
G:\Personalportfolio\LocationGET
```

## 当前状态

- 本机有 Git。
- 本机没有检测到 GitHub CLI `gh`。
- 当前环境没有检测到 `GITHUB_TOKEN` 或 `GH_TOKEN`。

因此我可以建立本地 Git 仓库并提交初始文档，但不能在没有账号凭据的情况下直接替你创建远程仓库。

## 方案 A：GitHub 网页创建远程仓库

1. 打开 GitHub，新建仓库：

```text
LocationGET
```

2. 不要勾选 Initialize with README，因为本地已经有 README。

3. 创建后在本地执行：

```bash
cd /d G:\Personalportfolio\LocationGET
git remote add origin https://github.com/<你的用户名>/LocationGET.git
git branch -M main
git push -u origin main
```

## 方案 B：安装 GitHub CLI 后创建

```powershell
scoop install gh
gh auth login
cd /d G:\Personalportfolio\LocationGET
gh repo create LocationGET --private --source . --remote origin --push
```

如果你希望公开仓库，把 `--private` 改成 `--public`。

## 方案 C：Gitee 或其他平台

在平台网页创建空仓库后，执行：

```bash
cd /d G:\Personalportfolio\LocationGET
git remote add origin <远程仓库 HTTPS 或 SSH 地址>
git branch -M main
git push -u origin main
```
