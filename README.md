# yt2gdrive

把 YouTube 频道的更新自动下载为 mp3 并上传到 Google Drive，适合播客式追更（通勤听、跑步听）。

## 特性

- 🎧 自动下载 mp3（最佳音质），按频道分目录
- ☁️ rclone 上传 Google Drive，带宽限速不占满网
- ⏱️ 只追最近 N 天的更新（默认 3 天），不会回捞频道历史视频
- 🧠 归档去重（`archive.txt`），不重复下载
- ⏰ macOS launchd 每日定时（默认 07:30），开机自启
- 🧹 自动清理本地旧文件

## 依赖

- [yt-dlp](https://github.com/yt-dlp/yt-dlp)
- [rclone](https://rclone.org/)（配好 Google Drive remote，默认名 `gdrive`）
- ffmpeg

macOS 一行安装：

```bash
brew install yt-dlp rclone ffmpeg
rclone config   # 添加 gdrive remote
```

## 快速开始

```bash
# 1. 准备频道列表
cp channels.conf.example ~/.config/yt2gdrive/channels.conf
vim ~/.config/yt2gdrive/channels.conf   # 每行一个频道

# 2. 手动跑一次
./scripts/yt2gdrive.sh

# 3. （可选）装每日定时任务，每天 07:30 自动同步
./scripts/install_launchd.sh
```

## 配置

全部配置可用环境变量覆盖，无需改脚本：

| 变量 | 默认值 | 说明 |
|---|---|---|
| `CHANNELS_FILE` | `~/.config/yt2gdrive/channels.conf` | 频道列表 |
| `ARCHIVE_FILE` | `~/.config/yt2gdrive/archive.txt` | 下载归档（去重） |
| `LOCAL_DIR` | `~/yt-uploads` | 本地暂存目录 |
| `REMOTE_NAME` | `gdrive` | rclone remote 名 |
| `REMOTE_PATH` | `油管` | Drive 目标目录 |
| `RECENT_DAYS` | `3` | 只下载最近 N 天 |
| `AUDIO_FORMAT` | `mp3` | 音频格式 |
| `AUDIO_QUALITY` | `0` | 0=最佳 |
| `BWLIMIT` | `8.5M` | 上传限速 |
| `CLEANUP_DAYS` | `3` | 本地保留天数 |

## 参数

- `--dry-run`：预演，不实际下载/上传
- `--keep-local`：保留本地文件不清理

## 文件命名

```
<上传日期 YYYYMMDD> - <标题前120字符>.mp3
```

按上传者（频道名）分目录存放。

## 排错

- 日志：`~/.config/yt2gdrive/last.log`
- 停用定时任务：`launchctl unload ~/Library/LaunchAgents/com.user.yt2gdrive.plist`

## License

MIT
