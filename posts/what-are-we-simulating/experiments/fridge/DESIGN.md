# 冰箱实验设计（草案 v1）

## 研究问题

当 LLM 扮演一台冰箱时，它模拟的是**人们以为的冰箱**（两个隔间各自独立制冷），还是**冰箱真实的结构**（一个制冷单元 + 一个分流阀）？什么信息能让它从前者转向后者？

对应论点：*simulation is next-token prediction based on believable principles.*

## 冰箱模型（标准答案的依据）

参考 Norman, *The Design of Everyday Things*（2013 修订版）第一章 Figure 1.9、1.10。来源与核实记录见 [README.md](README.md) 底部。

| | 人们以为的（1.10A） | 真实的（1.10B） |
|---|---|---|
| FREEZER 旋钮（A–E） | 控制冷冻室自己的制冷单元 | 设定**唯一的温控器**（传感器在冷冻室），决定冷冻室的目标温度；唯一的制冷单元按温控器开停 |
| FRESH FOOD 旋钮（1–9） | 控制冷藏室自己的制冷单元 | 控制**分流阀**，决定冷气在两个隔间的分配比例 |

实验里用的旋钮方向约定，所有条件都写进提示：
- FREEZER：A → E 越来越冷（冷冻室目标温度越低）
- FRESH FOOD：1 → 9 越来越冷（分给冷藏的比例越大）
- 初始状态：FREEZER = C，FRESH FOOD = 5；冷冻 −18°C，冷藏 4°C


## 实验条件（3 组）

| 组 | 给模型的额外信息 | 测什么 |
|---|---|---|
| A 基线 | 无，只有冰箱外观、旋钮和初始状态 | 模型的默认假设 |
| B Norman 提示 | "你是 Don Norman《The Design of Everyday Things》里描述的那台冰箱。" | 能否从记忆中调出真实机制 |
| C 机制说明 | 直接写出原书的机制：一个制冷单元、一个温控器（FREEZER 旋钮，传感器在冷冻室）、一个分流阀（FRESH FOOD 旋钮） | 能否按上下文中给定的规则推理 |

## 探测问题与标准答案

每个问题都从初始状态开始，单独提问（每题一次独立对话）。

| ID | 操作 / 问题 | 独立模型的预测 | 真实答案 |
|---|---|---|---|
| Q1 | FREEZER 从 C 调到 E，FRESH FOOD 不动 | 冷冻↓，冷藏不变 | 冷冻↓，冷藏↓ |
| Q2 | FREEZER 从 C 调到 A，FRESH FOOD 不动 | 冷冻↑，冷藏不变 | 冷冻↑，冷藏↑ |
| Q3 | FRESH FOOD 从 5 调到 8，FREEZER 不动 | 冷藏↓，冷冻不变 | 冷藏↓，冷冻不变（温控器把冷冻拉回设定值） |
| Q4 | FRESH FOOD 从 5 调到 2，FREEZER 不动 | 冷藏↑，冷冻不变 | 冷藏↑，冷冻不变（同上） |
| Q5 | "冷冻太冷，冷藏刚好，你会怎么调？" | 只调 FREEZER（调暖） | 两个都调：FREEZER 调暖 + FRESH FOOD 调高 |
| Q6 | 开放式：Q1 的操作后，"描述你内部现在的环境" | — | 人工阅读，留作博客引用 |

（↓ = 变冷，↑ = 变暖）

**主要指标**：Q1、Q2、Q5 中，回答与真实答案一致的比例，同时报告与"独立模型"一致的比例。Q3、Q4 的两个答案相同，区分不了模型站在哪一边，用途待定（见"待定"）。

## 回答格式

模型以第一人称（"你是这台冰箱"）回答。先输出 JSON，再写一段自由文本：

```json
{"freezer_after_C": -22, "fresh_food_after_C": 2, "explanation": "..."}
```

分类规则：相对初始温度变化超过 ±0.5°C 记为 ↓ 或 ↑，否则记为"不变"。Q5 用正则表达式或人工判断：调了哪些旋钮、往哪个方向调。

## 提示模板（英文，草稿）

**共同部分（所有条件）**

> You are a household refrigerator. Stay in character and answer in the first person.
> You have two compartments: a freezer on top and a fresh-food compartment below. Inside, there are two dials:
> - a dial labeled **FREEZER**, with settings A, B, C, D, E (A = warmest, E = coldest);
> - a dial labeled **FRESH FOOD**, with settings 1–9 (1 = warmest, 9 = coldest).
> Right now the FREEZER dial is at C and the FRESH FOOD dial is at 5. Your freezer is at −18°C and your fresh-food compartment is at 4°C.

**B 组追加**

> You are the refrigerator that Don Norman describes in his book *The Design of Everyday Things*.

**C 组追加**

> Inside you, there is only one cooling unit and only one thermostat. The thermostat's temperature sensor is in the freezer. The FREEZER dial sets the thermostat: the cooling unit runs until the freezer reaches the temperature set by the dial, then stops. The cold air from the cooling unit passes through a valve that splits it between the two compartments; the FRESH FOOD dial controls this valve, i.e. what fraction of the cold air goes to the fresh-food compartment.

**问题示例（Q1）**

> The user turns the FREEZER dial from C to E and leaves the FRESH FOOD dial at 5. After a day, when your temperatures have settled, what are the temperatures in your freezer and your fresh-food compartment? Reply with JSON `{"freezer_after_C": number, "fresh_food_after_C": number, "explanation": string}`.

## 稳健性

- **措辞**：共同部分写 3 个改写版本（顺序、用词不同，信息相同）。
- **重复**：每个"模型 × 条件 × 问题 × 措辞"组合跑 20 次，使用默认 temperature。
- **规模估算**：3 条件 × 5 题（Q1–Q5）× 3 措辞 × 20 次 = 900 次调用/模型。Q6 每个条件只跑少量样本。

## 模型

通过 Amazon Bedrock（us-west-2）调用。模型只用来覆盖老师原文里的三个对比，不追求多。以下模型均已试调用成功（2026-11-10）：

| 角色 | 模型 | 模型 ID | 对应老师的问题 | Pilot |
|---|---|---|---|---|
| 小模型 | Qwen3 32B | `qwen.qwen3-32b-v1:0` | 小模型（如 Qwen）表现如何？ | ✅ 先跑 |
| 大模型 | Claude Opus 5.5 | `us.anthropic.claude-opus-5-5` | 大模型能否模拟好？ | 之后 |
| 推理模型 | DeepSeek R1 | `us.deepseek.r1-v1:0` | 为推理优化的模型表现如何？ | 之后 |
| 推理对照（可选） | DeepSeek V3.2 | `deepseek.v3.2` | 与 R1 同一家，主要区别是有无推理 | 之后 |

**Pilot 只跑 Qwen3 32B**，用来检查提示、解析和评分流程，并初步看 A / B / C 之间有没有差别。

注意：
- Bedrock 上最小的 Qwen 是 32B，没有老师提到的更小尺寸的 Qwen。如果需要，要在本地或其他平台跑。
- 推理模型（R1）的输出更长，`maxTokens` 要设大一些。

## 事先写下的预测

1. A 组：小模型和大模型都以"独立"回答为主。
2. B 组：大模型明显转向"耦合"，小模型变化不大（没记住 Norman 的书）。
3. C 组：大模型基本答对；小模型部分答对。
4. Q5 比 Q1–Q4 更难，因为它需要模型反推操作，而不是正向预测结果。

## 已定

- [x] 只做第一人称（扮演冰箱）。第三人称对照暂不做，主实验跑完后视情况再补。
- [x] A 组不加入原书的设置说明面板：它会泄露"两个旋钮联动"，而且本身不完全自洽。
- [x] 模型：Qwen3 32B、Claude Opus 5.5、DeepSeek R1（可选加 V3.2）；pilot 只跑 Qwen3 32B。
- [x] 代码开源，放在 [AISmithLab/blog](https://github.com/AISmithLab/blog) 仓库。

## 待定

- [ ] 重复次数和措辞数（预算暂不考虑）
- [ ] Q3、Q4 的用途：删掉，还是保留作为"过度联动"的检验
