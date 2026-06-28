# 终端 Agent 中控

本页描述一种可选工作流：用 Playwright 控制已经登录的网页 AI，让多个网页模型在一个终端里协作。

它不是官方 API，也不是稳定的 SDK。网页结构改变、登录状态变化、模型选择器变化，都可能导致选择器需要调整。

## 目标

支持两类协作方式：

- 主控模式：ChatGPT 作为 coordinator，Gemini / DeepSeek / Zhipu 作为 worker agent。
- 会商模式：多个网页模型像聊天室一样平等讨论，再整理共识。

## 推荐命令设计

```text
直接输入文字                 和 ChatGPT 聊天；ChatGPT 可按需调用 Gemini
/主控 任务内容               ChatGPT 当 coordinator，其他模型当 worker agent
/会商 2 任务内容             多个模型平等讨论 2 轮
/all 任务内容                多模型平等会商 1 轮

/gpt 你好                   只发给 ChatGPT
/gemini 你好                只发给 Gemini
/ds 你好                    只发给 DeepSeek
/zhipu 你好                 只发给 Zhipu

/models                     查看当前网页模型识别结果
/reset                      关闭模型页签，重新打开干净页面
/read path                  读取本地文件内容到终端查看
/help                       显示帮助
/quit                       退出
```

中文命令可以按需增加，例如 `/清场`、`/全部`、`/智谱`。

## 主控模式

主控模式适合大多数工作任务。

流程：

```text
1. ChatGPT 拆解用户任务。
2. ChatGPT 给 Gemini / DeepSeek / Zhipu 分派 worker 任务。
3. 每个 worker 只执行分派给自己的部分。
4. ChatGPT 审查 worker 结果。
5. 如有必要，ChatGPT 追问某个 worker 一轮。
6. ChatGPT 输出最终结论。
```

推荐 prompt 结构：

```text
你是多模型 agent 工作流的 coordinator（负责人）。
用户任务：...

请先拆解任务，并给 Gemini、DeepSeek、Zhipu 三个 worker agent 分派工作。
输出：
1. 总目标
2. 关键约束
3. 给 Gemini 的任务
4. 给 DeepSeek 的任务
5. 给 Zhipu 的任务
6. 期望返回格式
```

Worker prompt：

```text
你是多模型工作流里的 worker agent：{name}。
用户原始任务：...
Coordinator 的任务分派：...

请只执行分派给你的那部分工作。
输出：
- 结论
- 依据
- 风险/不确定点
- 建议的下一步
- 是否需要其他 agent 补充
```

## 会商模式

会商模式适合少数需要多视角碰撞的问题，例如架构争议、产品方向、复杂取舍。

流程：

```text
1. GPT 发言。
2. Gemini 阅读前面发言并回应。
3. DeepSeek 阅读前面发言并回应。
4. Zhipu 阅读前面发言并回应。
5. 重复指定轮数。
6. 最后由一个模型整理共识、分歧和行动建议。
```

建议限制轮数，默认 1 到 3 轮，最多不要超过 10 轮，避免网页端长时间生成或触发额度限制。

## 备用账号

如果同一网页模型有多个付费账号，建议懒加载备用账号：

```text
1. 启动时只打开主账号。
2. 检测到额度/限制提示后，再打开备用账号。
3. 将同一条消息发送给备用账号。
```

常见检测词：

```text
quota
limit
rate
try again later
too many
capacity
额度
上限
限制
稍后
```

网页端没有稳定的额度 API，因此只能做启发式检测。

## 模型识别

终端可以从网页文本里启发式识别当前模型，例如：

```text
Gemini Pro
Gemini Flash
DeepSeek R1
DeepSeek V3
GLM
GPT-4o
```

如果检测到 Gemini Flash，建议提示用户确认是否已经从 Pro 降级。

## 安全边界

- 不要自动关闭用户正在使用的普通浏览器窗口。
- 不要把真实 profile 名、账号名、token、临时公网域名提交到公开仓库。
- 不要默认打开所有备用账号；备用账号应按需打开。
- 不要承诺网页自动化 100% 稳定。

## 限制

- 网页 DOM 改版会导致输入框、发送按钮、回复区域选择器失效。
- 不同账号之间没有天然共享上下文，需要脚本把必要摘要传过去。
- 网页端模型切换、额度限制、登录弹窗都只能通过页面文本或 UI 状态推断。
