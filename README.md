# x-clip-zh

一个给 OpenClaw / agent 用的剪藏 skill。

它的目标很直接：把 X/Twitter 或普通网页链接，稳定地转成中文 Obsidian Markdown 笔记，并把原文图片一起本地化保存。

## 特色

- 非中文内容默认做**忠实翻译**，不擅自改写成二创文章
- 支持 X 链接抓取失败时自动回退到 `r.jina.ai`
- 原文有图时，自动下载到本地 `Clippings/assets/`
- 图片按阅读节奏嵌入 Markdown，而不是全部堆到顶部
- 最后做真实落地校验，确保笔记和图片确实写进用户自己的 Obsidian vault

## 适合场景

- 把推文长帖收进 Obsidian
- 把英文网页翻成中文笔记
- 做长期可搜索、可复用的资料沉淀
- 保留原文图片和结构，而不是只存一个摘要

## 关键实现思路

1. 先抓原链接正文
2. 抓不到或不完整时，回退到 `r.jina.ai`
3. 识别语言，非中文默认忠实翻译
4. 如有图片，下载为本地文件并嵌入笔记
5. 根据 Obsidian 配置解析用户自己的目标 vault
6. 写入 `Clippings/` 与 `Clippings/assets/`
7. 校验文件、图片、embed 是否真实存在

## 使用前提

对方把这个 skill 拉到自己的 OpenClaw 后，通常在下面这些条件成立时就能直接用：

- agent 具备基础的 `read` / `write` / `web_fetch` / `exec` 能力
- 本机已安装 Obsidian
- 用户本地已经有至少一个可写的 Obsidian vault
- agent 能读取本机的 Obsidian 配置文件
- agent 被允许往用户自己的 vault 写入 Markdown 和图片文件

如果这些条件缺一块，这个 skill 也不是不能用，但就不该硬跑，应该先追问或降级处理。

## 可配置项

这个公开版故意去掉了作者个人环境里的固定位置。

使用时，需要让 agent 自己询问或解析用户的：

- 目标 Obsidian vault 名称
- 目标保存目录（默认可为 `Clippings/`）
- 是否需要严格翻译 / 总结 / 改写

## 失败时的默认处理

为了更接近“拉下来就能用”，建议 agent 先自动尝试，再在必要时追问：

1. 先自动读取 Obsidian 配置并推断默认 vault
2. 若无法唯一确定目标 vault，再询问用户 vault 名称
3. 若 `Clippings/` 不存在，则自动创建
4. 若平台或路径不兼容，明确告诉用户需要提供保存目录
5. 若没有文件写权限或缺少依赖，不要假装完成，直接报出阻塞点

## 文件

- `SKILL.md`：skill 规范本体

如果你想把它做成可分发版本，建议再补一个示例安装方式，或者直接发布到 GitHub / ClawHub。
