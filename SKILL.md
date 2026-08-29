---
name: yt2gdrive
description: "Sync latest audio from YouTube channels to Google Drive as mp3."
---

# yt2gdrive — YouTube 频道 → Google Drive 音频同步

把 YouTube 频道的更新自动下载为 mp3 并上传到 Google Drive（rclone remote），适合播客式追更。

## 依赖
- `yt-dlp`、`rclone`、`ffmpeg`（macOS: `brew install yt-dlp rclone ffmpeg`）
- 已配置好的 rclone remote（默认名 `gdrive`；`rclone config` 配 Google Drive）

## 工作流
1. 编辑 `channels.conf`：每行一个频道 URL（`#` 开头为注释）
2. 运行 `scripts/yt2gdrive.sh`
3. 可选：`scripts/install_launchd.sh` 装 macOS 每日定时任务（默认 07:30）

## 配置（环境变量可覆盖）
- `CHANNELS_FILE` 频道列表（默认 `~/.config/yt2gdrive/channels.conf`）
- `ARCHIVE_FILE` 下载归档，去重（默认 `~/.config/yt2gdrive/archive.txt`）
- `LOCAL_DIR` 本地暂存（默认 `~/yt-uploads`）
- `REMOTE_NAME` rclone remote（默认 `gdrive`）
- `REMOTE_PATH` Drive 目标目录（默认 `油管`）
- `RECENT_DAYS` 只下载最近 N 天（默认 3，防止回捞旧视频）
- `AUDIO_FORMAT`/`AUDIO_QUALITY` mp3 / 0（最佳）

## 行为要点
- 跳过时长 ≥ 2 小时的视频
- `--dateafter` + `--break-on-reject`：只追最近更新，不回捞历史
- `--download-archive` 去重，不重复下载
- 上传 `--bwlimit 8.5M` 限速，不占满带宽
- 自动清理本地 N 天前的文件（`--keep-local` 可保留）
- 频道名带尾随空格时自动修正目录名

## 排错
- 日志：`~/.config/yt2gdrive/last.log`
- `--dry-run` 预演不实际下载/上传
