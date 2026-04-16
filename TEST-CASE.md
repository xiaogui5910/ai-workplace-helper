# AI 职场嘴替工具箱 - 测试用例文档

> 测试对象：`index.html`（1971行）
> 测试日期：2026-04-16
> 测试方法：代码路径追踪 + 静态分析

---

## A. 全功能场景（20项）

| 编号 | 模块 | 测试点 | 前置条件 | 操作步骤 | 预期结果 | 实际结果 | 状态 |
|------|------|--------|----------|----------|----------|----------|------|
| A01 | SceneModule | 页面加载-默认场景选中 | 页面首次加载 | 打开页面，观察场景按钮区域 | `init()` 调用 `SceneModule.selectScene(SCENES[0])`，第一个场景（谈薪沟通）按钮高亮，placeholder 更新为谈薪沟通的示例 | 代码路径正确：`init()` 末尾调用 `SceneModule.selectScene(SCENES[0])`，会设置 `currentScene = SCENES[0]`，更新按钮高亮、placeholder、示例按钮 | PASS |
| A02 | QuoteModule | 页面加载-金句横幅显示 | 页面首次加载 | 打开页面，观察顶部横幅 | 根据 `dayOfYear % 30` 选择金句，横幅显示金句文本，带渐入动画 | 代码路径正确：`QuoteModule.init()` 计算 `dayOfYear`，取模选择金句，设置 `$quoteText.textContent` | PASS |
| A03 | SceneModule | 场景切换-点击预置场景 | 已选中默认场景 | 点击"离职沟通"场景按钮 | 1. 原场景取消高亮 2. 新场景高亮并播放 scale 动画 3. placeholder 更新 4. 示例按钮更新 | 代码路径正确：`selectScene()` 检查 `isGenerating`（false），设置 `currentScene`，更新 UI | PASS |
| A04 | SceneModule | 场景切换-自定义场景创建 | 页面已加载 | 1. 点击"+自定义场景" 2. 输入"和老板谈远程办公" 3. 点击确定 | 1. 输入框展开 2. 创建自定义场景对象（id='custom_xxx'） 3. 切换到该场景 4. 按钮显示场景名 | 代码路径正确：`$confirmCustomScene.click` 创建 `{id:'custom_'+Date.now(), ...}`，调用 `selectScene(customScene)` | PASS |
| A05 | SceneModule | 场景切换-自定义场景取消 | 自定义场景输入框已展开 | 1. 输入部分文字 2. 点击"取消" | 1. 输入框隐藏 2. "+自定义场景"按钮恢复显示 3. 输入框内容清空 | 代码路径正确：`$cancelCustomScene.click` 隐藏输入框、显示 toggle、清空 value | PASS |
| A06 | SceneModule | 场景切换-自定义场景为空确认 | 自定义场景输入框已展开 | 不输入任何内容，直接点击"确定" | 1. 不创建场景 2. 输入框获得焦点（提示用户输入） | 代码路径正确：`if (!name)` 为 true，执行 `$customSceneName.focus()` 后 return | PASS |
| A07 | InputModule | 输入区-手动输入情境 | 已选中场景 | 在 textarea 中手动输入文字 | 1. 文字正常显示 2. 字数统计实时更新 | 代码路径正确：`input` 事件监听触发 `updateCharCount()`，更新 `$charCount.textContent` | PASS |
| A08 | InputModule | 输入区-示例按钮填入 | 已选中场景 | 点击任一示例按钮 | 1. textarea 填入示例文本 2. 字数统计更新 3. textarea 获得焦点 | 代码路径正确：`btn.click` 设置 `$situationInput.value = text`，调用 `updateCharCount()`，`focus()` | PASS |
| A09 | InputModule | 输入区-字数统计(0/500) | 页面已加载 | 清空 textarea | 字数显示 "0/500"，颜色为灰色 | 代码路径正确：`updateCharCount()` 中 `len=0`，`0/500`，不满足 `>450` 条件，不添加红色样式 | PASS |
| A10 | InputModule | 输入区-字数上限(500字) | 页面已加载 | 输入500个字符 | 1. 字数显示 "500/500" 2. 超过450字时字数变红 3. maxlength=500 阻止继续输入 | 代码路径正确：HTML `maxlength="500"` 限制输入，`len=500 > 450` 添加红色样式 | PASS |
| A11 | ToneModule | 语气切换-温和 | 页面已加载 | 点击"温和"语气按钮 | 温和按钮高亮（indigo背景白色文字），播放 scale 动画 | 代码路径正确：`selectTone()` 检查 `isGenerating`（false），更新按钮样式 | PASS |
| A12 | ToneModule | 语气切换-坚定 | 页面已加载 | 点击"坚定"语气按钮 | 坚定按钮高亮，其他按钮恢复默认样式 | 代码路径正确：同 A11 | PASS |
| A13 | ToneModule | 语气切换-简洁 | 页面已加载 | 点击"简洁"语气按钮 | 简洁按钮高亮 | 代码路径正确：同 A11 | PASS |
| A14 | ToneModule | 语气切换-幽默 | 页面已加载 | 点击"幽默"语气按钮 | 幽默按钮高亮 | 代码路径正确：同 A11 | PASS |
| A15 | AIModule | 生成话术-正常流程 | 已选场景、已输入情境、网络正常 | 点击"生成话术"按钮 | 1. 按钮变为"取消生成" 2. 结果区显示打字光标 3. 流式接收文本 4. 完成后恢复按钮、启用复制/重新生成/点赞 5. 限流计数+1 | 代码路径正确：`generate()` 检查 `isGenerating`（false），设置 `isGenerating=true`，递增 `requestId`，保存快照，调用 `streamGenerate()`，onDone 回调恢复状态 | PASS |
| A16 | CopyModule | 复制功能-正常复制 | 已生成话术结果 | 点击"复制"按钮 | 1. 优先使用 `navigator.clipboard.writeText` 2. 成功后按钮显示"已复制 ✓" 3. 2秒后恢复 | 代码路径正确：`copy()` 检查 `currentResult` 非空，尝试三级复制策略 | PASS |
| A17 | AIModule | 重新生成-成功后重新生成 | 已生成话术结果 | 点击"重新生成"按钮 | 1. 清空旧结果 2. 重新调用 `generate()` 3. 生成新话术 | 代码路径正确：`$regenerateBtn.click` 直接调用 `this.generate()` | PASS |
| A18 | LikeModule | 点赞-点赞/取消 | 已生成话术结果 | 1. 点击"点赞" 2. 再次点击 | 1. 首次点击：按钮变红，显示"已赞"，计数+1 2. 再次点击：恢复默认，计数-1（最小0） | 代码路径正确：`toggle()` 获取当前状态，切换 liked，更新 localStorage 和 UI | PASS |
| A19 | StatsModule | 热门排行-生成后显示 | 已生成话术结果 | 生成话术后，观察热门场景区域 | 1. 排行区域显示 2. 当前场景排在第一位 3. 显示使用次数 | 代码路径正确：`onDone` 回调调用 `StatsModule.record(sceneSnapshot.id)`，500ms 防抖后 `render()` | PASS |
| A20 | SuggestModule | 场景征集-提交建议 | 页面已加载 | 1. 在征集输入框输入"和甲方催款" 2. 点击"提交建议" | 1. localStorage 保存建议 2. 输入框清空 3. 显示 Toast "感谢建议！" 4. 建议数量更新 | 代码路径正确：`submit()` 验证非空、<=100字，push 到数组，保存 localStorage | PASS |

---

## B. 异常场景（10项）

| 编号 | 模块 | 测试点 | 前置条件 | 操作步骤 | 预期结果 | 实际结果 | 状态 |
|------|------|--------|----------|----------|----------|----------|------|
| B01 | AIModule | 空输入点击生成 | 已选场景，textarea 为空 | 点击"生成话术"按钮 | 1. 不发起 API 请求 2. textarea 获得焦点 3. 显示红色边框 1.5秒 4. `isGenerating` 恢复 false | 代码路径正确：`generate()` 中 `!userInput` 为 true，`isGenerating=false`，focus + 红色边框 | PASS |
| B02 | AIModule | 极短输入(1字)生成 | 已选场景 | 输入1个字，点击生成 | 1. 正常发起 API 请求 2. 生成话术（内容可能较短） | 代码路径正确：`userInput.length >= 1`，通过空值校验，正常调用 API | PASS |
| B03 | AIModule | 超长输入(500字)生成 | 已选场景 | 输入500字，点击生成 | 1. 正常发起 API 请求 2. 生成话术 | 代码路径正确：`maxlength=500` 限制输入，`userInput` 非空，正常调用 API | PASS |
| B04 | SceneModule | 特殊字符场景名(!@#$%^&*()) | 页面已加载 | 创建自定义场景，输入名称"!@#$%^&*()" | 1. 场景创建成功 2. 按钮显示特殊字符名称 3. 使用 `textContent` 赋值，无 XSS 风险 | 代码路径正确：`name = $customSceneName.value.trim()` 非空，创建场景对象，`textContent` 赋值安全 | PASS |
| B05 | SuggestModule | 征集-空内容提交 | 征集输入框为空 | 不输入内容，点击"提交建议" | 1. 不保存 2. 输入框获得焦点 | 代码路径正确：`submit()` 中 `!text` 为 true，`$suggestInput.focus()` 后 return | PASS |
| B06 | SuggestModule | 征集-超长内容提交(>100字) | 页面已加载 | 输入101字，点击提交 | 1. 不保存 2. 显示 Toast "建议内容不能超过100字" | 代码路径正确：`text.length > 100` 为 true，Toast 提示后 return | PASS |
| B07 | SuggestModule | 征集-重复提交 | 已提交过一条建议 | 再次提交相同内容 | 1. 保存成功（无去重校验） 2. 建议数量+1 | 代码路径正确：`submit()` 无去重逻辑，直接 push 到数组。**注意：这是设计选择，非 BUG** | PASS |
| B08 | SceneModule | 自定义场景-超长名称(30字) | 页面已加载 | 输入30字场景名，点击确定 | 1. 场景创建成功 2. HTML `maxlength="30"` 限制最多30字 | 代码路径正确：`maxlength="30"` 阻止超过30字输入 | PASS |
| B09 | SceneModule | 自定义场景-特殊字符名称 | 页面已加载 | 输入含 `<script>` 的场景名 | 1. 场景创建成功 2. 无 XSS 执行（使用 textContent） | 代码路径正确：所有 DOM 赋值使用 `textContent`，无 innerHTML 注入风险 | PASS |
| B10 | RateLimitModule | 限流-达到20次上限 | 已生成20次 | 点击"生成话术"按钮 | 1. 不发起请求 2. 显示 Toast "今日免费次数已用完" 3. 按钮显示"今日次数已用完"并 disabled | 代码路径正确：`check()` 中 `count >= 20` 为 true，Toast 提示，返回 false，`generate()` 设置按钮 disabled | PASS |

---

## C. 边界场景（8项）

| 编号 | 模块 | 测试点 | 前置条件 | 操作步骤 | 预期结果 | 实际结果 | 状态 |
|------|------|--------|----------|----------|----------|----------|------|
| C01 | RateLimitModule | 限流数据异常-count为负数 | localStorage 中 count=-5 | 点击生成 | 1. `getDailyCount()` 返回 0（负数归零） 2. 正常生成 | 代码路径正确：`Math.max(0, Math.min(-5, 20))` = 0 | PASS |
| C02 | RateLimitModule | 限流数据异常-count为999 | localStorage 中 count=999 | 点击生成 | 1. `getDailyCount()` 返回 20（截断到上限） 2. 限流触发 | 代码路径正确：`Math.max(0, Math.min(999, 20))` = 20，`check()` 返回 false | PASS |
| C03 | RateLimitModule | 限流数据异常-count为非数字 | localStorage 中 count="abc" | 点击生成 | 1. `typeof data.count === 'number'` 为 false，返回 0 2. 正常生成 | 代码路径正确：`typeof "abc" === 'number'` 为 false，`count = 0` | PASS |
| C04 | 全局 | localStorage被禁用 | 浏览器禁用 localStorage | 执行任意操作 | 1. 所有 try-catch 静默降级 2. 页面功能不受影响 3. 限流/统计/点赞数据不持久化 | 代码路径正确：所有 localStorage 操作都有 try-catch，`getStats()`/`getLikes()`/`getDailyCount()` 在 catch 中返回默认值 | PASS |
| C05 | LikeModule | 点赞key-超长话术(1000字)截断 | 已生成1000字话术 | 点击点赞 | 1. `getLikeKey()` 取前20字作为 key 2. 正常点赞 | 代码路径正确：`text.substring(0, 20)` 截断，key 格式 `sceneId_toneId_前20字` | PASS |
| C06 | StatsModule | 排行数据-自定义场景不显示 | 自定义场景已使用 | 查看热门排行 | 1. 自定义场景（id 以 'custom_' 开头）不显示在排行中 | 代码路径正确：`render()` 中 `SCENES.find(s => s.id === sceneId)` 对 custom 场景返回 undefined，`if (!scene) return` 跳过 | PASS |
| C07 | RateLimitModule | 限流-次日自动重置 | localStorage 中日期为昨天 | 点击生成 | 1. `getDailyCount()` 检测日期不匹配，返回 0 2. 正常生成 | 代码路径正确：`data.date !== today` 时返回 0 | PASS |
| C08 | InputModule | 字数统计-中英文混合 | 页面已加载 | 输入"Hello你好World世界" | 字数显示 "12/500"（JS `string.length` 按字符计数） | 代码路径正确：`$situationInput.value.length` = 12（每个中文字算1，每个英文字母算1） | PASS |

---

## D. 稳定性场景（10项）

| 编号 | 模块 | 测试点 | 前置条件 | 操作步骤 | 预期结果 | 实际结果 | 状态 |
|------|------|--------|----------|----------|----------|----------|------|
| D01 | CancelModule | 生成中取消(无内容) | 正在生成，尚未收到任何 chunk | 点击"取消生成"按钮 | 1. `abortController.abort()` 2. `isGenerating=false` 3. 显示"已取消生成"灰色文字 4. 复制/重新生成按钮保持 disabled | 代码路径正确：`cancel()` 调用 `abort()`，`isGenerating=false`，`currentResult` 为空，显示取消提示 | PASS |
| D02 | CancelModule | 生成中取消(有部分内容) | 正在生成，已收到部分 chunk | 点击"取消生成"按钮 | 1. 生成停止 2. 保留已生成内容 3. 启用复制/重新生成/点赞按钮 | 代码路径正确：`cancel()` 中 `currentResult` 非空，启用所有操作按钮 | PASS |
| D03 | SceneModule | 生成中切换场景(应被拦截) | 正在生成中 | 点击其他场景按钮 | 1. 场景不切换 2. 生成不受影响 | 代码路径正确：`selectScene()` 开头 `if (isGenerating) return` | PASS |
| D04 | ToneModule | 生成中切换语气(应被拦截) | 正在生成中 | 点击其他语气按钮 | 1. 语气不切换 2. 生成不受影响 | 代码路径正确：`selectTone()` 开头 `if (isGenerating) return` | PASS |
| D05 | AIModule | 生成中点击重新生成(按钮disabled) | 正在生成中 | 点击"重新生成"按钮 | 1. 按钮为 disabled 状态 2. 点击无响应 | 代码路径正确：`setGeneratingState(true)` 中 `$regenerateBtn.disabled = true` | PASS |
| D06 | CopyModule | 生成中点击复制(按钮disabled) | 正在生成中 | 点击"复制"按钮 | 1. 按钮为 disabled 状态 2. 点击无响应 | 代码路径正确：`setGeneratingState(true)` 中 `$copyBtn.disabled = true` | PASS |
| D07 | LikeModule | 生成中点击点赞(按钮disabled) | 正在生成中 | 点击"点赞"按钮 | 1. 按钮为 disabled 状态 2. 点击无响应 | 代码路径正确：`setGeneratingState(true)` 调用 `LikeModule.setEnabled(false)`，`$likeBtn.disabled = true` | PASS |
| D08 | AIModule | 生成中刷新页面 | 正在生成中 | 刷新页面（F5） | 1. 页面重新加载 2. 所有状态重置 3. `isGenerating=false` 4. 按钮恢复默认 | 代码路径正确：页面刷新重置所有 JS 变量，`isGenerating` 重置为 false | PASS |
| D09 | ToneModule | 快速连续切换多个语气 | 页面已加载，无生成内容 | 快速依次点击 温和->坚定->简洁->幽默 | 1. 最终选中"幽默" 2. 每次切换更新高亮 3. 无报错 | 代码路径正确：`selectTone()` 每次更新 `currentTone` 和 UI，无竞态问题（同步操作） | PASS |
| D10 | FallbackModule | 降级打字机进行中点击重新生成 | 降级打字机效果进行中 | 点击"重新生成"按钮 | 1. 旧打字机定时器被清除（BUG#11修复） 2. 新的生成流程开始 | 代码路径正确：`generate()` 开头清除 `FallbackModule._typewriterTimer`，然后开始新流程 | PASS |

---

## E. 并发/重复操作场景（6项）

| 编号 | 模块 | 测试点 | 前置条件 | 操作步骤 | 预期结果 | 实际结果 | 状态 |
|------|------|--------|----------|----------|----------|----------|------|
| E01 | AIModule | 连续点击生成按钮2次 | 页面就绪 | 快速连续点击"生成话术"2次 | 1. 只发起1次 API 请求 2. 第二次点击被 `isGenerating` 拦截 | 代码路径正确：第一次点击 `isGenerating=true`，第二次 `if (isGenerating) return` 拦截 | PASS |
| E02 | AIModule | 连续点击生成按钮5次 | 页面就绪 | 快速连续点击"生成话术"5次 | 1. 只发起1次 API 请求 | 代码路径正确：同 E01，`isGenerating` 锁机制 | PASS |
| E03 | CancelModule | 极快速点击生成+取消交替 | 页面就绪 | 快速交替点击 生成->取消->生成->取消 | 1. 最终状态一致 2. 无残留定时器 3. 无报错 | **修复前FAIL**：`streamGenerate` 用户取消分支未校验 `requestId`，旧回调可干扰新请求状态。**修复后PASS**（BUG#24）：添加 `requestId` 校验，旧回调被正确忽略 | PASS(修复后) |
| E04 | ToneModule | 语气切换触发生成中再次切换 | 已有生成结果 | 切换语气（触发生成），生成中再次切换语气 | 1. 第二次切换被 `isGenerating` 拦截 2. 第一次生成正常完成 | 代码路径正确：`selectTone()` 中 `if (isGenerating) return` 拦截第二次切换 | PASS |
| E05 | AIModule | 取消后立即重新生成 | 生成中，有部分内容 | 点击取消，立即点击重新生成 | 1. 取消完成 2. 新的生成正常开始 3. 旧内容被清空 | **修复前FAIL**：旧请求的 catch 回调和 onChunk 回调未校验 `requestId`，可破坏新请求状态。**修复后PASS**（BUG#24+BUG#27）：添加 `requestId` 校验 | PASS(修复后) |
| E06 | FallbackModule | 降级中再次触发生成 | 降级打字机效果进行中 | 点击"生成话术"按钮 | 1. 旧打字机定时器被清除 2. 新流程开始（可能再次降级） | 代码路径正确：`generate()` 中 `if (FallbackModule._typewriterTimer)` 清除定时器 | PASS |

---

## F. 断网/超时/API错误场景（6项）

| 编号 | 模块 | 测试点 | 前置条件 | 操作步骤 | 预期结果 | 实际结果 | 状态 |
|------|------|--------|----------|----------|----------|----------|------|
| F01 | NetworkModule | 断网时点击生成→直接降级 | 网络已断开（`navigator.onLine=false`） | 点击"生成话术"按钮 | 1. 不发起 API 请求 2. 直接走降级逻辑 3. 显示离线话术（打字机效果） 4. 不消耗限流配额（BUG#19修复） | 代码路径正确：`generate()` 中 `NetworkModule.isOffline` 为 true，走降级分支，注释 `RateLimitModule.increment()` 已移除 | PASS |
| F02 | NetworkModule | 断网→恢复→再次生成 | 断网后网络恢复 | 1. 断网时生成（降级） 2. 恢复网络 3. 再次生成 | 1. 断网时降级 2. 恢复时显示绿色提示条 3. 再次生成走 API 正常流程 | 代码路径正确：`online` 事件触发 `showOnline()`，`isOffline=false`，后续 `generate()` 不走降级分支 | PASS |
| F03 | AIModule | API超时(5秒)→降级 | 网络正常但 API 响应慢 | 点击生成，等待5秒 | 1. 5秒后 `abortController.abort()` 2. `timeoutTriggered=true` 3. 检查 `filteredResult.length < 20` 则清空 4. 走 onError 降级 | 代码路径正确：`setTimeout` 5秒后 abort，catch 中判断 `timeoutTriggered`，过滤 think 标签后检查长度 | PASS |
| F04 | ErrorMapModule | API返回401错误→降级 | API Key 无效 | 点击生成 | 1. `response.ok` 为 false 2. 抛出包含 "401" 的错误 3. `ErrorMapModule` 匹配 "401" 返回 "API认证失败" 4. 降级到预设话术 | 代码路径正确：`!response.ok` 抛出错误，onError 中 `getFriendlyMessage()` 匹配 pattern "401" | PASS |
| F05 | ErrorMapModule | API返回429错误→降级 | API 限流 | 点击生成 | 1. 抛出包含 "429" 的错误 2. 匹配 "429" 返回 "请求过于频繁" 3. 降级 | 代码路径正确：同 F04，匹配 pattern "429" | PASS |
| F06 | NetworkModule | 网络波动-断连恢复多次切换 | 网络不稳定 | 1. 断网 2. 恢复 3. 断网 4. 恢复 5. 生成 | 1. 每次状态切换正确 2. 最终生成时根据当前网络状态决定走 API 还是降级 | 代码路径正确：`offline`/`online` 事件正确更新 `isOffline`，`generate()` 读取最新状态 | PASS |
