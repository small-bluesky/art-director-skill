# art-director-skill

将用户的一句话故事创意转化为专业、可执行的美术设计视觉方案。纯提示词驱动，内置27种动态漫视觉风格体系，输出涵盖视觉总纲、色彩剧本、材质体系、场景空间、人物造型、镜头光线、参考包、AI生图风格提示词及执行建议，适配竖版9:16动态漫画制作，可直接作为美术组前期设计简报和AI生图平台的提示词来源。

## 推荐模型与 API

本技能为纯提示词驱动，不绑定特定模型。以下为经过实测的推荐方案，按"易落地性"排序：

### 🥇 首选：DeepSeek-V3（性价比最高）

| 项目 | 说明 |
|------|------|
| API 地址 | `https://api.deepseek.com/v1` |
| 模型名 | `deepseek-chat` |
| 价格 | 输入 ¥1/百万token，输出 ¥2/百万token |
| 优势 | 国内直连无需代理，中文视觉描述能力极强，价格仅为 GPT-4o 的 1/30 |
| 注册 | [https://platform.deepseek.com](https://platform.deepseek.com) |

```python
from openai import OpenAI

client = OpenAI(
    api_key="your-deepseek-api-key",
    base_url="https://api.deepseek.com/v1"
)

response = client.chat.completions.create(
    model="deepseek-chat",
    messages=[
        {"role": "system", "content": SYSTEM_PROMPT},
        {"role": "user", "content": "你的故事创意"}
    ],
    temperature=0.8,
    max_tokens=6000
)
```

### 🥈 备选：硅基流动 SiliconFlow（国内最便捷）

| 项目 | 说明 |
|------|------|
| API 地址 | `https://api.siliconflow.cn/v1` |
| 模型名 | `deepseek-ai/DeepSeek-V3`（也支持 Qwen、GLM 等） |
| 价格 | 新用户赠送额度，DeepSeek-V3 约 ¥0.5/百万token |
| 优势 | 一个 API Key 可切换多模型，国内直连，Web 界面友好 |
| 注册 | [https://siliconflow.cn](https://siliconflow.cn) |

### 🥉 品质首选：GPT-4o（效果最稳定）

| 项目 | 说明 |
|------|------|
| API 地址 | `https://api.openai.com/v1` |
| 模型名 | `gpt-4o` |
| 价格 | 输入 $2.5/百万token，输出 $10/百万token |
| 优势 | 结构化输出最可靠，视觉描述最精准，适合预算充足的生产环境 |
| 注意 | 国内需代理访问 |

### 其他可用模型

- **智谱 GLM-4-Plus**：`https://open.bigmodel.cn/api/paas/v4`，中文文化语境理解力强
- **Qwen2.5-72B**：通过硅基流动或阿里云 DashScope 调用，中文能力优秀
- **Claude 3.5 Sonnet**：创意质量极高，但国内访问不便且价格较高

## 快速开始

### 方式一：直接复制提示词（最快）

打开 [skill.yaml](./skill.yaml)，找到 `system_prompt` 字段，将完整内容复制到任意 LLM 平台的 System Prompt 设置中，将一句话创意作为用户消息发送即可。

### 方式二：在 Dify 中导入

1. 创建 Chatflow 应用，添加 LLM 节点
2. System Prompt 粘贴 `system_prompt` 完整内容
3. 模型参数：Temperature `0.8`，Max Tokens `6000`
4. 添加开始节点，定义输入变量 `user_idea`
5. LLM 节点用户消息引用 `{{user_idea}}`
6. 发布应用

### 方式三：在 Coze 中导入

1. 创建新 Bot，在"人设与回复逻辑"中粘贴 `system_prompt`
2. 设置 Temperature 为 0.8
3. 将一句话创意直接发送给 Bot

### 方式四：Python 脚本调用

```python
import yaml
from openai import OpenAI

with open("skill.yaml", "r", encoding="utf-8") as f:
    skill = yaml.safe_load(f)

client = OpenAI(
    api_key="your-api-key",
    base_url="https://api.deepseek.com/v1"
)

result = client.chat.completions.create(
    model="deepseek-chat",
    messages=[
        {"role": "system", "content": skill["system_prompt"]},
        {"role": "user", "content": "一个落魄钢琴手在地下停车场听见一段神秘旋律，追踪后发现弹奏者是30年前的自己。"}
    ],
    temperature=skill["runtime"]["temperature"],
    max_tokens=skill["runtime"]["max_tokens"]
)

print(result.choices[0].message.content)
```

## 输入示例

```
一个落魄钢琴手在地下停车场听见一段神秘旋律，追踪后发现弹奏者是30年前的自己。
```

## 输出示例

完整输出示例请查看 [examples/output_01.md](./examples/output_01.md)，包含以下结构化段落：

- **【视觉总纲】** — 核心视觉概念、风格锚定、动态漫风格选择、视觉母题、色温光比、竖版构图适配
- **【色彩剧本】** — 三幕色彩走向、HEX色码、色彩象征规则、禁忌色
- **【材质与质感体系】** — 主材质谱、质感对比规则、材质叙事演变
- **【场景空间设计】** — 3-5个核心场景的完整空间设计
- **【人物造型建议】** — 造型关键词、具体外形、色彩定位、视觉弧光
- **【镜头与光线建议】** — 镜头语言、光线策略、构图建议
- **【视觉参考包】** — 5-8个具体参考及借鉴方式
- **【AI生图风格提示词】** — 场景/人物/封面提示词，可直接用于TeleStudio等AI生图平台
- **【执行注意事项】** — 预算敏感点、替代方案、拍摄风险、后期需求

## 动态漫27种风格体系

本技能内置了27种动态漫视觉风格，覆盖当前短视频漫画市场的主流画风。输入一句话创意后，Skill 会自动匹配最合适的风格，并在所有设计环节中贯彻该风格的视觉特征。

完整的风格通用前缀和示例提示词存储在 [references/style-prompts.md](./references/style-prompts.md) 中，可直接复制用于 AI 生图平台。

### 提示词构建公式

```
[风格通用前缀] + [场景/人物描述] + [色调说明] + [风格后缀总结] + 竖版，9:16比例。
```

- 精简模式：`风格词 + 核心场景词` 即可快速跑通视觉基调
- 完整模式：按公式逐层叠加细节

## 与 screenwriter-skill 配合使用

本技能包与 [screenwriter-skill](https://github.com/small-bluesky/screenwriter-skill) 天然互补：

1. 先用 **screenwriter-skill** 生成剧本（故事前提→人物→分场大纲→剧本→编剧注）
2. 将同一句话创意输入 **art-director-skill**，生成视觉方案
3. 两者输出的场景和人物可交叉对照，形成完整的"文字+视觉"前期设计包

## 高级用法

### 调整运行时参数

- **temperature**：`0.7-0.9` 适合创意发散。如需更精确的色彩和材质描述，可降至 `0.6`
- **max_tokens**：本技能输出较长，建议 ≥ `6000`。使用 DeepSeek-V3 时可设为 `8000`
- **stream**：默认关闭。流式输出适合实时展示生成过程

### 与 RAG 结合

在 LLM 调用前增加知识库检索节点，注入以下类型的参考资料：
- 特定时代/地域的建筑与室内设计资料
- 色彩心理学与色彩科学论文
- 经典电影的美术设计分析文档
- 材质与工艺参考库

### 接入图像生成

`extensions.image_generation` 预留了图像生成接口。未来工作流可扩展为：
1. LLM 生成视觉方案文本
2. 提取色彩剧本和场景描述
3. 自动调用 Midjourney / DALL-E / Stable Diffusion 生成概念图

### 拆分为多节点工作流

在 Dify 等平台中可将工作流拆分：
1. 节点一：视觉总纲 + 色彩剧本 + 材质体系
2. 节点二：场景空间设计（依赖节点一的色彩和材质定义）
3. 节点三：人物造型 + 镜头光线 + 参考包 + 执行建议

## 常见问题

### 为什么新增了"材质与质感体系"和"镜头与光线建议"？

原始版本缺少这两个维度。"材质体系"确保全片视觉触感的一致性——没有它，不同场景可能看起来像不同电影。"镜头与光线建议"则为美术设计和摄影指导之间搭建桥梁，避免美术组搭了景但摄影组打不了光的情况。

### 为什么 max_tokens 设为 6000 而不是 4000？

美术设计方案的输出量通常大于剧本。8个段落中，场景空间设计和视觉参考包尤其占篇幅。6000 token 可确保输出不被截断。使用 DeepSeek-V3 时，其价格极低，建议直接设为 8000。

### HEX 色码在实际拍摄中真的有用吗？

HEX 色码主要服务于前期设计沟通和后期 DI 调色参考。实拍时美术组更关注色板和实物参照，但 HEX 码能确保设计文档中的色彩描述无歧义，方便与调色师、灯光师数字化协作。

### 推荐用 DeepSeek 还是 GPT-4o？

- **日常使用、个人项目、预算有限**：DeepSeek-V3，性价比极高
- **商业项目、需要最稳定输出**：GPT-4o，结构化输出最可靠
- **国内快速上手**：硅基流动，注册即用，支持多模型切换

## 项目结构

```
art-director-skill/
├── README.md
├── skill.yaml
├── references/
│   └── style-prompts.md     # 27种动态漫风格完整提示词模板
├── examples/
│   ├── input_01.txt
│   └── output_01.md
├── assets/
│   └── workflow-diagram.png
├── LICENSE
└── .gitignore
```

## 贡献指南

欢迎通过 Issue 或 PR 参与贡献：

- 提示词优化，提升特定类型（科幻、古装、悬疑等）的视觉设计质量
- 提供更多示例输入输出
- 适配更多 Agent 平台的导入方案
- 补充更多模型的实测效果对比

请确保所有提交使用 **UTF-8 without BOM** 编码，换行符为 **LF**。

## 许可证

本项目基于 MIT 许可证开源，详见 [LICENSE](./LICENSE) 文件。