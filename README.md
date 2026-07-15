# gaokaomath-archive

本项目是 [`deekur/gaokaomath`](https://github.com/deekur/gaokaomath) 的独立 GitHub 归档仓库，用于持续保存已经同步的历年高考数学真题，降低源仓库删除文件、停止维护或删除仓库带来的数据丢失风险。

## 主要做法

- 使用 GitHub Actions 在 GitHub 托管的临时运行器上执行同步，不把题库下载到本地 NAS。
- 每天北京时间 03:17 自动同步，也支持在 Actions 页面手动触发。
- 使用 `/usr/bin/git` 浅克隆源仓库的 `main` 分支，只获取同步所需的最新内容。
- 使用不带 `--delete` 的 `rsync` 复制新增和更新内容。
- 使用 `git add --ignore-removal` 只暂存新增和修改文件，不暂存源仓库中的删除。
- 提交前检查暂存区；一旦发现删除操作，工作流立即失败并拒绝提交。
- 没有内容变化时不创建空提交；有变化时由 `github-actions[bot]` 自动提交并推送。

## 删除保护

源仓库删除文件后，本项目中已经归档的同路径文件仍会保留。若源仓库无法访问或被删除，同步任务会失败，但不会改动本项目已有数据。

如果源仓库更新了同一路径文件，本项目会同步新版本；旧版本仍可通过本项目的 Git 历史找回。因此，本项目的当前目录是“只增不删、允许更新”的归档快照，而 Git 历史保存历次版本。

## 云端同步流程

1. 检出本归档仓库。
2. 在 GitHub 托管运行器的临时目录中浅克隆源仓库。
3. 排除源仓库的 `.git`、`.github` 和 `README.md`，将其他内容复制到归档仓库。
4. 只暂存新增和修改，确认不存在删除后提交并推送。
5. GitHub Actions 运行结束后销毁临时运行器及其工作目录。

工作流文件位于 [`.github/workflows/sync-gaokaomath.yml`](.github/workflows/sync-gaokaomath.yml)。源仓库为公开仓库，当前同步不需要额外的访问令牌；本工作流仅使用本仓库的 `GITHUB_TOKEN` 推送归档提交。

## 手动同步

打开本仓库的 **Actions** 页面，选择 **Sync Gaokao Math Archive**，点击 **Run workflow** 并选择 `main` 分支即可。

## 来源与许可

题目资料来源于 [`deekur/gaokaomath`](https://github.com/deekur/gaokaomath)。资料的版权与使用条件以源仓库说明及本仓库同步保存的 `LICENSE` 为准。

特别感谢原项目维护者 [`deekur`](https://github.com/deekur) 对历年高考数学真题的长期收集、整理与公开分享。本项目仅提供独立归档与自动同步，不代表原项目，也不取代原项目的维护与说明。
