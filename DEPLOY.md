# 部署说明 · 言字卡精简版（v32.5.5）

这是从原版「言字卡」复制出来的**独立精简副本**，只保留运行所需的文件，可直接当静态站点部署。原版说明与授权条款见 `README.md`、`LICENSE`（两者均保持原文，未改动）。

## 相对原版删了什么

已整体移除六个功能及其配套组件：**心讯、播放器、助眠频道、游戏中心、朋友圈、我们的家**。版本号、文件名、Service Worker、CSS、数据 schema 均保持原样，因此老备份、旧消息渲染和原有更新链都能继续沿用。

细节（删除范围、保留策略、验证结果）见随包交付的 `精简说明.md`，该文件不放进仓库。

## 目录结构

```
/
├─ index.html                 # 唯一入口（约 529KB，含全部视图模板）
├─ sw.js                      # Service Worker：四本缓存 + 版本迁移
├─ version.json               # 当前版本号 {"ver":"v32.5.5"}
├─ asset-manifest.json        # 交付件清单（2 个主件 + 18 个懒加载分片）
├─ manifest.json              # PWA 清单
├─ _headers                   # Netlify/Cloudflare 用的缓存头（GitHub Pages 会忽略，无害）
├─ .nojekyll                  # 关掉 GitHub Pages 的 Jekyll 处理
├─ icon*.png / icon*.svg / apple-touch-icon.png
├─ dist/
│  ├─ app.v32.5.5.css         # 1.08MB
│  ├─ app.v32.5.5.js          # 2.13MB
│  └─ lazy.v32.5.5.*.js       # 18 个分片，共 1.53MB
└─ img/                       # 牌面等素材，158 个文件 / 8.3MB
```

合计约 13.7MB、约 195 个文件；单个文件最大 2.13MB，远低于 GitHub 的 100MB 上限，**不需要 Git LFS**。

## 本地自测

```bash
python -m http.server 8000
# 打开 http://127.0.0.1:8000/
```

必须走 http（`file://` 下懒加载分片和 Service Worker 会失败）。首次打开会先出现年龄与公告确认页，勾选后点「确认进入」。

## 上传到 GitHub（推荐用 git 命令行）

1. 在 GitHub 新建仓库，例如 `yanzika-lite`，**不要**勾选自动生成 README/License（避免和本目录文件冲突）。
2. 在本目录执行：

```bash
git init
git add .
git commit -m "言字卡精简版 v32.5.5"
git branch -M main
git remote add origin https://github.com/<你的用户名>/yanzika-lite.git
git push -u origin main
```

3. 仓库 **Settings → Pages**：Source 选 `Deploy from a branch`，Branch 选 `main`、目录选 `/ (root)`，保存后等 1–2 分钟。
4. 访问 `https://<你的用户名>.github.io/yanzika-lite/`，再打开 `version.json` 确认返回 `{"ver": "v32.5.5"}`。

### 如果用网页版拖拽上传，按下表分批拖（每批 ≤100 个文件）

| 批次 | 内容 | 说明 |
| --- | --- | --- |
| 1 | `LICENSE`、`README.md`、`DEPLOY.md`、`.gitignore`、`.nojekyll`、`manifest.json`、`_headers` | 元信息，不影响运行 |
| 2 | `icon.svg`、`icon-maskable.svg`、`icon-192.png`、`icon-512.png`、`icon-maskable-512.png`、`apple-touch-icon.png` | 图标 |
| 3 | `img/` 整个目录（分 2 批，约 158 个） | 素材，先传避免页面白图 |
| 4 | `dist/app.v32.5.5.css` | 样式 |
| 5 | `dist/app.v32.5.5.js` | 主逻辑 |
| 6 | `dist/` 下 18 个 `lazy.v32.5.5.*.js` | 分片 |
| 7 | `version.json`、`asset-manifest.json` | 版本与清单 |
| 8 | `index.html` | 入口，此时站点才真正可用 |
| 9 | **`sw.js`（必须最后）** | 否则 SW 会缓存住一个尚未传全的站点 |

顺序的原理：`dist/*` 文件名自带版本号，属于内容寻址，先传不会影响任何旧缓存；`index.html`、`version.json`、`asset-manifest.json`、`sw.js` 决定「这一版是哪一版」，越晚覆盖越安全，`sw.js` 放最后可避免首次访问就装上指向未上传文件的缓存。

## 以后更新版本的顺序

同一个版本号内改内容，直接覆盖同名文件即可（文件名不变＝URL 不变），但要注意：

1. 先传 `dist/` 新件 → 再传 `index.html` → 最后 `sw.js`；
2. 若换了版本号（v32.5.6），文件名全变，**旧件不要删**，留一版给还在旧缓存里的用户兜底；
3. `version.json` 与 `asset-manifest.json` 的 `ver` 必须和文件名一致，否则更新检测会失灵。

## 授权提醒

原版 `LICENSE` 允许**私人自用与私人二改**，但规定：保留署名、禁止商用倒卖、**公开部署或公开代码仓库需先经原作者同意**（在作者主页留言/私信说明改了什么、给谁用、放在哪里）。仓库若设为 Public，请先按该条款取得授权；仅自己私有部署则不受此限。本副本未改动 `LICENSE` 与 `README.md`。
