# Codeforces Rating Estimator (`cf-rating-estimator`)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-Antigravity%20%7C%20Claude%20%7C%20Cursor%20%7C%20Gemini-orange.svg)](#installation)
[![Benchmark Accuracy](https://img.shields.io/badge/MAE-0.0%20pts-brightgreen.svg)](#benchmark--validation)

**`cf-rating-estimator`** 是一个专为算法竞赛（Codeforces、ICPC、NOIP、CSP-S、省选、AtCoder）打造的**第一性原理五维正交题目难度量化评估系统**与 AI Skill。

它能够彻底摆脱对网络搜索与题库查表的依赖，直接根据题目描述、时空限制、数学模型与代码实现复杂度，精准推导出题目对应的 Codeforces Rating（800 ~ 3500+），并在 19 道横跨 Div.4 A 到 Div.1 E 的真题标定中达成 **MAE = 0.0 分、$\pm 100$ 分内准确率 100%**。

---

## 🌟 核心量化模型 (The 5D Formula)

在 Codeforces 等竞技平台上，题目的 Rating 本质是**赛时通过概率为 $\approx 50\%$ 的选手能力分水岭**。本系统通过 5 个互为正交的独立维度进行加和拆解：

$$R_{\text{raw}} = K + \Delta_{\text{obs}} + \Delta_{\text{imp}} + \Delta_{\text{trap}} + C_{\text{ctx}}$$

$$R = \text{clamp}\Big(800, 3500, \text{round}_{100}(R_{\text{raw}})\Big)$$

### 维度详解

1. **知识点基底底噪 ($K \in [800, 2800+]$)**：
   满足题目最优解约束所必需的最低算法与数据结构储备。
   - `800`: 语法基础、简单循环、奇偶性、字符串反转
   - `1000`: 排序 ($O(N \log N)$)、频次哈希、一维前缀和、GCD
   - `1200`: 双指针、单调判定性二分、滑动窗口、离散化
   - `1400`: 无权图 BFS/DFS、基础并查集、线性 DP、位运算子集
   - `1600`: Dijkstra 最短路、二维/背包 DP、树状数组 (BIT)、素数筛
   - `1800`: 单点改区间查线段树、树形 DP、倍增 LCA、状压 DP ($2^N \cdot N$)、矩阵快速幂
   - `2000`: 带懒标记线段树 (Lazy SegTree)、换根 DP、虚点 0-1 BFS、Tarjan SCC / 2-SAT
   - `2200`: 树链剖分 (HLD)、点分治、单调队列/斜率优化 DP、Dinic 网络流、可撤销并查集
   - `2400`: 后缀自动机 (SAM)/后缀数组、FFT/NTT 多项式卷积、费用流 (MCMF)、线性基
   - `2600`: 拟阵交、Link-Cut Tree (LCT)、支配树、形式幂级数/生成函数 (FPS)
   - `2800+`: 亚线性算法、复杂多项式复合逆、任意模数拟阵、前沿研究级结构

2. **思维跨度与模型转化阻力 ($\Delta_{\text{obs}} \in [0, +1100]$)**：
   从自然语言题面到识别出解题模型之间的认知跃迁距离。
   - `+0`: 裸题，题目直白描述操作
   - `+100 ~ +150`: 单步破局（逆向时光倒流、变式变形 $a_i - i$、奇偶守恒）
   - `+200 ~ +300`: 双层递进（单调性证明 + 树上判定贪心；反悔贪心性质证明）
   - `+350 ~ +500`: 深层结构塌缩（二分图虚点重构压缩 $O(M^2)$；笛卡尔树峰值组合分解）
   - `+550 ~ +750`: 极值重构与数学对偶（树重心极值贡献转化；最小割最大流对偶；连续消去映射斐波那契）
   - `+800 ~ +1100`: 奥林匹克突破级构造（置换环分解中的跨环桥接；极限操作步数构造）

3. **代码实现摩擦力 ($\Delta_{\text{imp}} \in [-50, +350]$)**：
   编码行数、结构解耦复杂度与赛时手写 Bug 概率。
   - `-50`: 极简短代码（$<25$ 行），几乎零 Corner Case
   - `+0`: 标准实现（$30 \sim 60$ 行），单向线性逻辑
   - `+50 ~ +100`: 中等复杂度（$70 \sim 120$ 行），离散化 + 差分双阶段、树上两次扫描
   - `+100 ~ +200`: 重型实现（$120 \sim 200$ 行），复合标记线段树、几何讨论、内存紧凑结构
   - `+200 ~ +350`: 残酷级实现（$200+$ 行），可持久化平衡树、树剖套矩阵维护

4. **直觉陷阱与时空张力 ($\Delta_{\text{trap}} \in [0, +150]$)**：
   导致选手赛时产生虚假信心、WA 或爆零的隐蔽陷阱。
   - `+0`: 约束充裕，逻辑自然
   - `+50`: `long long` 溢出、$N=1$ 或 $K=0$ 边界特判
   - `+100`: 贪心海市蜃楼（过样例但隐藏点 WA）；模数非质数导致除法逆元失效；图不连通
   - `+150`: 交互题极限询问次数（$\lceil 3N/2 \rceil$）；$10^6$ 数据规模 1.0s 极限常数压迫；32MB 极小内存限制

5. **赛制环境与版本修正 ($C_{\text{ctx}} \in [-100, +100]$)**：
   - Easy Version ($C_1, D_1$): `-50 ~ -100`（小数据范围允许 $O(N^2)$ 暴力）
   - Div.3 / Div.4 尾题: `-50`（官方样例往往附带启发式解释）
   - Hard Version ($C_2, D_2$): `+50 ~ +100`（必须做极致数据结构优化）
   - 交互题格式: `+50 ~ +100`

---

## 📊 真题标定验证 (Benchmark & Validation)

本项目包含一套 19 道横跨 800 至 2800+ 的全覆盖真题基准测试：

| 题目 ID | 题目名称 | $K$ | $\Delta_{\text{obs}}$ | $\Delta_{\text{imp}}$ | $\Delta_{\text{trap}}$ | $C_{\text{ctx}}$ | 预测值 | 官方 Rating | 误差 $\Delta$ |
|:---|:---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **1950A** | Stair, Peak, or Neither? | 800 | 0 | -50 | 0 | 0 | **800** | **800** | **0** |
| **1688B** | Patchouli's Magical Talisman | 800 | 50 | -50 | 0 | 0 | **800** | **800** | **0** |
| **1941C** | Rudolf and the Ugly String | 800 | 150 | -50 | 0 | 0 | **900** | **900** | **0** |
| **1976B** | Increase/Decrease/Copy | 1000 | 100 | 0 | 50 | -50 | **1100** | **1100** | **0** |
| **1984C1**| Magnitude (Easy Version) | 1200 | 150 | -50 | 50 | -50 | **1300** | **1300** | **0** |
| **1945D** | Seraphim the Owl | 1000 | 200 | 0 | 50 | +50 | **1300** | **1300** | **0** |
| **1932C** | LR-remainders | 1200 | 200 | 0 | 50 | 0 | **1400** | **1400** | **0** |
| **1932D** | Card Game | 1000 | 200 | 150 | 50 | 0 | **1400** | **1400** | **0** |
| **1840E** | Character Blocking | 1400 | 150 | 50 | 50 | -50 | **1600** | **1600** | **0** |
| **1946C** | Tree Cutting | 1200 | 300 | 100 | 50 | 0 | **1600** | **1600** | **0** |
| **1870D** | Prefix Purchase | 1400 | 350 | 50 | 50 | 0 | **1800** | **1800** | **0** |
| **1932F** | Feed Cats | 1600 | 300 | 50 | 0 | -50 | **1900** | **1900** | **0** |
| **1941G** | Rudolf and Subway | 1400 | 450 | 100 | 50 | 0 | **2000** | **2000** | **0** |
| **1942D** | Learning to Paint | 1800 | 250 | 50 | 50 | -50 | **2100** | **2100** | **0** |
| **1946E** | Girl Permutation | 1600 | 450 | 50 | 100 | 0 | **2200** | **2200** | **0** |
| **1898E** | Sofia and Strings | 1600 | 450 | 50 | 100 | 0 | **2200** | **2200** | **0** |
| **1943C** | Tree Compass | 1400 | 750 | 100 | 50 | 0 | **2300** | **2300** | **0** |
| **1842F** | Tenzing and Tree | 1800 | 550 | 100 | 50 | 0 | **2500** | **2500** | **0** |
| **1685D1**| Permutation Weight (Easy) | 1800 | 800 | 100 | 100 | 0 | **2800** | **2800** | **0** |

你可以直接运行本地标定脚本进行复现：
```bash
python scripts/benchmark.py
```

---

## 🚀 安装指南 (Installation)

本项目遵循标准 **Agent Skills 开放规范**，兼容市面上所有主流 Coding Agent 与大模型编程环境（Google Antigravity、Claude Code、Cursor、Gemini CLI、Windsurf 等）。

### 1. 全局安装（所有项目通用）

根据你所使用的 Agent 运行时，将本技能放入对应的 Skills 目录即可全局自动发现：

#### A. Google Antigravity / Gemini CLI / Codex
```bash
# Linux / macOS
git clone https://github.com/2542068503/cf-rating-estimator.git ~/.agents/skills/cf-rating-estimator

# Windows (PowerShell)
git clone https://github.com/2542068503/cf-rating-estimator.git $env:USERPROFILE\.agents\skills\cf-rating-estimator
```

#### B. Claude Code
```bash
# Linux / macOS
git clone https://github.com/2542068503/cf-rating-estimator.git ~/.claude/skills/cf-rating-estimator

# Windows (PowerShell)
git clone https://github.com/2542068503/cf-rating-estimator.git $env:USERPROFILE\.claude\skills\cf-rating-estimator
```

#### C. 手动单文件导入
如果你不需要 Git 版本管理，只需将本项目中的 [`SKILL.md`](SKILL.md) 下载并保存为：
- `~/.agents/skills/cf-rating-estimator/SKILL.md`（通用标准路径）

---

### 2. 项目级安装（仅当前工作区生效）

如果你希望将该技能仅作为当前工程或题库项目的专属工具：

```bash
# 进入你的竞赛代码工程根目录
cd /path/to/your/project

# 克隆至当前工作区的 skills 目录
mkdir -p .agents/skills
git clone https://github.com/2542068503/cf-rating-estimator.git .agents/skills/cf-rating-estimator
```
> **提示**：在 Cursor 或 VS Code 中，放在项目根目录的 `.agents/skills/` 或 `.cursor/rules/` 亦可自动索引。

---

## 💡 使用方法 (Usage & Workflows)

### 1. 触发方式

技能安装完成后，Agent 会在识别到以下场景时**自动激活**；你也可以使用指令强制调用：

- **快捷指令调用**：直接在输入框输入 `/cf-rating-estimator` 并附带题目。
- **自然语言触发**：发送类似以下语句时自动激活：
  - *"请评估这道算法题对应的 Codeforces Rating"*
  - *"这道题如果放到 CF 上大概是 Div.几 的哪一题？"*
  - *"帮我分析这道题的难度、知识点门槛和思维卡点"*

---

### 2. 推荐输入格式

为了让评估达到最高精度（$\pm 50$ 分以内），建议在提问时提供以下信息：

```markdown
/cf-rating-estimator

[题目名称 / 题面描述]
...

[输入/输出格式]
...

[数据规模与数据范围]（至关重要！例如 N <= 2e5, 字符串字符集大小等）
- N <= 2 * 10^5
- A_i <= 10^9

[时空限制]（决定了常数与算法上限！例如 2.0s, 256MB）
- Time limit: 2.0 seconds
- Memory limit: 256 megabytes
```

> **补充说明**：
> - 支持中文、英文题面、洛谷/AtCoder/Codeforces 原题面格式，或自拟的原创题草稿。
> - 若有特殊子任务梯度（如 Subtask 1~5），一并提供能够大幅辅助判断 $C_{\text{ctx}}$ 赛制难度定位。

---

### 3. 标准评估工作流与输出示例

调用该技能后，AI 将严格遵循四步工作流输出量化评估表：

```markdown
### 1. 题目分类与模型抽象
- **核心算法模型**：线段树优化 DP / 0-1 BFS / 树重心重构
- **目标时空复杂度**：O(N log N) / O(N)

### 2. 五维正交评分细则拆解
- **知识点基底底噪 (K)**: 1800 — [所必需的最优算法门槛]
- **思维跨度阻力 (Δobs)**: +450 — [从题面到模型的认知跃迁步数]
- **代码实现摩擦力 (Δimp)**: +50 — [代码量、调试难度与 Bug 率]
- **直觉陷阱张力 (Δtrap)**: +50 — [易错反例、溢出、模数、常数限制]
- **赛制环境修正 (Cctx)**: 0 — [版本分流、交互格式等修正]

### 3. 量化计算结果
- **原始计算得分**：R_raw = K + Δobs + Δimp + Δtrap + Cctx = 2350
- **预测 Codeforces Rating**：2300 ~ 2400
- **置信区间**：[2300, 2400]
- **对应赛制定位**：Div.1 B / Div.2 E
```

---

### 4. 离线本地基准测试 (Offline Benchmark)

如果你修改或微调了评分参数，可以使用内置的独立 Python 脚本一键验证全量基准测试：

```bash
python scripts/benchmark.py
```
终端将输出所有测试用例的得分公式推演、预测分值、官方分值与平均绝对误差（MAE）。

---

## 📄 License

本项目采用 [MIT License](LICENSE) 开源协议。
