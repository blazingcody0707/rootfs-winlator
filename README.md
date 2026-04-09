# Root Filesystem Customized
此仓库使用 GPL-3.0 协议开源

[![GPL-3.0](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](LICENSE)

Winlator glibc 定制版 imagefs，用于补全原版 Winlator 的项目，适用于 [moze30/winlator-glibc](https://github.com/moze30/winlator-glibc)。
## 目录
- [使用](#use)
- [编码](#locale)
- [MangoHud](#mangohud)
- [Gstreamer](#gstreamer)
- [构建参数](#configure_arg)
- [其他](#others)
## 声明
任何修改的 Winlator 第三方版本在分发时（非个人使用的分发版本）内置此项目相关文件后，务必声明此仓库链接（在发布时或应用内），以便于修复。
Any modified third-party versions of Winlator distributed (i.e., distribution versions not for personal use) must declare the link to this repository upon release or within the application after incorporating files related to this project, in order to facilitate fixes.
<a id='use'></a>
## 使用
已经添加了所有 Linux 语言的编码补全，对于一些冷门未翻译游戏理论上可能存在效果。总之无论你是任何国家的用户，可以切换对应的编码来改变 Wine 的显示语言和修复字体乱码问题。
同时添加了全球的时区文件，你可以通过 `TZ` 变量来校准 Wine 的显示时间。

只需要替换掉 APK 包 `assets` 文件夹内的 `imagefs.tzst` 文件就能享受比原版更好的解码效果。如果你不想破坏改版的 rootfs 结构，请自行解包并解压此仓库 Releases 的 `output-full.tar.xz`（包含完整的时区文件与所有语言的 UTF-8 编码）或 `output-lite.tar.xz`（不包含编码与时区文件）到 rootfs，然后再使用 `zstd` 压缩为 `imagefs.tzst` 并自行添加到 APK 里。
安装完成后无需重装（除非你重新签名导致安装包发生冲突）。
在 Winlator 主界面：
1. 点击左上角菜单
2. ⚙️ 设置 (Setting)
3. 滑动到页面最底部
4. 重新安装文件系统 (Reinstall System Files)
5. 等待进度条跑完
可能需要创建一个容器进行测试，否则可能不会创建 `libGL.so.1` 的链接。
然后你就可以启动容器来测试解码效果了。
<a id='locale'></a>
## 关于编码
现在所有语言的支持与对应编码支持均已完善，你可以通过设置变量 `LC_ALL`，值为对应语言的变量值，例如 `zh_CN.UTF-8`。
<a id='mangohud'></a>
## 关于 MangoHud
在容器设置环境变量：
`MANGOHUD`
- `1`
`MANGOHUD_CONFIG`
- `engine_version,fps,frametime,ram,version,vulkan_driver,present_mode,arch`
<a id='gstreamer'></a>
## Gstreamer 解码调试
声明变量 `GST_DEBUG` 值为 `4`，如果没有输出则是调用其他解码，请在调试中勾选 `quartz`、`mfplat` 或 `dxva2`。
## MangoHud 调试
声明变量 `MANGOHUD_LOG_LEVEL`，值可以为：
- `off`
- `err`
- `info`（编译默认值）
- `debug`（推荐，否则看不到有用的信息）
## 关于视频解码
对于 Unity H264 游戏，经过测试此版本已经可以流畅播放和解码 H264 视频，不会出现卡顿、卡死或黑屏现象，包括声音也是正常的。但在此之前你必须使用原版自带的 Wine 并在环境变量设置里启用 `WINE_DO_NOT_CREATE_DXGI_DEVICE_MANAGER` 这个变量，如果没有请自行添加，值为 **1**。此变量只存在于原版和应用相关补丁的 Wine 中。
<a id='configure_arg'></a>
## 构建参数
FLAC 与 OPUS 均可以通过 libav 代替。
### GStreamer
```bash
meson setup builddir \
  --buildtype=release \
  --strip \
  -Dgst-full-target-type=shared_library \
  -Dintrospection=disabled \
  -Dgst-full-libraries=app,video,player \
  -Dbase=enabled \
  -Dgood=enabled \
  -Dbad=enabled \
  -Dugly=enabled \
  -Dlibav=enabled \
  -Dtests=disabled \
  -Dexamples=disabled \
  -Ddoc=disabled \
  -Dges=disabled \
  -Dpython=disabled \
  -Ddevtools=disabled \
  -Dgstreamer:check=disabled \
  -Dgstreamer:benchmarks=disabled \
  -Dgstreamer:libunwind=disabled \
  -Dgstreamer:libdw=disabled \
  -Dgstreamer:bash-completion=disabled \
  -Dgst-plugins-good:cairo=disabled \
  -Dgst-plugins-good:gdk-pixbuf=disabled \
  -Dgst-plugins-good:oss=disabled \
  -Dgst-plugins-good:oss4=disabled \
  -Dgst-plugins-good:v4l2=disabled \
  -Dgst-plugins-good:aalib=disabled \
  -Dgst-plugins-good:jack=disabled \
  -Dgst-plugins-good:pulse=enabled \
  -Dgst-plugins-good:adaptivedemux2=disabled \
  -Dgst-plugins-good:v4l2=disabled \
  -Dgst-plugins-good:libcaca=disabled \
  -Dgst-plugins-good:mpg123=enabled \
  -Dgst-plugins-base:examples=disabled \
  -Dgst-plugins-base:alsa=enabled \
  -Dgst-plugins-base:pango=disabled \
  -Dgst-plugins-base:x11=enabled \
  -Dgst-plugins-base:gl=disabled \
  -Dgst-plugins-base:opus=disabled \
  -Dgst-plugins-bad:androidmedia=disabled \
  -Dgst-plugins-bad:rtmp=disabled \
  -Dgst-plugins-bad:shm=disabled \
  -Dgst-plugins-bad:zbar=disabled \
  -Dgst-plugins-bad:webp=disabled \
  -Dgst-plugins-bad:kms=disabled \
  -Dgst-plugins-bad:vulkan=disabled \
  -Dgst-plugins-bad:dash=disabled \
  -Dgst-plugins-bad:analyticsoverlay=disabled \
  -Dgst-plugins-bad:nvcodec=disabled \
  -Dgst-plugins-bad:uvch264=disabled \
  -Dgst-plugins-bad:v4l2codecs=disabled \
  -Dgst-plugins-bad:udev=disabled \
  -Dgst-plugins-bad:libde265=disabled \
  -Dgst-plugins-bad:smoothstreaming=disabled \
  -Dgst-plugins-bad:fluidsynth=disabled \
  -Dgst-plugins-bad:inter=disabled \
  -Dgst-plugins-bad:x11=enabled \
  -Dgst-plugins-bad:gl=disabled \
  -Dgst-plugins-bad:wayland=disabled \
  -Dgst-plugins-bad:openh264=disabled \
  -Dgst-plugins-bad:hip=disabled \
  -Dgst-plugins-bad:aja=disabled \
  -Dgst-plugins-bad:aes=disabled \
  -Dgst-plugins-bad:dtls=disabled \
  -Dgst-plugins-bad:hls=disabled \
  -Dgst-plugins-bad:curl=disabled \
  -Dgst-plugins-bad:opus=disabled \
  -Dgst-plugins-bad:webrtc=disabled \
  -Dgst-plugins-bad:webrtcdsp=disabled \
  -Dpackage-origin="[rootfs-winlator](https://github.com/Waim908/rootfs-winlator)" \
  --prefix=/data/data/com.winlator/files/rootfs/
```
### MangoHud
```bash
meson setup builddir \
  -Dwith_xnvctrl=disabled \
  -Dwith_wayland=disabled \
  -Dwith_nvml=disabled \
  -Dinclude_doc=false
  --prefix=/data/data/com.winlator/files/rootfs/
```
依赖：
### libxkbcommon
```bash
meson setup builddir \
  -Denable-xkbregistry=false \
  -Denable-bash-completion=false \
  -Denable-wayland=false \
  -Denable-tools=false \
  -Denable-bash-completion=false \
  --prefix=/data/data/com.winlator/files/rootfs/
```
<a id='others'></a>
## CA 证书支持
[MoZilla 证书](https://curl.haxx.se/ca/cacert.pem)
## 其他
- [data.tar.zst — 全语言 UTF-8 编码文件 (Ubuntu)](http://ports.ubuntu.com/pool/universe/g/glibc/locales-all_2.39-0ubuntu8.6_arm64.deb)
- [tzdata-2025b-1-aarch64.pkg.tar.xz — 全时区文件 (Arch Linux ARM)](https://eu.mirror.archlinuxarm.org/aarch64/core/tzdata-2025b-1-aarch64.pkg.tar.xz)
## 鸣谢
由衷感谢以下所有项目及其开发者们所作出的努力：
- [Winlator](https://github.com/brunodev85/Winlator)
- [termux-packages](https://github.com/termux/termux-packages)
- [Winlator glibc](https://github.com/longjunyu2/winlator)
## Root Filesystem 依赖与软件环境
### 重新构建 / 新增 / 更新
| 状态 | 项目 |
|------|------|
| 新增 | [MangoHud Cmod](https://github.com/coffincolors/winlator/releases/tag/winlator_mangohud_glibc_v1) |
| 重构并更新 | [GStreamer](https://github.com/GStreamer/gstreamer) |
| 新增 | [xz](https://github.com/tukaani-project/xz) |
| 新增 | [libxkbcommon](https://github.com/xkbcommon/libxkbcommon) |
| 新增 | [xkeyboard-config](https://xorg.freedesktop.org/archive/individual/data/xkeyboard-config/) |
| 更新 | [FLAC](https://github.com/xiph/flac) |
| 更新 | [GLib](https://github.com/GNOME/glib) |
| 新增 | [libxkbfile](https://xorg.freedesktop.org/releases/individual/lib/) |
### 其他
- [MangoHud](https://github.com/flightlessmango/MangoHud) — 未加入（基于原版开源版本）
## 补丁参考
- [glibc-packages](https://github.com/termux-pacman/glibc-packages)
