# TECH：技术方案设计

> 文件名：TECH-技术方案设计.md
> 版本：V1.0
> 设计角色：资深前端架构师
> 设计日期：2025-04-15
> Git 提交：`docs: 架构师输出技术方案`

---

## 一、技术选型

### 1.1 技术栈

| 技术 | 选型 | 说明 |
|------|------|------|
| 页面结构 | HTML5 语义化标签 | 单文件 `index.html`，无框架依赖 |
| 样式方案 | Tailwind CSS (CDN) | 原子化 CSS，快速开发，响应式开箱即用 |
| 交互逻辑 | 原生 JavaScript (ES6+) | 无需构建工具，浏览器直接运行 |
| AI 能力 | MiniMax API（OpenAI 兼容接口） | Coding Plan 提供的 API Key，使用 MiniMax-M2.7 模型 |
| 数据存储 | localStorage | 收藏、计数、场景征集等轻量数据 |
| 字体 | 系统字体栈 + Google Fonts (Noto Sans SC) | 中文显示优化 |

### 1.2 选型理由

| 决策 | 理由 |
|------|------|
| **单文件 HTML** | 参赛作品要求轻量化，打开即用，无需构建/部署 |
| **Tailwind CSS CDN** | 无需安装，class 即样式，响应式适配极快 |
| **原生 JS** | 无框架依赖，代码量可控，SOLO 可直接生成 |
| **MiniMax API** | 用户已提供 Coding Plan API Key，OpenAI 兼容接口，前端可直接 fetch 调用 |
| **localStorage** | 无需后端，满足所有数据持久化需求 |

---

## 二、整体架构设计

### 2.1 架构图

```
┌──────────────────────────────────────────────────┐
│                  index.html（单文件）               │
│                                                    │
│  ┌──────────────────────────────────────────────┐ │
│  │              UI 层（HTML + Tailwind）          │ │
│  │  ┌─────────┐ ┌─────────┐ ┌────────────────┐ │ │
│  │  │ Header  │ │ Scene   │ │ Input Area     │ │ │
│  │  │ (金句)  │ │ Buttons │ │ (textarea)     │ │ │
│  │  └─────────┘ └─────────┘ └────────────────┘ │ │
│  │  ┌─────────┐ ┌─────────┐ ┌────────────────┐ │ │
│  │  │ Tone    │ │ Generate│ │ Result Area    │ │ │
│  │  │ Select  │ │ Button  │ │ (话术展示)     │ │ │
│  │  └─────────┘ └─────────┘ └────────────────┘ │ │
│  │  ┌─────────┐ ┌─────────┐ ┌────────────────┐ │ │
│  │  │ Actions │ │ Ranking │ │ Feedback       │ │ │
│  │  │ (操作)  │ │ (排行)  │ │ (征集/分享)    │ │ │
│  │  └─────────┘ └─────────┘ └────────────────┘ │ │
│  └──────────────────────────────────────────────┘ │
│                                                    │
│  ┌──────────────────────────────────────────────┐ │
│  │              JS 逻辑层                        │ │
│  │  ┌────────────┐  ┌────────────────────────┐ │ │
│  │  │ SceneModule │  │ InputModule            │ │ │
│  │  │ 场景管理    │  │ 输入处理               │ │ │
│  │  └────────────┘  └────────────────────────┘ │ │
│  │  ┌────────────┐  ┌────────────────────────┐ │ │
│  │  │ AIModule   │  │ ToneModule             │ │ │
│  │  │ API调用    │  │ 语气切换               │ │ │
│  │  └────────────┘  └────────────────────────┘ │ │
│  │  ┌────────────┐  ┌────────────────────────┐ │ │
│  │  │ CopyModule │  │ StorageModule          │ │ │
│  │  │ 复制功能   │  │ localStorage 管理      │ │ │
│  │  └────────────┘  └────────────────────────┘ │ │
│  │  ┌────────────┐  ┌────────────────────────┐ │ │
│  │  │ RankModule │  │ FallbackModule         │ │ │
│  │  │ 排行统计   │  │ 预设话术降级           │ │ │
│  │  └────────────┘  └────────────────────────┘ │ │
│  └──────────────────────────────────────────────┘ │
│                                                    │
│  ┌──────────────────────────────────────────────┐ │
│  │              数据层                           │ │
│  │  ┌────────────────────────────────────────┐  │ │
│  │  │ localStorage                          │  │ │
│  │  │ - sceneCounts: {场景使用次数}           │  │ │
│  │  │ - favorites: [收藏的话术]               │  │ │
│  │  │ - likes: {话术点赞数}                   │  │ │
│  │  │ - userScenes: [用户提交的场景]           │  │ │
│  │  │ - dailyQuote: {今日金句索引}            │  │ │
│  │  │ - usageCount: {每日使用次数(限流)}       │  │ │
│  │  └────────────────────────────────────────┘  │ │
│  └──────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────┘
         │
         │ fetch (HTTPS)
         ▼
┌─────────────────────┐
│  MiniMax API        │
│  (OpenAI 兼容接口)   │
│  模型: MiniMax-M2.7  │
└─────────────────────┘
```

### 2.2 文件结构

```
ai-workplace-helper/
├── index.html              # 唯一的代码文件（HTML + CSS + JS 全部内联）
├── PRD-产品需求文档.md      # PRD 定稿
├── TECH-技术方案设计.md     # 本文档
├── PLAN-开发迭代计划.md     # 开发计划（阶段3产出）
├── README-参赛作品说明.md   # 参赛文案（阶段8产出）
├── .gitignore
└── screenshots/            # 功能截图（阶段7产出）
```

---

## 三、核心模块划分

### 3.1 模块清单

| 模块 | 职责 | 优先级 | 依赖 |
|------|------|--------|------|
| **SceneModule** | 场景数据定义、场景切换、示例文案填充 | P0 | 无 |
| **InputModule** | 情境输入框管理、字数统计、示例快速填入 | P0 | SceneModule |
| **AIModule** | MiniMax API 调用、Prompt 构造、流式解析、打字机效果 | P0 | InputModule, ToneModule |
| **ToneModule** | 4 种语气定义、语气切换、联动重新生成 | P1 | AIModule |
| **CopyModule** | 一键复制到剪贴板、复制成功反馈 | P0 | 无 |
| **StorageModule** | localStorage 读写封装、数据结构管理 | P0 | 无 |
| **FallbackModule** | 预设话术库管理、API 失败时自动降级 | P1 | AIModule |
| **RankModule** | 场景使用计数、TOP3 排行展示 | P2 | StorageModule |
| **FeedbackModule** | 场景征集、话术点赞、每日金句、分享引导 | P2 | StorageModule |

### 3.2 模块间调用关系

```
用户操作
  │
  ├─ 点击场景按钮 ──→ SceneModule ──→ InputModule（填充示例）
  │
  ├─ 输入情境 ──→ InputModule
  │
  ├─ 选择语气 ──→ ToneModule ──→ AIModule（重新生成）
  │
  ├─ 点击生成 ──→ AIModule
  │                    │
  │                    ├─ 成功 → 打字机效果展示
  │                    │
  │                    └─ 失败 → FallbackModule（降级展示）
  │
  ├─ 点击复制 ──→ CopyModule
  │
  ├─ 点赞/征集 ──→ FeedbackModule ──→ StorageModule
  │
  └─ 查看排行 ──→ RankModule ──→ StorageModule
```

---

## 四、AI API 接入方案

### 4.1 MiniMax API 调用方式

用户提供的 API Key（`sk-api-ESLfbbly...`）是 **Coding Plan 专用**，使用 **OpenAI 兼容接口**格式。

**接口地址**：`https://api.minimax.chat/v1/chat/completions`

**请求格式**（OpenAI 兼容）：
```javascript
const response = await fetch('https://api.minimax.chat/v1/chat/completions', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer sk-api-ESLfbblyEMs4j2_DU9u6QVhB7axdOP1V-hc_1lMc-pSMH_po8aDhqY6WfNrJJ8k7ONfBWkeLgI0omIjaFIwmHrl16iLuP85dYbU7KEVAgqXpdUNj18wCl-4'
  },
  body: JSON.stringify({
    model: 'MiniMax-M2.7',
    messages: [
      { role: 'system', content: SYSTEM_PROMPT },
      { role: 'user', content: userMessage }
    ],
    stream: true,           // 流式输出，实现打字机效果
    temperature: 0.8,       // 适度随机性
    max_tokens: 512         // 话术不需要太长
  })
});
```

### 4.2 流式输出解析（SSE）

```javascript
// 读取流式响应
const reader = response.body.getReader();
const decoder = new TextDecoder();

while (true) {
  const { done, value } = await reader.read();
  if (done) break;

  const chunk = decoder.decode(value);
  const lines = chunk.split('\n');

  for (const line of lines) {
    if (line.startsWith('data: ')) {
      const data = line.slice(6);
      if (data === '[DONE]') break;

      try {
        const parsed = JSON.parse(data);
        const content = parsed.choices?.[0]?.delta?.content;
        if (content) {
          // 追加到展示区，实现打字机效果
          appendToResult(content);
        }
      } catch (e) { /* 忽略解析错误 */ }
    }
  }
}
```

### 4.3 Prompt 工程

**System Prompt（系统角色设定）**：
```
你是一位拥有10年经验的职场沟通教练，精通各类职场场景的高情商表达。
你的风格是：专业但不生硬，温暖但不谄媚，坚定但不冒犯。

你的任务是根据用户描述的职场情境，生成一段得体的话术。
```

**User Prompt 模板**：
```
当前场景：{sceneName}
语气风格：{toneName}（{toneDescription}）
用户情境：{userInput}

请生成一段50-200字的职场话术，要求：
1. 语气{toneDescription}，符合职场礼仪
2. 表达清晰自然，口语化，不生硬
3. 直接输出话术内容，不添加"以下是话术"等前缀
4. 如涉及具体数据/时间，用XX占位
5. {toneExtraRule}
```

**语气风格对应的额外规则**：
```javascript
const TONE_RULES = {
  gentle: '多用"理解""建议""可以吗""方便的话"等柔性词汇',
  firm: '多用"需要""建议""期望"等明确词汇，有理有据',
  concise: '控制在50字以内，直奔主题，不铺垫',
  humor: '可适当加入轻松元素（如自嘲、比喻），但保持职场得体'
};
```

### 4.4 降级方案

当 API 调用失败时（超时 5 秒 / 网络错误 / 额度不足），自动切换到预设话术库：

```javascript
async function generateWithFallback(scene, tone, input) {
  try {
    // 尝试 API 调用，设置 5 秒超时
    const result = await Promise.race([
      callMiniMaxAPI(scene, tone, input),
      new Promise((_, reject) =>
        setTimeout(() => reject(new Error('TIMEOUT')), 5000)
      )
    ]);
    return { source: 'ai', content: result };
  } catch (error) {
    console.warn('API 调用失败，使用降级方案:', error.message);
    const fallback = getFallbackText(scene, tone);
    return { source: 'fallback', content: fallback };
  }
}
```

---

## 五、话术存储方案

### 5.1 场景数据结构

```javascript
const SCENES = [
  {
    id: 'salary',
    name: '谈薪沟通',
    icon: '💰',
    placeholder: '例：我在公司工作了2年，承担了更多责任，想和领导谈加薪',
    examples: [
      '我在公司工作了2年，承担了更多责任，想和领导谈加薪',
      '入职时薪资偏低，现在想申请调薪',
      '跳槽拿到offer，想用这个和现公司谈涨薪留任'
    ]
  },
  {
    id: 'resign',
    name: '离职沟通',
    icon: '👋',
    placeholder: '例：我决定跳槽了，想和领导/同事友好告别',
    examples: [
      '我决定跳槽了，想和领导友好告别',
      '想和团队同事告别，感谢大家的帮助',
      '需要写一封离职邮件，好聚好散'
    ]
  },
  {
    id: 'refuse',
    name: '拒绝不合理需求',
    icon: '🙅',
    placeholder: '例：同事总让我帮他做PPT，但我自己的工作都做不完',
    examples: [
      '领导让我周末加班，但我已经安排了家庭活动',
      '同事总让我帮他做PPT，但我自己的工作都做不完',
      '其他部门的人让我帮忙做不属于我的工作'
    ]
  },
  {
    id: 'report',
    name: '向上汇报',
    icon: '📊',
    placeholder: '例：项目延期了，我需要向领导解释原因并提出解决方案',
    examples: [
      '项目延期了，需要向领导解释原因并提出解决方案',
      '想向领导汇报本周工作成果和下周计划',
      '有一个改进建议想向领导提出'
    ]
  },
  {
    id: 'apologize',
    name: '职场道歉',
    icon: '🙏',
    placeholder: '例：我在会议上说错了话，得罪了同事，想道歉',
    examples: [
      '我在会议上说错了话，得罪了同事，想道歉',
      '错过了一个重要的deadline，需要向领导道歉',
      '和同事产生了误会，想澄清并道歉'
    ]
  },
  {
    id: 'hr',
    name: 'HR/猎头沟通',
    icon: '🤝',
    placeholder: '例：猎头挖我，但我不想去，怎么委婉拒绝',
    examples: [
      '猎头挖我，但我不想去，怎么委婉拒绝',
      '面试后想发一封感谢信',
      '和HR谈薪资期望，怎么表达比较合适'
    ]
  },
  {
    id: 'cross',
    name: '跨部门协作',
    icon: '🔗',
    placeholder: '例：其他部门一直不配合，我需要推进项目但对方不给资源',
    examples: [
      '其他部门一直不配合，需要推进项目但对方不给资源',
      '需要跨部门借人帮忙，不知道怎么开口',
      '对方部门甩锅给我们，需要委婉地推回去'
    ]
  },
  {
    id: 'performance',
    name: '绩效面谈',
    icon: '📈',
    placeholder: '例：领导给我打了低绩效，我觉得不公平，想沟通',
    examples: [
      '领导给我打了低绩效，我觉得不公平，想沟通',
      '年终自评不知道怎么写才能突出自己的贡献',
      '想和领导沟通明年的绩效目标和期望'
    ]
  }
];
```

### 5.2 语气数据结构

```javascript
const TONES = [
  { id: 'gentle', name: '温和', icon: '🌸', desc: '委婉得体，适合对上级' },
  { id: 'firm',   name: '坚定', icon: '💪', desc: '有理有据，维护权益' },
  { id: 'concise',name: '简洁', icon: '⚡', desc: '直奔主题，高效沟通' },
  { id: 'humor',  name: '幽默', icon: '😄', desc: '轻松化解，拉近距离' }
];
```

### 5.3 预设话术库（降级方案）

```javascript
const FALLBACK_TEXTS = {
  salary: {
    gentle: '领导您好，想和您沟通一下薪酬方面的事情。过去这段时间我在XX项目中承担了XX工作，也取得了一些成果。希望能结合目前的贡献和市场情况，适当调整薪资级别。感谢您的时间。',
    firm: '领导，我想就薪酬调整和您沟通。过去一年我负责了XX项目，产出了XX成果，承担了更多职责。根据市场水平和我的贡献，我期望薪资能调整到XX水平。',
    concise: '领导，基于我近期在XX项目的额外贡献和职责扩展，申请薪资调整，期望能安排时间详谈。',
    humor: '领导，有个"钱"的问题想和您聊聊😂 我最近在XX项目上又是加班又是扛活，感觉自己的价值也需要一次"版本升级"了，方便的话聊几句？'
  },
  refuse: {
    gentle: '非常理解你这边比较紧急，但我目前手上有优先级更高的任务在赶deadline，实在抽不出整块时间。要不你先看看XX文档，或者我晚点有空帮你看一下关键点？',
    firm: '这个需求我目前没办法接，手头的项目已经排满了，强行加进来会影响交付质量。建议你和XX沟通一下资源分配，或者调整一下优先级。',
    concise: '现在排不开，手头项目优先级更高，建议找XX协调资源。',
    humor: '兄弟，我的排期已经比我的发际线还满了😂 你先看看能不能找别人救个急，实在不行我下周帮你看看？'
  },
  // ... 其余场景的预设话术（开发时完整填充）
};
```

### 5.4 每日金句数据

```javascript
const DAILY_QUOTES = [
  '职场最大的能力，不是会做事，而是会说话。',
  '高情商不是圆滑，而是让大家都舒服。',
  '拒绝别人最好的方式，不是说不，而是给出替代方案。',
  '会汇报的人，不一定是做得最好的，但一定是最容易被看见的。',
  '道歉不是认输，而是给关系一个修复的机会。',
  '谈薪不可耻，可耻的是不敢开口。',
  '职场中没有"不好意思"，只有"不好意思说"的代价。',
  '好聚好散，是职场人最基本的体面。',
  '向上管理的本质，是降低领导的决策成本。',
  '幽默是职场中最被低估的软技能。',
  // ... 共 30 条，按日期轮换
];
```

### 5.5 localStorage 数据结构

```javascript
// 场景使用计数
// key: 'wp_scene_counts'
// value: { "salary": 15, "refuse": 28, ... }

// 收藏的话术
// key: 'wp_favorites'
// value: [{ id, scene, tone, content, timestamp }, ...]

// 话术点赞
// key: 'wp_likes'
// value: { "hash_of_content": 5, ... }

// 用户提交的场景
// key: 'wp_user_scenes'
// value: [{ text, timestamp }, ...]

// 每日使用次数（限流用）
// key: 'wp_daily_usage'
// value: { "2025-04-15": 8, ... }
```

---

## 六、交互逻辑设计

### 6.1 场景切换逻辑

```
用户点击场景按钮
  → 高亮当前选中按钮（active 样式）
  → 更新输入框 placeholder 为该场景的引导文案
  → 显示 2-3 个示例按钮（可点击快速填入）
  → 记录场景使用次数到 localStorage
  → 更新热门排行
```

### 6.2 话术生成逻辑

```
用户点击「生成话术」
  → 校验：输入框不能为空（为空则提示）
  → 校验：检查每日使用次数是否超限（>20次则提示并降级）
  → 按钮置灰 + 显示 loading 动画
  → 构造 Prompt（场景 + 语气 + 情境）
  → 调用 MiniMax API（流式）
    → 成功：逐字展示（打字机效果）
    → 失败/超时：降级到预设话术
  → 按钮恢复 + 隐藏 loading
  → 显示操作按钮（复制/重新生成/点赞）
  → 记录使用次数
```

### 6.3 语气切换逻辑

```
用户点击语气按钮
  → 高亮当前选中语气
  → 如果已有生成内容：
    → 自动重新生成（使用当前场景 + 当前输入 + 新语气）
  → 如果没有生成内容：
    → 仅切换选中状态，等待用户点击生成
```

### 6.4 一键复制逻辑

```
用户点击「复制」
  → 读取话术展示区的纯文本内容
  → 调用 navigator.clipboard.writeText()
  → 按钮文字变为「已复制 ✓」
  → 2 秒后恢复为「📋 复制」
  → 兼容方案：clipboard API 不可用时，使用 document.execCommand('copy')
```

### 6.5 打字机效果实现

```javascript
async function typeWriter(element, text, speed = 30) {
  element.textContent = '';
  for (let i = 0; i < text.length; i++) {
    element.textContent += text[i];
    // 自动滚动到底部
    element.scrollTop = element.scrollHeight;
    await new Promise(resolve => setTimeout(resolve, speed));
  }
}
```

> 注意：如果使用流式 API，则不需要 typeWriter，直接逐 chunk 追加即可。

---

## 七、UI 设计规范

### 7.1 配色方案

| 用途 | 色值 | 说明 |
|------|------|------|
| 主色调 | `#4F46E5`（Indigo-600） | 按钮、选中态、链接 |
| 辅助色 | `#7C3AED`（Violet-600） | 渐变、强调 |
| 背景色 | `#F9FAFB`（Gray-50） | 页面背景 |
| 卡片背景 | `#FFFFFF` | 内容卡片 |
| 主文字 | `#111827`（Gray-900） | 标题、正文 |
| 辅助文字 | `#6B7280`（Gray-500） | 提示、说明 |
| 成功色 | `#10B981`（Emerald-500） | 复制成功、提交成功 |
| 警告色 | `#F59E0B`（Amber-500） | 降级提示 |

### 7.2 响应式断点

| 断点 | 宽度 | 布局调整 |
|------|------|---------|
| 手机 | < 640px | 场景按钮 2 列，单栏布局 |
| 平板 | 640px-1024px | 场景按钮 4 列，适当间距 |
| PC | > 1024px | 最大宽度 640px 居中，场景按钮 4 列 |

### 7.3 关键 UI 组件

| 组件 | 样式要点 |
|------|---------|
| 场景按钮 | 圆角卡片，hover 阴影加深，选中态主色边框 |
| 输入框 | 圆角，聚焦时主色边框，placeholder 浅灰 |
| 语气按钮 | 胶囊形，选中态主色填充，未选中灰色描边 |
| 生成按钮 | 主色渐变背景，大号圆角，hover 放大效果 |
| 话术展示区 | 白色卡片，柔和阴影，内边距充足 |
| 操作按钮 | 小号胶囊形，灰色描边，hover 主色 |

---

## 八、兼容性与性能优化

### 8.1 浏览器兼容

| 浏览器 | 兼容策略 |
|--------|---------|
| Chrome 80+ | 完全支持（fetch、clipboard API、async/await） |
| Safari 14+ | 完全支持（需注意 clipboard API 需要 HTTPS） |
| 微信内置浏览器 | 基本支持（部分 CSS 动画降级） |
| Firefox 75+ | 完全支持 |

### 8.2 性能优化

| 优化项 | 方案 |
|--------|------|
| 首屏加载 | Tailwind CSS CDN + Google Fonts 预加载 |
| API 响应 | 流式输出，首 token 即展示 |
| 动画性能 | 使用 CSS transform/opacity，避免触发重排 |
| 内存管理 | localStorage 数据定期清理（>100条收藏时提醒） |
| 网络优化 | API 调用设置 5 秒超时，避免长时间等待 |

### 8.3 前端限流

```javascript
function checkDailyLimit() {
  const today = new Date().toISOString().split('T')[0];
  const usage = JSON.parse(localStorage.getItem('wp_daily_usage') || '{}');
  const count = usage[today] || 0;
  return count < 20; // 每人每天最多 20 次 AI 生成
}
```

---

## 九、开发风险点与规避方案

| 风险 | 概率 | 影响 | 规避方案 |
|------|------|------|---------|
| MiniMax API CORS 限制 | 中 | 前端无法直接调用 | 优先测试；若 CORS 被拒，使用 MiniMax 原生接口（`/v1/text/chatcompletion`）或搭建简单代理 |
| API Key 在前端暴露 | 高 | 被他人盗用消耗额度 | Base64 编码 + 前端每日限流 20 次 + 底部声明 |
| 流式输出兼容性 | 低 | 部分浏览器不支持 ReadableStream | 降级为非流式请求 + 打字机效果模拟 |
| localStorage 容量限制 | 低 | 数据过多导致存储失败 | 收藏上限 100 条，定期清理旧数据 |
| Tailwind CDN 加载慢 | 低 | 首屏样式闪烁 | 添加内联关键 CSS 作为加载占位 |
| 手机端键盘遮挡 | 中 | 输入框被键盘遮挡 | 生成按钮在输入框上方，避免在底部 |

---

## 十、API 调用测试方案

开发前需验证以下内容：

1. **API 连通性**：使用提供的 API Key 发送测试请求，确认可正常返回
2. **流式输出**：确认 `stream: true` 时返回 SSE 格式数据
3. **CORS 策略**：确认前端 fetch 是否被 CORS 拦截
4. **模型名称**：确认 `MiniMax-M2.7` 是否为正确的模型标识

```javascript
// 测试脚本（开发前执行）
async function testAPI() {
  const res = await fetch('https://api.minimax.chat/v1/chat/completions', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': 'Bearer sk-api-...'
    },
    body: JSON.stringify({
      model: 'MiniMax-M2.7',
      messages: [{ role: 'user', content: '你好，测试一下' }],
      max_tokens: 50
    })
  });
  const data = await res.json();
  console.log('API 测试结果:', data);
}
```

---

> 架构师：AI 资深前端架构师
> 设计日期：2025-04-15
> 文档版本：V1.0
