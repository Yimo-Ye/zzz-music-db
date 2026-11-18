# 绝区零音乐库 · ZZZ MUSIC DB

> 一个基于 VitePress 的纯静态《绝区零》音乐数据库 —— 收录曲目信息、专辑封面、歌词，并提供全站常驻播放器。

🌐 在线站点：<https://yimo-ye.github.io/zzz-music-db/>

---

## ✨ 特点

- **纯静态托管**：VitePress 预渲染每个页面，零后端、零数据库，扔到 GitHub Pages 即可运行。
- **全站播放器**：底部播放条挂在 `layout-bottom` 插槽上，切页面不中断播放，支持进度拖拽、音量记忆、展开/折叠，并同步系统媒体控制（媒体键 / 锁屏信息）。
- **五种页面形态**：首页看板、曲库检索、歌曲详情、歌手档案、专辑档案，彼此通过 `?name=` / `?genre=` 互相跳转。
- **首页两段式导航**：上半部分是「统计条 + 精选歌单」，下半部分是「曲目类型」卡片网格，点击直接带筛选条件进曲库。
- **数据驱动**：所有页面内容都从 `songs.json` 推导，首页歌单则来自只记录曲目编号的 `themes.json` —— 加一条数据 = 自动出现在曲库、分类统计、歌手页、专辑页，改歌单不用碰页面代码。
- **容错做得比较狠**：封面走「URL 编码 → 原名」双轨降级，歌词走「转义路径 → 物理原名」双轨嗅探，零宽字符统一清洗，避免中文标点导致 404；歌单里 id 失效会自动跳过并用其它曲目补齐。
- **深浅双主题背景**：浅色 / 深色各一张背景图，全站（含直接刷新任意子路由）常驻，切换模式自动换图，不靠颜色蒙层。
- **响应式**：歌单网格 3 → 2 → 1 列，统计条 4 → 2 列；曲库宽屏页码全展开、窄屏窗口化，横向永不溢出。

---

## 📄 页面与功能

| 页面 | 路由 | 主要功能 |
|---|---|---|
| 首页 | `/` | 统计条（收录歌曲 / 专辑 / 收录进度 / 主题歌单）、多组精选歌单（当前 9 组，来自 `themes.json`，卡片内可点封面播放、点歌名进详情）、曲目类型网格（按 `genre` 自动聚合，点击带筛选进曲库） |
| 曲库 | `/library` | 关键词搜索（歌名 / 歌手 / 专辑 / 简介）、分类筛选（带数量，支持 `?genre=` 直达）、分页（每页 20 条）、点击封面播放 |
| 歌曲详情 | `/song?name=歌名` | 拟真黑胶唱盘、封面两级降级、歌词外部 txt 加载（支持居中 / 左对齐切换）、制作班底（制作/作词/作曲/编曲）、B 站 MV 跳转 |
| 歌手档案 | `/artist?name=歌手` | 该歌手的专辑馆藏 + 全部单曲 |
| 专辑档案 | `/album?name=专辑` | 该专辑下的曲目列表 |
| 关于 | `/README` | 本文件 |

### 交互细节

- 点击任意曲目卡片左侧封面 → 广播 `zzz-play-track` 自定义事件 → 常驻播放器接管播放。
- 首页歌名链接只占文字宽度（不会被拉伸成整行），长标题仍按省略号截断。
- 播放音源为网易云官方外链：`https://music.163.com/song/media/outer/url?id={wyid}.mp3`。
- 音量写入 `localStorage`（键名 `zzz-player-volume`），下次打开自动恢复。
- 顶栏到顶全透明，下滑才出现毛玻璃；导航项为胶囊样式，hover / 当前页带独立磨砂。

---

## 🧱 技术栈

- [VitePress 1.6](https://vitepress.dev/) + Vue 3（`<script setup>`，页面直接用 Composition API 写逻辑）
- [vue3-toastify](https://github.com/jh-leong/vue3-toastify) —— 播放失败等提示
- 原生 CSS（`color-mix()` / `backdrop-filter` / CSS 变量），无 UI 框架
- Node.js 脚本 + Python 脚本用于数据清洗与补全

---

## 📁 目录结构

```text
zzz-music-db/
├─ index.md                  # 首页（layout: home + 统计条 / 精选歌单 / 曲目类型）
├─ library.md                # 曲库检索页
├─ song.md                   # 歌曲详情页（歌词、黑胶、制作班底）
├─ artist.md                 # 歌手档案页
├─ album.md                  # 专辑档案页
├─ README.md                 # 本文件 = 站内「关于」页
├─ .vitepress/
│  ├─ config.mts             # 站点配置（base、标题、导航、社交链接）
│  └─ theme/
│     ├─ index.js            # 主题扩展：注册播放器 / Toast / 自定义 CSS，并注入背景图地址
│     ├─ custom.css          # 全站样式：背景层、顶栏磨砂、胶囊导航
│     └─ components/
│        └─ GlobalPlayer.vue # 全站常驻播放器
├─ public/
│  └─ data/
│     ├─ songs.json          # ⭐ 曲目数据源
│     ├─ themes.json         # ⭐ 首页精选歌单（只记曲目 id）
│     ├─ images/             # 专辑封面 {专辑名}.jpg
│     ├─ lyrics/             # 纯文本歌词 {歌名}.txt
│     └─ zzz-bg-light.webp   # 浅色主题背景
│        zzz-bg-dark.webp    # 深色主题背景
├─ convert-songs.mjs         # 数据清洗：把字段统一成数组
├─ fetch_wy_ids.py           # 批量补全网易云 wyid
├─ run_dev.bat               # 清缓存 + 启动开发服务器
├─ run_build.bat             # 生产构建
├─ copy_readme.bat           # 把 README.md 拷进 dist
└─ deploy.sh                 # 构建并推送到 gh-pages
```

---

## 🗂️ 数据结构

`public/data/songs.json` 是一个歌曲对象数组，字段如下：

| 字段 | 类型 | 说明 |
|---|---|---|
| `id` | string | 唯一编号（被 `themes.json` 引用，同时参与「收录进度」计算） |
| `title` | string | 曲名，同时作为歌词文件名 |
| `artist` | string[] | 歌手列表 |
| `producer` | string[] | 制作人 |
| `lyricist` | string[] | 作词 |
| `composer` | string[] | 作曲 |
| `arranger` | string[] | 编曲 |
| `album` | string | 专辑名，同时作为封面文件名 |
| `year` | string | 发行年份 |
| `genre` | string | 分类（如 `BGM/纯音乐`、`角色EP/角色MV`、`伴奏`…） |
| `language` | string[] | 语言 |
| `wyid` | string | 网易云音乐歌曲 ID，**播放功能依赖此字段** |
| `bvid` | string | B 站视频号（可选） |
| `description` | string | 简介 / 备注 |

示例：

```json
{
  "id": "1",
  "title": "覆灭重生 Come Alive",
  "artist": ["Philip Strand", "雷声"],
  "producer": ["雷声"],
  "lyricist": ["Philip Strand", "雷声"],
  "composer": ["Philip Strand", "雷声"],
  "arranger": ["雷声"],
  "album": "绝区零-覆灭重生 Come Alive",
  "year": "2024",
  "genre": "主题曲/宣传曲",
  "language": ["国语", "英语"],
  "wyid": "2604937294",
  "bvid": "BV1E6421Z7rm",
  "description": "OP，公测宣传曲"
}
```

### `public/data/themes.json` —— 首页精选歌单

首页那 9 张歌单卡片不是写死在页面里的，而是由这个文件驱动。**这里只记曲目编号**，标题、歌手、封面等全部到 `songs.json` 里查，所以改歌单不需要动 `index.md`。

| 字段 | 类型 | 说明 |
|---|---|---|
| `key` | string | 唯一标识，仅用于 Vue 的 `:key`，不显示 |
| `title` | string | 卡片标题 |
| `desc` | string | 卡片副标题（单行显示，建议 11 字以内，超出会省略号） |
| `emoji` | string | 卡片图标，单个 emoji |
| `ids` | string[] | 曲目编号，数组顺序 = 卡片内从上到下的顺序（建议 3 个） |

示例：

```json
{
  "key": "classic",
  "title": "典藏精选",
  "desc": "官方主题曲与版本主打",
  "emoji": "💎",
  "ids": ["1", "442", "226"]
}
```

取数规则：

- 按 `ids` 顺序到 `songs.json` 里查，查不到（曲目下架 / 编号变动）自动跳过；
- 同一个 `id` 只会在一个歌单里出现，重复写了后面的那份会被跳过；
- 某张歌单不足 3 首时，自动用其它未使用曲目补齐，保证卡片高度一致；
- 整个文件缺失或 JSON 解析失败，首页降级为「暂无精选歌单」，不影响统计条和曲目类型。

### 资源命名约定

| 资源 | 路径 | 命名规则 |
|---|---|---|
| 专辑封面 | `public/data/images/{专辑名}.jpg` | 必须与 `album` 字段完全一致 |
| 歌词 | `public/data/lyrics/{歌名}.txt` | 必须与 `title` 字段完全一致，纯文本 |

> 页面会先按 URL 编码请求，失败再按原名重试，因此中文、逗号、括号、空格通常无需手动改名。

### 收录进度怎么算

取数组最后一条的 `id` 视为「目标总量」，用 `已收录数量 ÷ 目标总量` 得到百分比。所以 `id` 必须连续递增，进度才有意义。

---

## 🚀 本地开发

```bash
npm install          # 首次
npm run docs:dev     # 开发预览 http://localhost:5173/zzz-music-db/
npm run docs:build   # 生产构建，输出到 .vitepress/dist
npm run docs:preview # 预览构建产物
```

Windows 下也可以直接双击：

| 脚本 | 作用 |
|---|---|
| `run_dev.bat` | 先删掉 `.vitepress/cache`（缓存真出过问题），再启动 dev |
| `run_build.bat` | 直接 `npm run docs:build` |
| `copy_readme.bat` | 把根目录 `README.md` 复制进 `dist`，保证线上也有原始 README |

---

## 🛠️ 维护数据

### 新增一首歌

1. 往 `public/data/songs.json` 追加一条记录，`id` 接续最大值，`artist` 等字段写成数组。
2. 放封面：`public/data/images/{专辑名}.jpg`。
3. 放歌词（纯音乐可跳过）：`public/data/lyrics/{歌名}.txt`。
4. 补 `wyid`（没有它就无法播放），可以手动填，也可以跑脚本。
5. `npm run docs:dev` 检查，然后构建部署。

### 调整首页歌单

1. 打开 `public/data/themes.json`，每项对应首页一张卡片。
2. 改 `title` / `desc` / `emoji` 换文案和图标，改 `ids` 换曲目（建议正好 3 个）。
3. `desc` 是单行显示，建议控制在 11 个汉字内，太长会被省略号截断。
4. 调节数组顺序 = 调整卡片在首页的排列顺序。

> 曲目类型（如「角色EP/角色MV」「BGM/纯音乐」）不需要维护，它由 `songs.json` 的 `genre` 自动统计，图标表写在 `index.md` 的 `GENRE_ICONS` 里。

### 两个辅助脚本

| 脚本 | 作用 | 注意 |
|---|---|---|
| `convert-songs.mjs` | 把 `artist / producer / lyricist / composer / arranger / language` 里的 `A & B` 字符串切成数组，并把 `NA` / `xxx` / 空串洗成 `[]` | 读写的路径写死为 `public/data/songs-test.json`，正式使用前改路径 |
| `fetch_wy_ids.py` | 调网易云 Web 搜索接口，按「歌名 + 首位歌手」匹配出 `wyid` 写回 JSON | 同样是 `songs-test.json`；接口为公开接口，请控制频率 |

---

## 📦 部署

站点配置里 `base` 为 `/zzz-music-db/`，对应 GitHub Pages 的项目路径：

```ts
// .vitepress/config.mts
export default defineConfig({
  base: '/zzz-music-db/',
  // ...
})
```

一键发布（构建 → 用临时 git 仓库强推 `dist` 到 `gh-pages`，脚本里的目标分支是 `master:gh-pages`）：

```bash
sh deploy.sh
```

也可以直接用 GitHub Actions 或任意静态托管（Netlify / Vercel / Cloudflare Pages），把构建命令设为 `npm run docs:build`、产物目录设为 `.vitepress/dist` 即可。

> ⚠️ 换域名或改仓库名时，务必同步修改 `base`，否则 `public/` 下的图片、歌词、背景图会全部 404。

---

## ⚠️ 几个已知约定

- **路由用查询参数**：页面靠 `?name=` / `?genre=` 传参，在 `onMounted` 里读 `location.search`，不做 SSR 动态路由。
- **首页歌单由 JSON 驱动**：要换歌单只改 `public/data/themes.json`（标题 / 副标题 / 图标 / 曲目 id 都在里面）；「曲目类型」网格则是把 `songs.json` 的 `genre` 自动聚合 + 计数，按数量倒序排列，不需要手工维护。
- **播放强依赖 `wyid`**：缺失时播放器会直接 toast 提示「暂无网易云在线音源」。
- **歌词是纯文本**：按行渲染（`white-space: pre-line`），不支持时间轴滚动。
- **背景图地址在 JS 里拼**：`theme/index.js` 用 `import.meta.env.BASE_URL` 拼出 `--home-bg-light` / `--home-bg-dark` 写进 `<html>`，样式留在 `custom.css` —— 换 `base` 只改一处，背景不会 404。

---

## 📜 声明

- 本站为**非官方粉丝项目**，与米哈游 / HoYoverse 无隶属关系。
- 音乐版权归 **米哈游 / 三Z-STUDIO** 所有；本站仅做信息索引，音频指向网易云音乐官方外链接口，不存储、不分发任何音频文件。
- 数据仅收录网易云音乐平台「三Z-STUDIO」发行专辑中的曲目，经 AI 检索 + 人工复核整理，若有疏漏欢迎提交 Issue。
- 封面、歌词等素材版权归原作者所有，如涉及侵权请联系删除。

