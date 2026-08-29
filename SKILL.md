---
name: yt2gdrive
description: "Sync latest audio or video (mp3/mp4) from YouTube channels to Google Drive."
---

# yt2gdrive — YouTube 频道 → Google Drive 媒体同步

把 YouTube 频道的更新自动下载为音频（mp3）或视频（mp4）并上传到 Google Drive（rclone remote），适合播客式追更或离线收藏。

## 依赖
- `yt-dlp`、`rclone`、`ffmpeg`（macOS: `brew install yt-dlp rclone ffmpeg`）
- 已配置好的 rclone remote（默认名 `gdrive`；`rclone config` 配 Google Drive）

## 工作流
1. 编辑 `channels.conf`：每行一个频道 URL（`#` 开头为注释）
2. 运行 `scripts/yt2gdrive.sh`（默认下载音频 mp3）
3. 可选：`MEDIA_MODE=video ./scripts/yt2gdrive.sh` 下载视频 mp4
4. 可选：`scripts/install_launchd.sh` 装 macOS 每日定时任务（默认 07:30）

## 配置（环境变量可覆盖）
- `MEDIA_MODE` 音频/视频：`audio`（默认 mp3）或 `video`（mp4）
- `CHANNELS_FILE` 频道列表（默认 `~/.config/yt2gdrive/channels.conf`）
- `ARCHIVE_FILE` 下载归档，去重（默认 `~/.config/yt2gdrive/archive.txt`）
- `LOCAL_DIR` 本地暂存（默认 `~/yt-uploads`）
- `REMOTE_NAME` rclone remote（默认 `gdrive`）
- `REMOTE_PATH` Drive 目标目录（默认 `油管`）
- `RECENT_DAYS` 只下载最近 N 天（默认 3，防止回捞旧视频）
- `AUDIO_FORMAT`/`AUDIO_QUALITY` mp3 / 0（最佳）
- `VIDEO_FORMAT` 视频格式选择器（默认最佳 mp4）
- `MAX_FILESIZE` 视频单文件上限（默认 2G）
- `MAX_DURATION` 跳过超长视频（默认 7200 秒=2 小时）

## 行为要点
- `MEDIA_MODE=audio`（默认）：转 mp3 音频；`MEDIA_MODE=video`：下载 mp4 原画
- 跳过时长 ≥ 2 小时的视频（`MAX_DURATION` 可调）
- `--dateafter` + `--break-on-reject`：只追最近更新，不回捞历史
- `--download-archive` 去重，不重复下载
- 上传 `--bwlimit 8.5M` 限速，不占满带宽
- 自动清理本地 N 天前的文件（`--keep-local` 可保留）
- 频道名带尾随空格时自动修正目录名

## 排错
- 日志：`~/.config/yt2gdrive/last.log`
- `--dry-run` 预演不实际下载/上传
