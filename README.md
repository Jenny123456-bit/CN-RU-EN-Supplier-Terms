
# 中俄双语物流术语库 · CN–RU Logistics Glossary

> 同一票货，国内叫「换装」，俄方单证写 «перегрузка»，英文合同写 «transshipment»。
> 译法不统一，单证就会被退；退一次、重报一次，就是一列车的滞港费。

一个面向中俄铁路 / 班列 / 口岸 / 关务场景的**三语术语工具**：查词、比对译法、校验单证一致性，
内置 AI 术语助手与开放 API，纯静态、零后端、可离线打开。

[![License: MIT](https://img.shields.io/badge/code-MIT-1f6feb.svg)](./LICENSE)
[![Data: CC BY 4.0](https://img.shields.io/badge/data-CC%20BY%204.0-c8a15a.svg)](./data/terms.js)
[![No dependencies](https://img.shields.io/badge/dependencies-0-3fb950.svg)](./package.json)
[![Deploy: GitHub Pages](https://img.shields.io/badge/deploy-GitHub%20Pages-8b6828.svg)](./.github/workflows/pages.yml)

---

## 它解决什么

中俄线单证的痛点不在翻译水平，而在**口径**。同一个动作、同一笔费用，中方口语、俄方单证、英文合同
三套写法；一旦分叉，报关单与运单对不上，链条就断在口岸。

术语库把「会直接造成退单」的词先钉死：**一条推荐译法 + 若干变体译法 + 一句易错提示 + 一个场景例句**。

| 现状 | 本工具 |
| --- | --- |
| 靠老同事口头传译名 | 139 条词条，推荐译法 + 变体译法全部留档 |
| 译法不一致，报关才发现 | 粘贴单证，秒级标出「同一术语 N 种译法」 |
| 不知道错了多少钱 | 按重报周期 × 单车日滞留成本折算风险敞口 |
| 术语只在某个人电脑里 | 静态 JSON + SDK + iframe，任何系统都能接 |

## 功能

- **三语同屏** — 中文 / русский / English 三列始终并列，任何屏幕宽度都不隐藏；窄屏（≤600px）自动转卡片行，三语各占一整行，可直接发给对方对照。
- **三语检索** — 三种语言与变体译法统一打分排序，支持整句粘贴反查。
- **高风险标记** — 22 个「混淆即退单」的词单独标红，可一键只看高危。
- **单证译法校验** — 本地正则比对，识别三类问题：同一术语多译法、非推荐译法、高风险词缺译文。
- **风险成本折算** — 冲突条数 × 重报周期 × 单车日滞留成本，给出保守估算（参数可调）。
- **AI 术语助手** — 右下角小窗，接任意 OpenAI 兼容接口；无密钥时自动降级为本地检索脑。
- **开放接入** — 静态 JSON 端点 / JS SDK / iframe 三种接法，零后端。
- **可分享检索** — `#/terms?q=换装` 直接是链接，发给同事就能打开同一结果。
- **暗色 / 浅色、全键盘操作** — `Ctrl K` 聚焦检索，`↑ ↓` 切换词条，`Esc` 收起浮层。

## 快速开始

```bash
git clone https://github.com/OWNER/REPO.git
cd REPO
```

三种运行方式，任选其一：

```bash
# ① 直接双击 index.html（无需服务器，file:// 可用）
# ② Python 静态服务器
python -m http.server 8080
# ③ Node（package.json 已内置脚本）
npm run dev
```

打开 <http://localhost:8080> 即可。

## 接入到自己的系统

### 1 · 静态 JSON（零依赖）

```bash
curl https://OWNER.github.io/REPO/api/terms.json
curl "https://OWNER.github.io/REPO/api/terms.json?q=%E6%8D%A2%E8%A3%85"   # 前端过滤示例
```

也可走 jsDelivr CDN（自带缓存与全球加速）：

```
https://cdn.jsdelivr.net/gh/OWNER/REPO@main/api/terms.json
```

### 2 · JS SDK

```html
<script src="https://OWNER.github.io/REPO/sdk/zhru-terms.js"></script>
<script>
  ZRTerms.search("换装", { limit: 5 }).then(console.table);
  ZRTerms.audit("在布列斯特完成换装（перевалка），换装费按 перегрузка 计").then(console.log);
  ZRTerms.mount("#box", { query: "слот" });   // 挂一个迷你检索面板
</script>
```

### 3 · iframe 嵌入

```html
<iframe src="https://OWNER.github.io/REPO/#/terms"
        style="width:100%;height:760px;border:0" title="中俄物流术语库"></iframe>
```

完整契约见 [`docs/API.md`](./docs/API.md)。

## AI 助手

应用内任意页面右下角 → **AI 术语助手** → ⚙ 设置：

| 服务商 | Base URL | 推荐模型 |
| --- | --- | --- |
| DeepSeek | `https://api.deepseek.com/v1` | `deepseek-chat` |
| 通义千问 | `https://dashscope.aliyuncs.com/compatible-mode/v1` | `qwen-plus` |
| OpenAI | `https://api.openai.com/v1` | `gpt-4o-mini` |
| Moonshot | `https://api.moonshot.cn/v1` | `moonshot-v1-8k` |
| 本地 Ollama | `http://localhost:11434/v1` | `qwen2.5:7b` |

- 密钥只写入浏览器 `localStorage`，请求由浏览器直连你填写的 Base URL，**不经过任何中转服务器**。
- 未配置密钥时，助手使用本地术语检索作答（术语对比、条款模板、检索建议），不联网。
- 每次提问会自动检索 6 条最相关词条，作为上下文注入 system prompt，约束模型使用库内推荐译法。

## 目录结构

```
.
├─ index.html                 # 应用入口（术语库 / 单证校验 / API / 关于）
├─ assets/
│  ├─ app.css                 # 设计系统（暗色 + 浅色两套变量）
│  └─ app.js                  # 检索、校验、AI、API 调试台
├─ data/terms.js              # ★ 术语数据唯一真源（139 条）
├─ api/                       # 由 scripts/build-api.mjs 生成，勿手改
│  ├─ terms.json              # 全量：meta + terms
│  ├─ index.json              # 元数据 + 分类计数
│  └─ search-index.json       # 轻量索引（id/cn/ru/en/alt）
├─ sdk/zhru-terms.js          # 外部接入 SDK（search / audit / mount）
├─ scripts/build-api.mjs      # 数据校验 + 生成 api/*.json
├─ docs/
│  ├─ API.md                  # 数据契约与接入示例
│  ├─ ARCHITECTURE.md         # 结构与取舍
│  └─ PUBLISH.md              # 发布到 GitHub 的完整步骤
└─ .github/                   # Pages 部署、Issue/PR 模板
```

## 贡献词条

最容易的贡献是一个词：**它曾经让你被退单，或差点被退单**。

1. 在 `data/terms.js` 的对应分类里追加一条（字段说明见 `docs/API.md`）；
2. 跑 `npm run build` —— 脚本会校验 id 唯一、分类合法、中俄双语不重复；
3. 提 PR，模板会问你三个问题：来源、易错点、场景例句。

口径约定见 [CONTRIBUTING.md](./CONTRIBUTING.md)。

## 数据与许可

- 代码：[MIT](./LICENSE)
- 术语数据：[CC BY 4.0](./data/terms.js) —— 可商用、可修改，请保留仓库地址与版本号。
- 例句为场景化示例，不含真实单证数据、客户信息或价格。

## 路线图

- [x] 139 条核心词条（换装 / 关务 / 舱位 / 单证 / 费用 / 异常）
- [x] 检索、分类、高风险标记、收藏
- [x] 单证译法校验 + 风险成本折算
- [x] AI 助手与开放 API
- [ ] 扩展至 300 条（按线路补充：满洲里 / 霍尔果斯 / 二连浩特 / 扎门乌德 / 布列斯特）
- [ ] 哈萨克语（拉丁转写）第三列
- [ ] 术语变更 diff 视图（v1 → v2 译法调整留痕）
- [ ] 铁路运单字段级校验（按 СМГС 栏位逐栏对照）

## 致谢

站台 03 · 自主实践 ——「我给这个岗位，做了一件工具」。

译准，才不赔钱。
