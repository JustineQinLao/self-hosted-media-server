# Self-Hosted Media Server Stack

[![Docker](https://img.shields.io/badge/Docker-Compose-blue?style=for-the-badge&logo=docker)](https://docker.com)
[![Jellyfin](https://img.shields.io/badge/Jellyfin-Media_Server-00A4DC?style=for-the-badge&logo=jellyfin)](https://jellyfin.org)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)

A comprehensive self-hosted media server stack featuring Jellyfin for media streaming, automated torrent downloading with qBittorrent, YouTube downloading capabilities, and secure remote access via Cloudflare Tunnel.

## 📋 Table of Contents

- [Features](#-features)
- [Architecture](#-architecture)
- [Prerequisites](#-prerequisites)
- [Quick Start](#-quick-start)
- [Configuration](#-configuration)
- [Services](#-services)
- [Usage](#-usage)
- [Troubleshooting](#-troubleshooting)
- [Contributing](#-contributing)
- [License](#-license)

## ✨ Features

- 🎬 **Jellyfin Media Server** - Stream your media library with a beautiful interface
- ⬇️ **qBittorrent** - Automated torrent downloading and management
- 🎵 **YouTube Downloader** - Download videos from YouTube and other platforms
- 🌐 **Nginx Reverse Proxy** - Secure web access with SSL termination
- 🔒 **Cloudflare Tunnel** - Secure remote access without port forwarding
- 🐳 **Docker Compose** - Easy deployment and management
- 📱 **Cross-platform** - Works on Linux, Windows, and macOS
- 🔧 **Environment-based Configuration** - Easy to customize ports and settings

## 🏗️ Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Cloudflare    │────│     Nginx       │────│    Jellyfin     │
│     Tunnel      │    │   (Port 80)     │    │   (Port 8096)   │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                                │
                                ├─────────────────┐
                                │   qBittorrent   │
                                │   (Port 8080)   │
                                └─────────────────┘
                                │
                                ├─────────────────┐
                                │  YT-DLP Web UI  │
                                │   (Port 3033)   │
                                └─────────────────┘
```

## 📋 Prerequisites

Before you begin, ensure you have the following installed:

- **Docker** (version 20.10+)
- **Docker Compose** (version 2.0+)
- **Git** (for cloning the repository)
- **Cloudflare Account** (for tunnel setup)

### System Requirements

- **RAM:** Minimum 4GB, Recommended 8GB+
- **Storage:** SSD recommended for media files
- **Network:** Stable internet connection for downloads

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/JustineQinLao/self-hosted-media-server.git
cd self-hosted-media-server
```

### 2. Configure Environment Variables

```bash
cp .env.example .env
```

Edit `.env` with your actual values:
- Set your Cloudflare tunnel token
- Adjust ports if needed
- Configure media paths

### 3. Start the Stack

**Option 1: Detached Mode (Recommended for Production)**
```bash
docker compose up -d
```

**Option 2: Foreground Mode (For Development/Testing)**
```bash
docker compose up
```
*Use this option to see real-time logs and stop with Ctrl+C*

### 4. Access Your Services

- **Jellyfin:** `http://localhost` (via Nginx reverse proxy)
- **qBittorrent:** `http://localhost:8080` (default credentials: admin/adminadmin)
- **YT-DLP Web UI:** `http://localhost:3033`
- **Remote Access:** Via your Cloudflare tunnel URL

## ⚙️ Configuration

### Environment Variables

| Variable | Description | Default | Required |
|----------|-------------|---------|----------|
| `JELLYFIN_PORT` | Internal port for Jellyfin | `8096` | No |
| `YT_DLP_PORT` | Port for YouTube downloader | `3033` | No |
| `NGINX_PORT` | Port for Nginx reverse proxy | `80` | No |
| `CLOUDFLARED_TOKEN` | Cloudflare tunnel token | - | Yes |
| `JELLYFIN_MEDIA_PATH` | Path to media directory | `./media` | No |

### Cloudflare Tunnel Setup

1. **Create a Tunnel:**
   ```bash
   cloudflared tunnel login
   cloudflared tunnel create your-tunnel-name
   ```

2. **Get Token:**
   ```bash
   cloudflared tunnel token your-tunnel-name
   ```

3. **Configure DNS:**
   - Add CNAME record pointing to your tunnel
   - Example: `media.yourdomain.com` → `your-tunnel-id.cfargotunnel.com`

## 🛠️ Services

### Jellyfin Media Server
- **Purpose:** Media streaming and organization
- **Features:** Hardware acceleration, plugins, mobile apps
- **Configuration:** Accessible at root path via Nginx
- **First Run:** Set up admin account and media libraries

### qBittorrent
- **Purpose:** Torrent downloading and management
- **Features:** Web UI, RSS feeds, automated downloads
- **Default Credentials:** `admin` / `adminadmin`
- **Web UI:** `http://localhost:8080`

### YT-DLP Web UI
- **Purpose:** Download videos from YouTube and other platforms
- **Features:** Web interface for yt-dlp, queue management
- **Access:** `http://localhost:3033`
- **Supported Sites:** YouTube, Vimeo, Twitch, and 1000+ others

### Nginx Reverse Proxy
- **Purpose:** Load balancing and SSL termination
- **Configuration:** Routes traffic to appropriate services
- **Features:** Security headers, request buffering

### Cloudflare Tunnel
- **Purpose:** Secure remote access without port forwarding
- **Benefits:** HTTPS by default, DDoS protection, global CDN

## 📖 Usage

### Adding Media to Jellyfin

1. **Organize Your Media:**
   ```
   media/
   ├── Movies/
   │   ├── Movie1 (2023)/
   │   └── Movie2 (2023)/
   └── TV Shows/
       ├── Show1/
       │   ├── Season 01/
       │   └── Season 02/
       └── Show2/
   ```

2. **Add Libraries in Jellyfin:**
   - Go to Dashboard → Libraries
   - Add Movie Library pointing to `./media/Movies`
   - Add TV Show Library pointing to `./media/TV Shows`

### Downloading with qBittorrent

1. **Access Web UI:** `http://localhost:8080`
2. **Change Default Password:** Tools → Preferences → Web UI
3. **Add Torrents:** Drag & drop .torrent files or paste magnet links
4. **Set Download Location:** Point to your media directory

### Downloading YouTube Videos

1. **Access YT-DLP UI:** `http://localhost:3033`
2. **Paste URL:** Enter YouTube or other video URLs
3. **Configure Options:** Choose format, quality, output path
4. **Download:** Videos save to your media directory

## 🔧 Troubleshooting

### Common Issues

**Services Won't Start:**
```bash
# Check logs (for detached mode)
docker compose logs [service-name]

# Or run in foreground to see live logs
docker compose up [service-name]

# Restart specific service
docker compose restart [service-name]

# Rebuild if needed
docker compose up --build [service-name]
```

**Permission Issues:**
```bash
# Fix ownership
sudo chown -R $USER:$USER ./jellyfin/
sudo chown -R $USER:$USER ./media/
```

**Cloudflare Tunnel Issues:**
```bash
# Check tunnel status
docker compose logs cloudflared

# Verify token
cloudflared tunnel list
```

**Media Not Showing in Jellyfin:**
- Check file permissions
- Refresh library metadata
- Verify file formats are supported

### Logs and Monitoring

```bash
# View all logs (for detached mode)
docker compose logs -f

# View specific service logs
docker compose logs -f jellyfin
docker compose logs -f qbittorrent
docker compose logs -f yt-dlp-web-ui

# Or run in foreground to see live logs
docker compose up jellyfin
```

## 🤝 Contributing

We welcome contributions! Please follow these steps:

1. **Fork the repository**
2. **Create a feature branch:** `git checkout -b feature/amazing-feature`
3. **Commit your changes:** `git commit -m 'Add amazing feature'`
4. **Push to the branch:** `git push origin feature/amazing-feature`
5. **Open a Pull Request**

### Development Guidelines

- Follow Docker best practices
- Update documentation for any configuration changes
- Test changes thoroughly before submitting
- Use meaningful commit messages

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [Jellyfin](https://jellyfin.org) - The volunteer-built media solution
- [qBittorrent](https://qbittorrent.org) - Feature-rich torrent client
- [yt-dlp](https://github.com/yt-dlp/yt-dlp) - YouTube downloader
- [Cloudflare](https://cloudflare.com) - Secure tunnel service

## 📞 Support

If you need help:

1. **Check the logs:** `docker compose logs`
2. **Review documentation:** This README and service-specific docs
3. **Search issues:** GitHub Issues for similar problems
4. **Create an issue:** If you can't find a solution

---

**Happy streaming! 🎬🍿**
