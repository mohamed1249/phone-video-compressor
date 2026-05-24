# 📱 Phone Video Compressor

A free command-line tool that finds large videos on your Android phone, compresses them using your **NVIDIA GPU** (or CPU fallback) with minimal quality loss, and replaces the originals on your phone — freeing up storage without losing your memories.

Built with Python, FFmpeg, and ADB.

---

## ✨ Features

- Scans your phone for videos above a size you choose (default 50 MB)
- Compresses using H.265 (HEVC) — typically **40–60% smaller** with near-identical quality
- **GPU accelerated** with NVIDIA NVENC — up to **5–8x faster** than CPU encoding
- Automatic CPU fallback if no NVIDIA GPU is detected
- Skips already-compressed videos automatically
- Replaces originals on your phone safely — pushes the new file first, only deletes the original after confirming the transfer succeeded
- Keeps `_compressed` in the filename so you always know which files were processed
- Saves a manifest log of everything processed

---

## 📋 Requirements

Before running the tool, install these two free programs:

### 1. ADB (Android Debug Bridge)
Used to communicate with your phone over USB.

- Download **Platform Tools** from: https://developer.android.com/tools/releases/platform-tools
- Extract the zip to a folder (e.g. `C:\platform-tools`)
- Add that folder to your Windows PATH:
  - Search "Environment Variables" in the Start menu
  - System Variables → Path → New → paste `C:\platform-tools`
  - Open a new CMD window and test: `adb version`

### 2. FFmpeg
Used for the actual video compression.

- **Easiest:** open CMD and run:
  ```
  winget install ffmpeg
  ```
- Or download manually from https://ffmpeg.org/download.html and add the `/bin` folder to PATH
- Test it: `ffmpeg -version`

### 3. USB Debugging on your phone
- Go to **Settings → About phone**
- Tap **Build number** 7 times (this unlocks Developer Options)
- Go to **Settings → Developer Options**
- Enable **USB Debugging**
- Plug your phone into your laptop via USB-C
- A popup will appear on your phone — tap **Allow**

---

## 🚀 Getting Started

### Option A — Run the .exe (recommended, no Python needed)
1. Download `PhoneVideoCompressor.exe` from the [Releases](../../releases) page
2. Make sure ADB and FFmpeg are installed (see Requirements above)
3. Double-click the `.exe` and follow the prompts

### Option B — Run from source (requires Python 3.9+)
```bash
# Clone the repo
git clone https://github.com/YOUR_USERNAME/phone-video-compressor.git
cd phone-video-compressor

# Install dependencies
pip install ffmpeg-python

# Run
python main.py
```

### Option C — Build the .exe yourself
```bash
# Make sure you have Python and pip installed, then run:
build.bat
# The exe will appear at dist\PhoneVideoCompressor.exe
```

---

## 🖥️ How to Use

When you launch the tool, it walks you through everything step by step.

### Step 1 — Configure Settings
On first launch you'll be asked to set:

| Setting | Description | Default |
|---|---|---|
| Output folder | Where compressed videos are saved on your laptop | `C:\CompressedVideos` |
| Size threshold | Only compress videos larger than this (MB) | `50` |
| Encoder | GPU or CPU encoder to use | `hevc_nvenc` (GPU) |
| Quality (CQ) | Lower = better quality, larger file | `28` |
| Preset | Speed vs compression trade-off | `p4` (balanced) |

### Step 2 — Scan & Compress (Menu Option 1)
- Scans the following folders on your phone by default:
  - `/sdcard/DCIM`
  - `/sdcard/Movies`
  - `/sdcard/Download`
  - `/sdcard/Pictures`
  - `/sdcard/WhatsApp/Media/WhatsApp Video`
- Lists all videos above your size threshold
- Asks for confirmation before starting
- Copies each video to your laptop, compresses it, saves it with `_compressed` in the name
- Shows progress and space saved for each file

### Step 3 — Replace on Phone (Menu Option 2)
- Run this **after** Step 2, once you're happy with the compressed videos
- Pushes each compressed video back to the exact same location on your phone
- Deletes the original only after confirming the transfer was successful
- Triggers a gallery refresh so your phone picks up the changes immediately

> **These two steps are intentionally separate.** You can review the compressed videos on your laptop before committing to replacing anything on your phone.

---

## ⚙️ Encoder Guide

| Encoder | Requires | Speed | Quality |
|---|---|---|---|
| `hevc_nvenc` | NVIDIA GPU | Very fast | Excellent |
| `h264_nvenc` | NVIDIA GPU | Fastest | Good |
| `libx265` | CPU only | Slow | Excellent |
| `libx264` | CPU only | Fast | Good |

**Recommended:** `hevc_nvenc` if you have an NVIDIA GPU, `libx265` otherwise.

If you select a GPU encoder but it's not available on your machine, the tool automatically falls back to `libx265` on CPU.

### Quality Setting (CQ/CRF)
| Value | Quality | File Size |
|---|---|---|
| 24 | High quality | Larger |
| 28 | Balanced ✅ | Recommended |
| 32 | Some loss | Smaller |

---

## 🔒 Is it safe?

Yes. The tool is designed to never lose your videos:

- It always **pushes the compressed file first** and verifies the transfer before deleting the original
- If anything goes wrong during transfer, the original is left untouched
- The two-step design means you can **review compressed videos** on your laptop before replacing anything on your phone
- A `_manifest.json` file keeps a full log of everything processed

---

## 🛠️ Troubleshooting

**"No device found"**
- Make sure your phone is plugged in and the screen is unlocked
- Check that USB Debugging is enabled
- Try a different USB cable or port
- Run `adb devices` in CMD to see what's detected

**"Device is unauthorized"**
- Look at your phone screen — there should be a popup asking to allow USB Debugging
- Tap **Allow** and try again

**"GPU encoder not found"**
- Your FFmpeg build may not include NVENC support
- Run: `ffmpeg -encoders | findstr nvenc` to check
- If nothing shows, reinstall FFmpeg via `winget install ffmpeg` which includes NVENC

**Compression is slow**
- Make sure you selected a GPU encoder (`hevc_nvenc` or `h264_nvenc`)
- Verify your NVIDIA drivers are up to date: run `nvidia-smi` in CMD
- Try preset `p1` for maximum speed at the cost of slightly larger files

**Videos not showing in gallery after replacement**
- The tool triggers a media scanner automatically, but it can take a minute
- If they still don't appear, restart your phone

---

## 📁 File Structure

```
phone-video-compressor/
├── main.py          # Main application (all logic in one file)
├── build.bat        # Builds the .exe using PyInstaller
├── README.md        # This file
├── .gitignore       # Excludes build output and temp files
└── LICENSE          # MIT License
```

---

## 🤝 Contributing

Contributions are welcome! If you find a bug or have an idea:

1. Open an [Issue](../../issues) to discuss it first
2. Fork the repo and create a branch: `git checkout -b feature/your-feature`
3. Make your changes and test them
4. Submit a Pull Request

