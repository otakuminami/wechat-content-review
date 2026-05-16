# 微信公众号合规审查

> WeChat Official Account Content Compliance Reviewer

一个公众号文章合规审查工具，基于微信公众平台运营规范和《网络信息内容生态治理规定》，对文章内容逐段审查，标注风险等级并给出修改建议。适配多个 AI 助手平台。

## 功能

- 逐段扫描文章，按 🔴 严重(11 类) / 🟡 高风险(8 类) / 🟢 中风险(9 类) 三级标注
- 输出结构化审查报告：逐项排查表 + 修改建议 + 结论
- 自动识别「影射中国」的风险模式
- 全文倾向性判定（正面/中性/负面）——负面倾向自动上调风险等级
- 疑罪从有原则——边界模糊一律按较高风险标注
- 对书评/国际资讯类文章采用适度放宽但非豁免标准
- **纯审计工具，不拦截发布**——拿到报告后自行决策

---

## 安装

```bash
git clone https://github.com/<你的用户名>/wechat-content-review.git
cd wechat-content-review
```

你只需要项目中的特定文件，不需要安装任何依赖。

---

## 平台支持

| 平台 | 适配文件 | 使用方式 |
|------|---------|---------|
| **WorkBuddy / CodeBuddy** | `SKILL.md` | 安装为 Skill，对话中说「帮我审一下」即可 |
| **Cursor** | `platforms/cursor/.cursorrules` | 复制到项目根目录或用户级 Cursor Rules |
| **Claude Code** | `platforms/claude-code/CLAUDE.md` | 复制到项目根目录或 `~/.claude/CLAUDE.md` |
| **ChatGPT Custom GPT** | `platforms/chatgpt/` | 创建 Custom GPT，粘贴 prompt.md 并上传知识文件 |

---

## 各平台配置说明

### WorkBuddy / CodeBuddy

**安装方式：**

将 `SKILL.md` 和 `references/` 目录复制到 WorkBuddy skills 目录：

```bash
mkdir -p ~/.workbuddy/skills/wechat-content-review
cp SKILL.md ~/.workbuddy/skills/wechat-content-review/
cp -r references ~/.workbuddy/skills/wechat-content-review/
```

**操作步骤：**

1. 在对话中输入以下任意一句话触发审查：
   - `帮我审一下这篇文章`
   - `检查一下能不能发`
   - `有没有违规风险`
2. 将文章内容粘贴到对话中
3. AI 自动逐段审查，输出结构化报告
4. 根据报告中的修改建议自行调整文章

**示例：**

```
你：帮我审一下这篇文章

（粘贴文章全文）

AI：## 🔍 合规审查报告
    **文章**：XXX
    ...
```

---

### Cursor

将 `platforms/cursor/.cursorrules` 复制到以下任一位置：

- **项目级**：项目根目录 `.cursorrules`
- **用户级**：Cursor Settings → Rules → User Rules，粘贴内容

**操作步骤：**

1. 在 Cursor Chat（Cmd+L）中，直接粘贴文章内容
2. 附上一句触发语，如 `请审查这篇文章的合规性` 或 `帮我审一下`
3. AI 按 `.cursorrules` 中定义的规则执行审查，输出报告
4. 根据修改建议调整原文

**注意：** Cursor 不会自动识别粘贴内容为审查请求，必须附带明确的审查指令。

---

### Claude Code

将 `platforms/claude-code/CLAUDE.md` 复制到以下任一位置：

- **项目级**：项目根目录 `CLAUDE.md`
- **用户级**：`~/.claude/CLAUDE.md`

**操作步骤：**

1. 在终端启动 Claude Code：`claude`
2. 输入触发语加文章内容：
   ```
   帮我审一下这篇文章：

   （文章全文）
   ```
3. Claude Code 读取 `CLAUDE.md` 中的审查规则，输出结构化报告
4. 或使用 `/review` 类自定义命令（如已配置）

**注意：** 如果文章较长（3000+ 字），建议先让 Claude Code 读取文章文件，再请求审查：
```
请读取 article.md，然后帮我做合规审查
```

---

### ChatGPT Custom GPT

**配置步骤：**

1. 打开 ChatGPT →「Explore GPTs」→「Create」
2. 名称设为「公众号合规审查」
3. 将 `platforms/chatgpt/prompt.md` 的内容粘贴到 Instructions
4. 在 Knowledge 中上传 `references/wechat-guidelines.md`
5. 在 Conversation starters 中添加：
   - `帮我审一下这篇公众号文章`
   - `检查这篇文章能不能发`
   - `这篇文章有没有违规风险`
6. 保存并发布

**使用步骤：**

1. 在 ChatGPT 中找到你创建的「公众号合规审查」GPT
2. 直接粘贴文章内容，或选择一个会话起手
3. GPT 按预设规则执行审查，输出结构化报告
4. 可以追问：「这段具体哪里有问题？」「帮我重写这一段」

**注意：** Custom GPT 对超长文章有上下文限制。建议单次审查的文章不超过 8000 字。长文可分段审查。

---

## 审查维度

| 风险等级 | 覆盖类别 | 示例 |
|---------|---------|------|
| 🔴 严重 (11) | 政治敏感、颠覆政权、领土主权、民族仇恨、邪教迷信、色情低俗、暴力恐怖、谣言虚假、赌博诈骗、人身攻击、非法商业 | 批评政治制度、台独港独表述、无资质医疗推广 |
| 🟡 高风险 (8) | 意识形态、极端修辞、群体攻击、境外势力、敏感历史、标题党、引战嫌疑、恐慌煽动 | 体制性批评、「炸弹」修辞、「速删」类标题 |
| 🟢 中风险 (9) | 外国政治、社会矛盾、公共卫生、宗教话题、医疗健康、食品安全、教育批评、经济敏感、性别对立 | 外国选举评论、贫富差距、未经证实的医疗建议 |

---

## 审查报告示例

```
## 🔍 合规审查报告

**文章**：ChatGPT 对教育体制的冲击
**文章类型**：普通
**风险等级**：🟡 需注意
**全文倾向**：中性

### 逐项排查
| # | 类别 | 位置 | 原文片段 | 风险 | 建议 |
|---|------|------|----------|------|------|
| 1 | 教育批评 | 第3段 | "教育体制僵化已久" | 🟡 | 限制在具体问题讨论，避免体制性批评 |
| 2 | 标题党 | 标题 | "教育已死" | 🟡 | 改为客观表述，如「传统教育的挑战」 |

### 修改建议
- [ ] 第3处：「教育体制僵化已久」 → 「传统教育模式面临新挑战」
- [ ] 标题：「教育已死」 → 「AI 时代，教育面临的新挑战」

### 结论
整体风险可控。主要问题在标题和第三段对教育体制的批评，建议用中性表达替代绝对化论断。
```

---

## 文件结构

```
wechat-content-review/
├── SKILL.md                        # WorkBuddy/CodeBuddy 入口
├── README.md                       # 本文件
├── LICENSE                         # MIT 许可证
├── .gitignore
├── references/
│   └── wechat-guidelines.md        # 微信公众平台运营规范摘要
└── platforms/
    ├── cursor/
    │   └── .cursorrules            # Cursor 适配
    ├── claude-code/
    │   └── CLAUDE.md               # Claude Code 适配
    └── chatgpt/
        ├── README.md               # ChatGPT 配置说明
        └── prompt.md               # Custom GPT 指令
```

---

## 开源协议

MIT License — 详见 [LICENSE](./LICENSE)

## 贡献

欢迎提交 Issue 或 Pull Request。主要贡献方向：

- 审查维度的补充和细化
- 更多触发词/模式的覆盖
- 报告模板的优化
- 更多平台的适配（Windsurf、Cline、Coze、Dify 等）
