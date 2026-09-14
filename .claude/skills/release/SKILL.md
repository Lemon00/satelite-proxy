---
name: release
description: 递增版本号、生成更新摘要、推送 tag 触发发布 CI。当用户说"发布"时使用。
---

# 发布流程

触发词：用户说"发布"。按以下步骤顺序执行，不要跳步、不要询问确认（除非发现异常）。

## 1. 递增版本号

- 只有一个版本源：`package.json` 的 `version` 字段（`src-tauri/tauri.conf.json` 通过 `"version": "../package.json"` 引用它，不用改）。
- 默认 patch +1（`1.0.33` → `1.0.34`）。
- 用 Edit 直接修改 `package.json`，不要用 `npm version`（避免它自动创建 git tag/commit）。

## 2. 生成更新摘要

- 用 `git log <上一个tag>..HEAD --oneline` 和关键 commit 的 diff 提炼本次改动。
- 严格按 `AGENTS.md` 中「更新说明输出模板」的固定格式输出摘要（4–5 条要点 + 固定下载指南 + macOS 隔离提示），不要自创格式。
- 把生成的摘要展示给用户确认内容准确（不是确认要不要发，只是过一眼摘要对不对）。

## 3. 提交 + 打 tag + 推送

```bash
git add package.json
git commit -m "chore(release): bump version to vX.Y.Z"
git tag vX.Y.Z
git push origin main
git push origin vX.Y.Z
```

- tag 格式必须是 `v` 前缀（`.github/workflows/release.yml` 的触发条件是 `tags: ["v*"]`）。
- commit message 不加 Co-Authored-By（项目规则）。
- 推送后到此结束——CI（GitHub Actions `Release` workflow）会自动打四平台安装包并建 GitHub Release，不在本流程内手动创建/编辑 Release。

## 完成后回报

简短告知：新版本号、tag 是否推送成功、CI workflow 链接（`gh run list --workflow=release.yml -L 1` 或直接提示去 Actions 页面看）。
