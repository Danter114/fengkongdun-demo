# 风控盾 demo · GitHub Pages 发布目录

这个目录**不是手工维护的** —— 它由 `_web_sim_src` 的 `npm run build` 顺带产出
（见 `build.mjs` 里「GitHub Pages 镜像」那一段）：构建会先把成品落进
`后端\静态\demo\`，再把这**整棵树原样复制**到这里。
**改 UI 之后重跑一次构建，这里就刷新了**；手工改这里的东西下次构建会被覆盖
（构建前会先删掉整个目录，不留旧文件）。

## 里面是什么

```
index.html          demo.js  demo.css          ← 第 01 页「输入舆情事件」
step2/index.html    step2.js step2.css        ← 第 02 页「知识图谱构建」
step3/index.html    step3.js step3.css        ← 第 03 页「仿真推演」（回放）
step3/report.html   step3/echarts.min.js      ← 第 03 页的报告，由 iframe 按需加载
step5/index.html    step5.js step5.css        ← 第 05 页「深度互动」
.nojekyll                                     ← 空文件
README.md                                     ← 就是本文件
```

> ★ **目录名是跟着"工作流步骤号"走的，所以没有 `step4/` 这个目录**，
> 别以为是漏传了。第 04 步「报告生成」的内容住在第 03 页里（进度条走满，
> 报告就在同一页铺开）。

| 文件 | 说明 |
|---|---|
| `index.html` | 第 01 页。url 末尾不用带文件名，`.../<仓库名>/` 直接就是它 |
| `step2/index.html` | 第 02 页。`.../<仓库名>/step2/` |
| `step3/index.html` | 第 03 页。`.../<仓库名>/step3/` |
| `step3/report.html` | 引擎真跑出来的那份可视化报告（EVT-001 · 42 轮）。**不写在壳里**，进度条走满后由 iframe 按 `./report.html` 加载 |
| `step3/echarts.min.js` | ECharts 5.4.3（约 1 MB）。报告画图用，**随包发、不挂公网 CDN** |
| `step5/index.html` | 第 05 页。`.../<仓库名>/step5/` |
| `*.js` | React + 全部组件，已 bundle。运行期零外链、不联网 |
| `*.css` | Tailwind 只编译用到的类 |
| `.nojekyll` | 空文件。别让 GitHub Pages 走 Jekyll，也免得带下划线的文件被吞 |

**第 02 页为什么一进来有条进度条**：从第 01 页点「构建知识图谱」跳过来时，
正文区中间会先走一遍五步构建进度 —— 构建知识图谱 → 构建人物画像 → 框定角色槽位 →
预设画像库选人 → 生成最终数据，一步一秒、**总计 5 秒**，走完才把图谱铺出来
（对应"建图"那段真流程，不是凑的动画）。

**直接把 `/step2/` 单独打开不会播这条进度**，一进去就是图谱 —— 它只在"从首页
点进来"时才有意义（刷新、收藏夹进来、探针挖 DOM 都走这条路）。想强制看一遍：
地址后挂 `?build=1`。

**第 03 页为什么是"回放"**：`/sim` 那份界面是接后端的（进度、报告都要问服务器）。
GitHub Pages 是纯静态托管，没有后端。所以这一页把真跑过的那一次 **EVT-001（42 轮）**
的结果烤进了包里 —— 界面、时序、数字都和 `/sim` 一致，只是数据是随包带的。
弹窗里的轮 18 / S = 0.9283、报告里的每一个数，都取自那次真跑的产物（来源写在
`_web_sim_src\src\demo\step3\replay.js` 的注释里）。

**第 05 页是什么**：跑完推演、看完报告之后，接着往下问。
两个模式 —— 与 Report Agent 对话（就报告内容提问）、与任意个体对话
（先挑一个智能体，再以"那个人"的口吻聊）。个体那一档带一张**形象卡片**：
渠道 / 角色类型 / 人设简介，数据取自本场那 102 位智能体的画像库
（烘在 `step5.js` 里，所以断网也能选人）。

> ⚠ **第 05 页不连任何模型**：它是纯静态页，答案**离线烘好随包发**。
> 但内容是有出处的、不是占位文本 —— Report Agent 那档照着实报告整理（`reportQa.js`），
> 个体那档拿真快照喂模型烘成（`dialogues.js`，102 位各一份）。所以断网也问得下去。

**每一页的 js/css 就放在它自己那一层**，页面里引的是 `./step2.css` 这种相对路径。
所以：

- url 末尾带不带文件名都行（目录下有 `index.html`）
- 换域名、换仓库名、改成挂在本机后端 `/demo/` 下 —— **一个字符都不用改**

★ 也因此有个**必须守住的规矩**：每一页的 url **要以斜杠结尾**（`/step2/`，不是 `/step2`）。
相对链接是按"当前目录"解析的；少了那个斜杠，浏览器会把当前目录认成上一级，
`../` 就退过头了。Pages 上目录天然带斜杠，本机后端由 `app.py` 的
`/demo` → `/demo/` 重定向保证。

## 怎么发布（本机没装 git，走网页上传）

### 1. 建仓库
github.com → New repository → 名字随意（如 `fengkongdun-demo`）→ 可见性选 **Public**
→ Create。

> ⚠ 免费账号的 GitHub Pages **只支持公开仓库**。也就是说这几页会**公开可搜**。
> 页面里没有业务系统数据，用的是本场 demo 自己的画像库（第 02 / 05 页演示的就是它）。
> 但**"公开可搜"这件事你要心里有数**。

### 2. 传文件
仓库页 → **Add file → Upload files** → 把 `index.html`、`demo.js`、`demo.css`
**和 `step2`、`step3`、`step5` 这三个文件夹**一起拖进去 → Commit changes。

> ★ **传的是这个目录"里面的东西"，不是 `_demo_pages` 这个目录本身。**
> 把整个 `_demo_pages` 文件夹拖进去的话，站点的根就变成 `.../<仓库名>/_demo_pages/`，
> 首页多一层、本文后面写的地址全不对。

> 拖文件夹是可行的：Chrome / Edge 的上传框接受整个目录，会连子目录一起传。
> **`step3/` 要连着里面五个文件一起传**（`index.html`、`step3.js`、`step3.css`、
> `report.html`、`echarts.min.js`）—— 少一个，第 03 页要么白屏要么弹不出报告
> 对应的图。传完仓库根目录下应当能看到 `index.html`、`demo.js`、`demo.css`、
> `step2/`、`step3/`、`step5/` 六样（**没有 `step4/`，那是空的**）。
>
> ⚠ `echarts.min.js` 有 1 MB，上传会慢一点，等进度条走完再 Commit。

然后 **Add file → Create new file** → 文件名填 `.nojekyll` → **内容留空** → Commit。

> 为什么 `.nojekyll` 要单独建：文件名以点开头，Windows 资源管理器把它当隐形，
> 拖拽上传框里很可能选不中。手建一个空文件最稳。

### 3. 打开 Pages
仓库 → **Settings → Pages** →
Source 选 **Deploy from a branch** → Branch 选 `main`、目录选 **`/ (root)`** → Save。

等 1–2 分钟，地址就是：

```
https://<你的用户名>.github.io/<仓库名>/          ← 第 01 页
https://<你的用户名>.github.io/<仓库名>/step2/    ← 第 02 页
https://<你的用户名>.github.io/<仓库名>/step3/    ← 第 03 页
https://<你的用户名>.github.io/<仓库名>/step5/    ← 第 05 页「深度互动」
```

### 4. 以后更新
改完界面 → `npm run build`（会同时刷新本目录）→ 回仓库
**Add file → Upload files** 再传一遍（同名会覆盖）→ Commit。
Pages 一两分钟后自动重建。

---

## 装过 git 的话（更顺）

```powershell
cd D:\桌面\mirofish阅读\_demo_pages
git init -b main
git add -A
git commit -m "风控盾 demo"
git remote add origin https://github.com/<你的用户名>/<仓库名>.git
git push -u origin main
```

---

## 发布前先本地验一遍

```powershell
python -I D:\桌面\mirofish阅读\_p67_pages_check.py     # 镜像体检：文件齐不齐、逐位对不对、渲染得出来吗
python -I D:\桌面\mirofish阅读\_p68_http_interaction.py # 真点一遍：拖文件 / 翻页 / 回放 / 有没有出站请求
```

`_p67` 会拿这个目录**起一个和 Pages 行为一样的纯静态服务**（没有服务端逻辑），
逐项查：`/`、`/step2/`、`/step3/`、`/step5/` 取不取得到、MIME 对不对、页面里引的相对资源
在镜像里有没有、镜像里的 js/css 和交付目录**是不是逐位相同**（防"传了旧版本"），
最后用 Edge 无头**真渲染一遍**确认不白屏 —— 第 03 页要渲染两次（进度中 / 跑完），
只看一次等于放掉这一页一半的行为。

`_p68` 更进一步：透过 `http://` 用真事件点一遍，并在第 03 页等报告铺开后读
`performance.getEntriesByType('resource')` —— **把浏览器真发过的请求列出来**，
看有没有一条出了本站（同源 iframe 里那份也读，因为 ECharts 是在 iframe 的文档里
加载的，父文档看不到）。这条是"离线可跑、不依赖公网"唯一说得硬的证据：
源码里 grep 不到 `https://` 只说明没写死，说明不了运行期不发请求。
