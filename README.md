# Gmu Music Player - RG52MINI 中文+EQ 编译包

这个包包含了 GMU 音乐播放器的 EQ 增强源码修改和 GitHub Actions 自动编译工作流。

编译后的版本支持：
- ✅ 中文文件名/歌词显示（通过 SDL_ttf）
- ✅ 5 段 EQ 均衡器（60Hz / 230Hz / 910Hz / 3.6kHz / 14kHz）
- ✅ 频谱显示（GMU 原生）
- ✅ 歌词显示（.lrc / .txt）
- ✅ 多种主题（含 4 套中文 TTF 主题）

---

## 编译方法（GitHub Actions，免费）

### 第一步：创建 GitHub 仓库
1. 注册/登录 GitHub（https://github.com）
2. 新建一个仓库（比如叫 `gmu-rg52mini`），选 Public
3. 把这个压缩包的所有文件上传到仓库根目录

### 第二步：触发编译
1. 上传文件后，GitHub 会自动触发 Actions 编译
2. 或者手动触发：仓库页面 → Actions → 选择 "Build Gmu for RG52MINI" → Run workflow
3. 等待约 3-5 分钟编译完成

### 第三步：下载编译产物
1. 编译完成后，进入 Actions → 点击最新的运行记录
2. 页面底部 Artifacts 区域下载 `gmu-rg52mini-aarch64-ttf-eq`
3. 解压得到 `gmu.bin` 和 `frontends/sdl.so`

---

## 部署到掌机

1. 把编译好的 `sdl.so` 复制到掌机：
   ```
   /roms/ports/gmu-music-player/frontends/sdl.so
   ```
   （覆盖原文件）

2. 把 `gmu.bin` 复制到：
   ```
   /roms/ports/gmu-music-player/gmu.bin
   ```
   （可选，原 gmu.bin 一般不需要替换）

3. 修改配置文件 `gmu.rg52mini.conf`，添加/修改：
   ```ini
   SDL.DefaultSkin=theme-dark-ttf
   EQ.Enabled=yes
   EQ.Band0=0.0
   EQ.Band1=0.0
   EQ.Band2=0.0
   EQ.Band3=0.0
   EQ.Band4=0.0
   ```

4. 重启 Gmu 即可

---

## EQ 配置说明

| 配置项 | 频段 | 范围 |
|---|---|---|
| EQ.Band0 | 60Hz（超低音） | -12.0 ~ +12.0 dB |
| EQ.Band1 | 230Hz（低音） | -12.0 ~ +12.0 dB |
| EQ.Band2 | 910Hz（中音） | -12.0 ~ +12.0 dB |
| EQ.Band3 | 3.6kHz（高音） | -12.0 ~ +12.0 dB |
| EQ.Band4 | 14kHz（超高音） | -12.0 ~ +12.0 dB |

常用 EQ 预设：
- 流行：Band0=2, Band1=1, Band2=0, Band3=2, Band4=3
- 摇滚：Band0=4, Band1=2, Band2=-1, Band3=1, Band4=3
- 古典：Band0=3, Band1=0, Band2=0, Band3=0, Band4=3
- 爵士：Band0=2, Band1=1, Band2=1, Band3=1, Band4=2

---

## 频谱和歌词操作

- **频谱**：播放时按 Start 切到「曲目信息」页，反复按 A/B 循环切换显示模式，频谱在循环中
- **歌词**：把 .lrc/.txt 歌词文件和音乐放同目录同名，曲目信息页按 B 显示
- **主题切换**：按 Select+L1 进设置，或修改配置文件 SDL.DefaultSkin

---

## 文件说明

```
.github/workflows/build.yml  - GitHub Actions 编译工作流
patches/audio.h              - EQ API 声明（替换原文件）
patches/audio.c              - EQ 实现（替换原文件，5段BiQuad均衡器）
README.md                    - 本说明文件
```

工作流会自动：
1. 从 jhe2/gmu 检出最新源码
2. 应用 EQ 修改
3. 安装 AArch64 交叉编译工具链 + SDL2/SDL2_image/SDL2_ttf
4. 编译生成支持 TTF 中文 + EQ 的 sdl.so
