# EvoTeam 审稿意见与补充实验清单

## 需要补充的四组实验

### 实验 1　补充 EvoFlow 和 MaAS 两个基线

分别与 EvoTeam 在可比的骨干模型、工具权限、任务划分和预算下对照；说明之前没纳入的原因。

对应问题　[C.Q1](#c-q1)

### 实验 2　两项消融：去掉技能、改用全局规则

① 关闭智能体层的技能和提示词补丁更新，保留团队与规则进化。② 保留多个团队，将团队专属规则改为全局共享，保持同等的规则适用性筛选、提示词和更新逻辑。

对应问题　[B.Q9](#b-q9)、[C.Q2](#c-q2)、[A.Q4](#a-q4)、[B.Q8](#b-q8)

### 实验 3　改变任务顺序

固定任务集合、系统起点与预算，比较原顺序、不同随机排列，或按类别分块／课程式排列。在同一留出集上比较各顺序对应的进化结果。

对应问题　[A.Q2](#a-q2)、[B.Q4](#b-q4)、[C.Q4](#c-q4)

### 实验 4　改变随机种子，独立重跑进化

固定任务顺序，从相同初始状态出发，以不同运行种子或独立采样重复完整进化流程。报告逐次结果、汇总值和置信区间，交代独立进化次数及测试 rollout 次数。

对应问题　[A.Q3](#a-q3)、[C.Q4](#c-q4)

四组实验以外，仍须单独回应等预算比较（C.Q3）、未见基准家族迁移（C.Q5）、检查点回测（A.Q5、C.Q6）和失败模式统计（C.Q8）。

实验5  同成本下混合训练和单独训练的差异对比

实验6 多次重复训练，重复测试的鲁棒性分析

## 审稿人及强相关问题

| 审稿人 | OpenReview 标识 | Overall Assessment | Confidence |
| --- | ------------- | ------------------ | ---------- |
| A   | stpV          | 3.0                | 2          |
| B   | Jrkf          | 3.5                | 4          |
| C   | xmYv          | 2.5                | 3          |

问题沿用 Word 中的 A.Q1、B.Q1、C.Q1 等编号，按原评审的 Weakness 顺序归拢。英文原文列在每条 Weakness 下，独立建议及勘误保留其来源栏目。

| 颜色标记  | 强相关问题                           | 共同问题             |
| ----- | ------------------------------- | ---------------- |
| 🟣 紫色 | [A.Q2](#a-q2) ↔ [B.Q4](#b-q4)   | 任务顺序敏感性          |
| 🟡 黄色 | [A.Q7](#a-q7) ↔ [B.Q6](#b-q6)   | 完整进化阶段的成本        |
| 🔵 蓝色 | [B.Q9](#b-q9) ↔ [C.Q2](#c-q2)   | 关闭智能体层技能和提示词更新   |
| 🟢 绿色 | [B.Q10](#b-q10) ↔ [C.Q7](#c-q7) | 单基准平均优势较小及部分任务落后 |

蓝色组对应 Word 中 B.Q9 的深蓝色和 C.Q2 的浅蓝色。红色单独强调保留在 [B.Q8](#b-q8)；[A.Q3](#a-q3)、[A.Q5](#a-q5) 保留红色“补充实验”备注。

## 审稿人 A

stpV · Overall Assessment 3.0 · Confidence 2。 [原评审](https://openreview.net/forum?id=Jw4GRwL1pz\&noteId=DycYUzXiZy)

### Weakness 1　A.Q1

**英文原文**

> The proposed framework appears to be an incremental extension of existing methods rather than a fundamentally new approach to multi-agent evolution. The paper would benefit from a clearer discussion of its technical novelty and a more explicit explanation of why team-scoped experience management provides advantages beyond existing experience retrieval and workflow evolution mechanisms.

<a id="a-q1"></a>

#### A.Q1　EvoTeam 的技术增量体现在哪里？

相较已有的经验检索和工作流进化，按团队管理协作经验具体改变了什么？为什么这种管理方式能解决已有机制难以处理的问题？所提出的框架是现有方法的渐进式扩展

Answer :感谢审稿人建议我们进一步明确技术贡献，

> 感谢您专业的审稿意见，以及对进一步明确技术贡献的建议。
>
> 多智能体系统在连续执行异构任务时，需要持续学习如何组织互补能力，并保留适用于不同协作需求的分工方式。例如，多跳问答需要协调证据检索、信息核验与答案整合；跨文件代码修改则需要协调依赖分析、代码实现与独立验证。同一个智能体可以参与这两类任务，但其协作对象、承担的职责和需要交付的结果可能不同。这就带来了一个组织层面的问题：当多种分工方式各有适用场景时，系统如何让它们长期共存，并从各自参与的任务中持续改进？
>
> （Meta-Team 已经利用集体执行经验改进智能体行为、协作方式和团队组织。）**EvoTeam 进一步将多种可复用的团队组织作为进化对象。** 每个团队记录自己能够支持的协作需求和候选成员。新任务到来时，系统根据所需分工选择合适的团队，并且不断在团队的范围下不断积累经验，包括团队成员的组成，以及与其分工相对应的交接和验证规则；而智能体自身学到的技能则作为自身的能力可用于不同团队的任务。围绕这一组织单元，EvoTeam 实现了以下三个方面的能力：
>
> 1. **根据协作需求选择、创建和保留团队。** 新任务到来时，系统根据所需的分工、互补角色和验证需求选择团队，并从候选成员中招募本次执行所需的智能体。在进化过程中，若现有团队无法满足需求，系统创建临时团队，再依据执行经验评估其组织方式是否具有复用价值，决定是否将其保留。
> 2. **共同改进团队组织及其协作规则。** 任务执行后的反思用于修订所参与团队的候选成员和协作需求描述，并更新与其职责分工相对应的信息交接、验证和协调规则。具有兼容协作需求的任务可以持续完善同一个团队，各团队的规则则在各自范围内更新，使经验积累与组织改进保持对应。
> 3. **支持个体能力跨团队复用。** 智能体从执行中获得的技能保留在个体层面，可用于其参与的不同团队。这样，不同团队能够形成各自适用的协作方式，同时共享智能体持续积累的能力。
>
> 上述设计在经验检索和工作流优化之外，进一步支持系统学习**哪些团队组织值得保留、何时应当复用或新建团队，以及一次执行经验应当用于改进哪个团队及其协作规则**。团队的复用依据是协作需求：来自不同领域、但需要相似分工的任务，可以共同完善同一个团队；需要不同分工的任务，则可以发展并持续改进各自适用的团队组织。

Weakness 2

**英文原文**

> One of the central claims of the paper is that maintaining multiple teams with independent interaction rules enables more effective collaboration across heterogeneous tasks. However, the current ablation studies only compare the full framework against variants without interaction rules and without both team organization and interaction rules. In particular, an important missing baseline is a framework that maintains multiple teams while sharing a global interaction-rule library with comparable rule applicability filtering.

<a id="a-q4"></a>

#### A.Q4　多个团队共享一个全局规则库时，效果如何？

保留多个团队，改用共享交互规则库，并给予全局规则与团队规则相当的适用性筛选。将其与完整 EvoTeam 比较，检验规则归属于特定团队的作用。这里需要补一个共享规则的实验。

answer:

感谢您专业的审稿意见，团队在多次执行中积累的经验，既包括哪些成员适合共同承担任务，也包括这些成员之间应当如何交接工作。例如，同样由分析者向下一位成员报告发现，在诊断任务中，这次交接可以触发最终答复；在实现任务中，它需要启动代码修改和后续验证。因此，当系统维护多个可复用团队时，一个自然的问题是：各团队能否共享全部交互规则，在执行时自行筛选，还是应当由每个团队持续积累与自身分工相适应的规则？您提出的全局规则库提供了一个直接对照，可以检验当智能体能够自行筛选适用规则时，将规则归属于特定团队还能带来多少收益。

为检验交互规则对团队协作的贡献，我们在原稿第 4.4 节保留了按任务选择团队的机制，并移除学习到的交互规则。图 4 显示，移除规则后，GAIA 的成功率从 **77.00%** 降至 **67.00%**，LOCA 从 **67.08%** 降至 **58.33%**。这说明，即使系统已经为任务选定了合适的团队，成员之间积累的交接与验证经验仍有助于完成任务。

根据您的建议，我们进一步补充了**多团队共享全局交互规则库**的对照实验。两个版本采用相同的组队机制、任务顺序、预算、规则更新方式和适用性筛选条件。在全局版本中，各团队从同一个规则库中筛选适用规则，并将任务后的修订写回该库。在完整 EvoTeam 中，成员从当前团队的规则库中筛选规则，任务后的修订也写回该团队。实验结果如下。

| 混合基准进化设置                      | GAIA  | LOCA  |
| ----------------------------- | ----- | ----- |
| 多团队，移除交互规则（原稿图 4）             | 67.00 | 58.33 |
| 多团队，全局共享规则库，保留适用性筛选（补充实验）     |       |       |
| 多团队，团队专属规则库（完整 EvoTeam，原稿图 4） | 77.00 | 67.08 |

Weakness 3　A.Q2、A.Q3

**英文原文**

> Although the paper averages three independent rollouts per test instance, the paper does not provide sufficient evidence regarding their statistical reliability. In particular, mixed-benchmark evolution uses a single fixed task ordering, even though the framework sequentially updates team structures, interaction rules, and agent skills based on previous executions. Different task orders may therefore lead to substantially different evolved systems. Moreover, some improvements over Meta-Team are relatively small. Additional experiments with multiple independent evolution runs, different task orderings, and confidence intervals would help establish whether the reported improvements are consistent and statistically meaningful.

<a id="a-q2"></a>

#### A.Q2　混合进化的结果对任务顺序有多敏感？　

Answer:   系统会依据前一题更新团队、规则和技能。相同的一批任务换一个出现顺序，最终形成的系统和留出集表现会有多大变化？补充随机种子实验。

**🟣 强相关问题**　[B.Q4](#b-q4)

<a id="a-q3"></a>

#### A.Q3　独立重跑整个进化过程后，提升是否稳定？

Answer:   现稿对每个测试实例取三次 rollout 的平均值。审稿人要求多次**独立进化**并报告置信区间，特别关注相对 Meta-Team 的小幅优势能否稳定复现。需要补实验

### Weakness 4　A.Q5

**英文原文**

> A major motivation of the paper is to improve experience reuse across heterogeneous tasks while preventing interference between incompatible collaboration patterns. However, although the paper provides case studies of cross-benchmark experience reuse, it does not systematically quantify how previously acquired capabilities are preserved as the system continues to evolve across different task categories.

<a id="a-q5"></a>

#### A.Q5　后续任务的进化会保留多少已有能力？

Answer: 现稿给出了跨基准复用的案例。审稿人希望量化系统继续学习其他类别任务后，在先前任务类别上的能力变化，从而判断是否发生干扰或遗忘。需要补充一个接连训练的实验

### Weakness 5　A.Q6、A.Q7

**英文原文**

> EvoTeam maintains a growing library of reusable teams, each associated with its own candidate agents and interaction rules. The paper provides limited analysis of how this library evolves as more heterogeneous tasks are encountered. Although the paper includes cost-performance comparisons, they primarily focus on the reported cost per task and do not provide a detailed breakdown of the total cost of evolution, including team selection, reflection, and knowledge updates.

<a id="a-q6"></a>

#### A.Q6　遇到更多异构任务后，团队库如何增长和复用？

Answer : 说明可复用团队库随任务流的变化，包括团队、候选成员和交互规则如何积累。团队增长、复用和重复建队的具体统计见 B 的相关问题。

<a id="a-q7"></a>

#### A.Q7　从选队到知识更新，整个进化阶段花费多少？　🟡 黄色

Answer : 现稿主要给出每题成本。区分进化开销与最终推理开销，并拆出团队选择、反思和知识更新等主要环节。需要补充数据。

感谢您专业的审稿意见。

**在本文中，团队是能够跨任务保留并持续演化的组织结构。** 当新任务与已有团队支持的分工相匹配时，EvoTeam 复用该团队，并从其候选成员中招募本次任务所需的智能体。当任务需要新的分工方式时，系统创建临时团队，并在执行结束后根据其组织方式是否值得复用，决定是否将其纳入团队库。随着任务推进，执行经验既可以促成新团队的形成，也可以用于调整已有团队的候选成员和交互规则。因此，评估这一过程需要同时考察团队库如何随任务增长、已有团队如何被复用，以及形成和更新这些团队所需的成本。

原稿附录 E.2 的图 17 展示了一个团队在 LoCoBench 和 BeyondSWE 四个任务中的演化过程。第一个任务形成了一个支持代码重构与兼容性修复的团队，后续三个任务复用了该团队。第三个任务完成后，系统将 integrator 加入候选名单，使候选成员由 3 名增至 4 名。第四个任务沿用更新后的团队，并根据当次分工招募其中 3 名成员。在成本方面，原稿第 4.5 节图 5 显示，在混合进化后的 GAIA 和 BeyondSWE DepMig 评测中，EvoTeam 的单题推理成本均低于 Meta-Team 和 ReCreate，同时取得了更高的任务得分。

**根据您的建议，我们进一步补充了团队库演化统计与完整进化成本分析。** 两项分析均覆盖 混合训练的全部 116 个进化任务。第一，我们按原始执行顺序将任务划分为四个连续区间，每段 29 题，分别统计新团队的提议数、实际入库数、已有团队的复用次数及各区间结束时的团队库规模。我们还根据团队 ID 和任务来源，统计有多少个团队实际服务过不同基准的任务。第二，我们按调用阶段汇总输入和输出 token、API 费用及其占比，并记录从首个任务开始到最后一次更新完成的总墙钟时间。

| 区间 | 任务序号   | 复用率     | 新团队入库数 | 段末团队数 | 跨基准团队数／团队总数 | 本段新增规则数 | 段末有效规则数 |
| -- | ------ | ------- | ------ | ----- | ----------- | ------- | ------- |
| 1  | 1–29   | 82.76%  | 4      | 4     | 2/4         | 80      | 80      |
| 2  | 30–58  | 100.00% | 0      | 4     | 2/4         | **89**  | 169     |
| 3  | 59–87  | 93.10%  | 2      | 6     | 2/6         | **43**  | 212     |
| 4  | 88–116 | 96.55%  | 1      | 7     | 2/7         | 25      | **237** |

| 阶段           | 输入 token        | 输出 token      | 费用占比        |
| ------------ | --------------- | ------------- | ----------- |
| 团队选择与按需创建    | 233,818         | 17,870        | 0.04%       |
| 成员招募、任务分配与执行 | 715,959,730     | 2,695,717     | 86.16%      |
| 团队反思与组织更新    | 32,810,955      | 115,433       | 3.94%       |
| 交互规则更新       | 11,975,394      | 427,912       | 1.71%       |
| 个体技能与提示词更新   | 68,002,592      | 204,991       | 8.15%       |
| **合计**       | **828,982,489** | **3,461,923** | **100.00%** |

从团队演化过程看，将 116 个任务按顺序等分为四个区间后，首个区间结束时，系统已保留 **4** 个团队，至第 116 个任务时达到 **7** 个。首段和末段已有团队的复用率分别为 **82.76%** 和 **96.55%**。四个区间的新团队入库数依次为 **4、0、2、1**，表明新团队的形成主要集中在早期；后续任务更多地复用已有团队，同时仍会按需补充新的团队结构。复用率并非逐段单调上升，但末段的 29 个任务中已有 **28 个**直接复用了已有团队。最终保留的 **7 个团队中，3个实际服务过至少两个不同基准的任务**

从成本构成看，完成整轮进化估计累计使用约 **8.32 亿 tokens**，总墙钟时间为 **22.41 小时**。团队选择与创建约占估算模型费用的 **0.04%**，成员招募与任务执行约占 **86.16%**，任务结束后的团队、规则和个体更新合计约占 **13.80%**。主要开销来自**成员招募、任务分配与执行阶段的模型调用**。结合原稿图 5，这些结果从两个层面刻画了系统成本：本轮统计衡量形成并持续更新团队库、完成 116 个进化任务的全流程总投入，图 5 则展示使用已进化团队处理新任务的单题成本。

**🟡 强相关问题**　[B.Q6](#b-q6)

### Comments Suggestions And Typos

> See Weaknesses

## 审稿人 B

Jrkf · Overall Assessment 3.5 · Confidence 4。 [原评审](https://openreview.net/forum?id=Jw4GRwL1pz\&noteId=IWWTKP3awh)


### Weakness 1　B.Q1、B.Q2、B.Q3、B.Q4

**英文原文**

> **Manuscript Omission of Global Agent Pool $\mathcal{A}$ Details, Missing Team Library Statistics, and Stream-Order Sensitivity**:
>
> - Prompt 2 (Figure 10, Core Rule 2) explicitly forbids inventing new agents outside the predefined public agent pool $\mathcal{A}$ ("Select members only from the supplied agent pool... Do not invent agents"), so $\mathcal{A}$ upper-bounds organizational expressiveness. The earlier repository inspection recorded a seven-agent pool under `agents/pool_EvoTeam/` (planner, researcher, context analyst, implementer, integrator, verifier, answer agent). However, neither Section 3 nor Appendix B documents $|\mathcal{A}|$, the roles, or their benchmark-specific tool permissions. These details should appear in the manuscript.
> - Similarly, the paper reports no quantitative statistics on the evolved library $\mathcal{T}$: How many teams $M=|\mathcal{T}|$ are proposed vs. admitted during individual and mixed evolution? What is the cold-start vs. reuse rate on evolution and holdout splits? Notice that Algorithm 1 includes team creation and revision, but *no team merging or pruning mechanism*—if early tasks trigger overlapping cold-start teams, rule accumulation could fragment across near-duplicate teams. Finally, mixed-benchmark evolution uses a single fixed shuffle seed (`20260828`, line 1065); sensitivity to stream ordering (e.g., blocked/curriculum order vs. multiple random shuffles) remains untested.

<a id="b-q1"></a>

#### B.Q1　预定义的全局智能体池有哪些成员和权限？

Answer :图 10 的 Prompt 2 禁止创建池外智能体。审稿人据代码记录了 7 类角色：规划、研究、上下文分析、实现、集成、验证和答案生成。请核对，并在第 3 节或附录 B 交代池大小、角色及各基准的工具权限。需要做一个权限表格，或者交代这个权限都放在了代码仓库中。

<a id="b-q2"></a>

#### B.Q2　团队提议、入库、冷启动和复用各占多少？

Answer : 分别报告 Individual 和 Mixed 进化中提出的团队数、获准入库数和最终库规模；在进化集与留出集分别给出冷启动率和已有团队复用率。可以做表格放复用率

<a id="b-q3"></a>

#### B.Q3　如何处理相似团队重复建队及长期累积？

Answer : 算法 1 有创建和修订，未描述合并或剪枝。说明早期冷启动是否会让相似团队分散积累规则，并在局限性中讨论长任务流下团队库和规则库可能持续增长的问题。需要找提示词核对如何解决这些问题的。

<a id="b-q4"></a>

#### B.Q4　换用分块、课程式或不同随机顺序会怎样？　🟣 紫色

Answer: 混合进化仅使用打乱种子 20260828（原稿第 1065 行）。审稿人举例希望比较按类别分块、课程式顺序或多次随机打乱，检验任务到达顺序对结果的影响。需要补充实验3

**🟣 强相关问题**　[A.Q2](#a-q2)

answer ：感谢您专业的审稿意见。

在 EvoTeam 中，全局智能体池定义了团队构建时可使用的成员及其初始能力范围。团队进化在这一范围内调整候选成员组成，并持续更新协作规则和个体技能。固定的初始成员集合使我们能够考察：给定相同的可用能力，系统如何通过组织和协作方式的进化，更好地处理不同任务。

在混合基准设置中，系统包含七个智能体配置：Planner、Researcher、Context Analyst、Implementer、Integrator、Verifier 和 Answer Agent。其中，Planner 担任固定 Chairman，Answer Agent 提供最终输出服务，其余五个角色构成可被团队选择和招募的候选成员。

智能体的工具访问范围由其角色配置与当前基准允许的工具共同确定。在混合基准运行时，代码对两者取交集，因此团队选择不会额外扩大工具访问范围。例如，GAIA 提供文件处理、命令执行和网页检索工具，LoCoBench 提供文件与命令执行工具，LOCA-Bench 进一步提供原生 MCP 服务，BeyondSWE 使用隔离容器中的仓库执行和编辑工具。

根据您的建议，我们将在 Section 3.1 和 Appendix B 中补充初始智能体池的规模、角色职责、各角色工具配置及基准级工具约束，并给出对应代码位置。单基准实验使用的初始池也将分别列出，使这些设置可以直接从论文中查阅。

| 配置              | 职责                | 团队构建中的位置    |
| --------------- | ----------------- | ----------- |
| Planner         | 招募成员、分配任务与协调执行    | 固定 Chairman |
| Researcher      | 检索外部证据、提供来源与不确定性  | 可招募成员       |
| Context Analyst | 分析附件、仓库结构和依赖关系    | 可招募成员       |
| Implementer     | 创建或修改任务产物         | 可招募成员       |
| Integrator      | 整合分散证据、检查跨产物一致性   | 可招募成员       |
| Verifier        | 独立验证计算、来源、测试及环境结果 | 可招募成员       |
| Answer Agent    | 按基准输出要求生成最终结果     | 全局输出服务      |

关于提到的第二个问题，EvoTeam 的团队库随执行经验逐步形成，其演化过程包括临时团队的提出、执行后的入库判断，以及已有团队的复用和修订。因此，团队库的统计需要同时反映最终规模和形成过程。

我们补充了各单基准设置及混合基准设置下的团队库统计，包括临时团队提出数量、正式入库数量、最终团队数量，以及进化阶段和测试阶段的团队复用率与冷启动率。

| 测试基准 / 任务类别                        | 单基准进化后的测试复用率 | 混合基准进化后的测试复用率 |
| ---------------------------------- | ------------ | ------------- |
| GAIA                               | 97%          | 95%           |
| LoCoBench — Feature Implementation | 100%         | 98%           |
| LoCoBench — Cross-File Refactoring | 100%         | 100%          |
| LOCA-Bench                         | 100%         | 100%          |
| BeyondSWE — CrossRepo              | 100%         | 100%          |
| BeyondSWE — DepMigrate             | 100%         | 100%          |

关于提到的第三个问题，在 EvoTeam 中，团队库通过协作需求匹配和执行后的入库判断控制新增团队，并在已有团队内部持续积累经验。团队身份依据其支持的协作模式确定，包括工作要求、互补角色和验证需求。来自不同领域、但具有兼容协作需求的任务，可以复用并共同完善同一个团队。具体设计如下

**1. 根据协作需求判断是否确实需要新团队。**

系统根据任务需要的分工、互补角色和验证方式匹配已有团队。任务主题或具体对象发生变化，并不直接构成新建团队的依据。例如，两个代码任务虽然修改不同的功能，但都需要依赖分析、代码实现和独立验证，就可以复用同一个团队。

团队选择提示词明确要求：

> “Choose cold_start when no team is suitable.”

团队创建策略进一步规定：

> “Create a team template only when the task requires a materially different collaboration pattern.”

这使新增团队的判断依据落在**现有团队是否缺少所需的协作模式**上，减少因任务表面差异而分别建队的情况。

**2. 冷启动先用于当前任务，经过执行验证后再决定是否入库。**

当已有团队无法满足需求时，系统从智能体池中构建临时团队。此时团队库数量尚未增加，提示词明确说明：

> “This does not admit the team to the persistent library.”

任务结束后，团队反思模块根据实际分工、执行轨迹和结果反馈，判断该组织方式是否具有后续复用价值。其提示词要求：

> “For a cold start, create a v1 template only when the completed run provides reusable evidence; otherwise use no_update.”

因此，**提出一个临时团队与正式增加一个可复用团队是两个独立决策**。后者需要执行证据支持，避免每次冷启动都直接留下一个持久化团队。

**3. 已有团队需要调整时，在原团队内修订，继续积累经验。**

对于被复用的团队，成员调整和描述修订沿用原有团队身份。团队反思提示词明确要求：

> “Preserve the supplied family_id.”

这一要求还受到代码校验约束：已有团队只允许修订、保留或不更新，不能通过该次反思创建新的团队分支。因此，例如执行后发现需要增加一个验证成员，系统可以更新原团队的候选成员，而无需将其另存为一个新团队。后续兼容任务仍可复用该团队，并在对应规则库中继续积累经验。

这些设计分别控制**新建是否必要、临时组织是否值得保留，以及已有团队的调整是否产生额外分支**。我们将在修订稿中明确上述过程，并补充临时团队提出数、正式入库数、团队复用率及团队数量随进化过程的变化，以展示这些控制在实际运行中的效果。当前实现未包含跨团队的显式合并或剪枝，因此我们将其表述为对团队新增和更新的约束，不将其作为完全消除团队重叠的保证。

Weakness 2　B.Q5、B.Q6、B.Q7

**英文原文**

> **Unreported Evolution-Phase Cost, Context Scaling of Rule Sets, and Evaluator-Feedback Assumptions**:
>
> - In Section 3.3 (Equations 5–7), the reflectors $\Omega_T,\Omega_R,\Omega_A$ are written solely as functions of $(T_q,\tau_c,q)$, omitting evaluator feedback. Appendix A (line 949) and Prompts 5–7 disclose benchmark feedback, including pass/fail scores and diagnostic strings such as “Email verification failed,” during evolution. The main-text equations should include this input, explain its availability requirements, and confirm whether all evolutionary baselines receive equivalent feedback. This is a specification and deployment-assumption issue, not evidence of concealed supervision or test leakage.
> - Section 4.5 (Figure 5) and Appendix C.2 (Figure 7) only report *holdout inference cost per task*, omitting the *evolution cost* (total tokens, API dollars, and wall-clock time for the 116-task evolution phase, which invokes $\Phi_S$, optional $\Phi_C$, $\Phi_{\mathrm{Chair}}$, task execution, plus $\Omega_T$, $\Omega_R$, and $|E_q|$ calls to $\Omega_A$).
> - Figure 16 (page 22) indicates that a single team accumulated **224 rules** (`Local update: 223 -> 224 rules`), and Algorithm 1 line 9 states *"Inject $R_{T_q}$ into agents' contexts as soft guidance."* How are $>200$ structured rules injected or pre-filtered per agent call without causing context bloat or instruction-following degradation as evolution scales beyond 116 tasks?

<a id="b-q5"></a>

#### B.Q5　反思器使用哪些评测反馈，各进化基线是否同等可用？

第 3.3 节公式 5–7 未列明反馈输入；附录 A 和 Prompt 5–7 使用了通过与否及诊断文本。请对齐公式与实现、交代各基线可见的反馈，并在局限性中说明开放场景可能需要人工或学习式验证器。

Answer ：

<a id="b-q6"></a>

#### B.Q6　116 个混合任务的完整进化成本是多少？　🟡 黄色

图 5、图 7 报告的是留出集单题推理成本。请列出进化的总 token、API 费用和墙钟时间，覆盖选队、按需组队、主席调度、任务执行，以及团队、规则和参与智能体的反思调用。

**🟡 强相关问题**　[A.Q7](#a-q7)

<a id="b-q7"></a>

#### B.Q7　单个团队积累 224 条规则后，每次调用实际读入多少？

图 16 显示某团队从 223 条增至 224 条；算法 1 第 9 行描述了向上下文注入规则。请说明每次调用注入哪些规则、是否预筛选，以及超过 116 个任务后上下文占用和指令遵循可能怎样变化。

answer :answer :感谢您专业的审稿意见。

在 EvoTeam 中，进化过程以任务执行轨迹和任务结果反馈共同作为依据：执行轨迹记录智能体的具体行为及协作过程，评估反馈提供这些行为是否达成任务目标的结果信号。例如，在我们的 GAIA 实验设置中，任务完成后由该基准对应的评估器判断答案是否正确，并将成功或失败的结果提供给反思模块。其他基准则提供其原生评估器支持的任务得分、测试结果或诊断信息。

这些反馈来自实验使用的基准评估流程，所有基线均可获得对应任务的相同反馈，各方法按照自身设计使用这些信息。EvoTeam 将反馈与执行证据结合，用于评估团队组成是否满足任务需求、交互规则是否支持有效协作，以及个体技能是否需要改进。

原文 Appendix A 已说明执行过程包含原生任务评估，Appendix D 的 Prompts 5–7 也将 evaluation feedback 列为三个反思模块的输入。根据您的建议，我们将在 Section 3.3 的 Equations (5)–(7) 中显式加入反馈变量 \\(f_q\\)，并同步更新 Algorithm 1：

同时，我们将在实验设置中明确各基准提供的反馈类型及其对所有比较方法的可用性。这些持久化更新发生在进化划分上；在独立的测试划分上，团队库、交互规则和个体技能均保持冻结。

在实际部署中，这一进化过程需要可获得的任务结果信号。对于具有自动测试或程序化检查的任务，可利用相应验证结果；对于缺少此类评估器的开放式任务，则需要人工或模型评估信号，其可靠性及对进化效果的影响需要进一步验证。我们将在 Limitations 中明确这一适用条件。

EvoTeam 的完整进化成本涵盖任务执行及执行后的经验更新，包括团队选择、按需创建团队、成员招募与协调、智能体执行，以及团队、交互规则和个体技能三个层面的反思。完整的成本报告需要同时呈现这一阶段的累计投入和进化结束后的任务执行成本。

原文 Section 4.5 的 Figure 5 和 Appendix C.2 的 Figure 7 报告了冻结系统在测试任务上的性能与平均执行成本，展示了所评估设置下的性能—成本关系。根据您的建议，我们将在修订稿中进一步补充 **116 个混合进化任务的完整成本及模块分解**。

在 EvoTeam 中，交互规则按照**所属团队和有向的发送方—接收方关系**组织，执行时依据当前协作需求提供相应的候选规则。Figure 16 中的 224 条规则表示该团队累积维护的规则总量；单次交接所使用的候选集合由当前参与协作的双方确定。

具体过程包括以下三个步骤：

1. **确定团队范围。** 当前任务选择的团队决定可使用的规则库 \\(R\_{T_q}\\)。
2. **筛选协作双方对应的规则。** 当智能体 \\(a\\) 需要向智能体 \\(b\\) 交接信息时，仅向 \\(a\\) 提供该团队中以 \\(a\\) 为发送方、以 \\(b\\) 为接收方的有效规则：\\[ R\_{T_q}^{a\rightarrow b} = \\{r\in R\_{T_q}\mid \operatorname{sender}(r)=a,\\; \operatorname{recipient}(r)=b,\\; r\text{ is active}\\}. \\]
3. **根据当前情境选择适用规则。** 智能体结合规则的触发条件、交付内容和证据要求进行选择。原文 Appendix D.4 的 Prompt 4 指定：多个规则适用时选择最具体的一条；没有适用规则时返回 `NONE`，直接完成协作。

因此，单次规则选择所需处理的上下文取决于**当前团队内、当前发送方—接收方对应的候选规则**。与其他协作关系相关的规则不会进入此次规则选择的上下文。个体 skill 则用于完成智能体自身的子任务，执行时根据当前需求选用相关 skill；它与用于智能体间信息交接的规则分别管理。

原文 Appendix D.4 已描述规则的发送方—接收方匹配、触发条件判断和单次交接选择机制。根据您的建议，我们将进一步修改 Section 3.2、Algorithm 1 的第 9 行及 Prompt 4 的输入说明，明确**候选规则的筛选发生在上下文注入之前**，并将实际注入集合表示为上述 \\(R\_{T_q}^{a\rightarrow b}\\),对于持续增长的规则库，原文 Appendix D.6 的反思机制支持修订已有规则，并在历史证据表明规则持续不适用、冗余或被更可靠规则替代时将其停用。

### Weakness 3　B.Q8、B.Q9、B.Q10

**英文原文**

> **Incomplete Ablation Design to Isolate Scoping vs. Global Sharing and Agent-Level Evolution**:
>
> - The central thesis of EvoTeam is that *scoping* interaction rules to compatible teams prevents cross-task interference while *sharing* individual skills helps. However, the ablation study (Section 4.4, Figure 4; Appendix C.1, Figure 6) only tests `Ours`, `w/o team-specific rules`, and `w/o team & team-specific rules`. Two key controls are missing: (a) **EvoTeam with global (unscoped) interaction rules** (keeping EvoTeam's exact team library, prompts, and non-destructive rule update logic $\Omega_R$, but storing rules in a single shared pool $R$ rather than per-team $R_T$), which is needed to verify that gains come from *team scoping* rather than stronger reflection prompts relative to Meta-Team; (b) **`w/o agent-level evolution` ($\Omega_A$ disabled)**, to quantify how much of the gain comes from individual skill/patch accumulation vs. collaboration evolution.
> - Note also that under Individual-Benchmark Evolution (Table 1), Meta-Team outperforms EvoTeam by 5.83 points on LOCA (69.58 vs. 63.75) and by 0.56 on BeyondSWE CrossR (41.67 vs. 41.11), making the overall margin slim (+0.55 average, without evolution-seed variance). Clarifying why EvoTeam trails Meta-Team by nearly 6 points on single-benchmark LOCA would be helpful.

<a id="b-q8"></a>

#### B.Q8　如何把团队专属规则的收益与反思提示词的收益区分开？　🔴 红色

比较全局与团队规则时，保持相同的团队库设置、提示词和非破坏性规则更新逻辑，只改规则的存储与共享范围。附录 E.1 的单条轨迹不足以单独判断因果贡献。与前面问题一致，需要补消融实验

**🔴 单独强调**

<a id="b-q9"></a>

#### B.Q9　关闭智能体层进化后，技能和提示词补丁贡献多少？　🔵 蓝色

禁用智能体反思器 Ω_A，停止累积技能和 prompt patch，同时维持团队与规则进化。将此版本与完整系统比较，分离个体层和协作层的贡献。与前面问题一致，需要补消融实验

**🔵 强相关问题**　[C.Q2](#c-q2)

<a id="b-q10"></a>

#### B.Q10　为什么单基准进化在 LOCA 上落后近 6 分？　🟢 绿色

表 1 中 EvoTeam／Meta-Team 在 LOCA 为 63.75／69.58，在 CrossRepo 为 41.11／41.67，平均仅领先 0.55 分。解释 LOCA 差距，并交代独立进化的波动；838 个留出实例、任务类型和独立进化次数需分清。

**🟢 强相关问题**　[C.Q7](#c-q7)

### Weakness 4　B.Q11、B.Q12

**英文原文**

> **Under-Specified Preliminary Study and Abbreviated Appendix Prompts**:
>
> - Section 2.1 and Figure 2a never state in the text or caption which target benchmark/subset is plotted in Figure 2a (the 64.82 score matches LoCoBench Feat, yet one bar is labeled *"Semantic Similarity"*, which is not among the four official LCBS dimensions in Equation 8), nor which source benchmark (`Inter-CT`) and intra-benchmark category (`Intra-CT`) were used.
> - Prompts 1–7 in Appendix D (Figures 9–15) are abbreviated mid-list (`5. . . .`, `7. . . .`, `8. . . .`); including the full rules in Appendix D (or explicitly cross-referencing the prompt files in the repository) would improve clarity.

<a id="b-q11"></a>

#### B.Q11　图 2a 的目标子集、迁移来源和指标是什么？

标明目标基准及子集、Inter-CT 的来源基准，以及 Intra-CT 的来源类别。64.82 与 LoCoBench Feat. 相符是审稿人的推测；请核实，并解释图中 Semantic Similarity 与公式 8 的四个 LCBS 维度如何对应。

answer:

<a id="b-q12"></a>

#### B.Q12　附录 D 的 Prompt 1–7 能否完整复现？

图 9–15 的多处列表以省略号截断。补全规则，或在附录中明确指向仓库内对应的完整 prompt 文件。

answer：感谢您专业的审稿意见。在本文中，预实验旨在考察已有协作经验迁移到新任务后，能否支持多智能体系统继续有效进化。准确理解和复现这一过程，需要明确经验的迁移来源、目标任务的评估指标，以及控制系统行为的完整提示词。

为考察经验迁移的影响，我们在原文 Section 2.1 和 Figure 2a 中比较了跨基准迁移（Inter-CT）和基准内跨类别迁移（Intra-CT）两种设置。结果显示，两种迁移设置在图中报告的三个指标上均低于仅在目标任务上进化的 Meta-Team，表明已有经验能否带来收益，与其是否适合目标任务的协作需求有关。

关于图中的指标，LoCoBench 综合得分由 Software Engineering Excellence、Functional Correctness、Code Quality 和 Long-Context Utilization 四个一级维度加权得到，每个一级维度又包含若干子维度。**Figure 2a 中的 Semantic Similarity 属于【所属一级维度】下的子维度。** 原文公式 8 给出了四个一级维度的聚合方式，但未完整列出其下属子维度，导致图中指标与公式之间的对应关系不够清晰。

根据您的建议，我们将在修订稿中补充以下内容：

1. **补全预实验设置。** 在 Section 2.1 和 Figure 2a 图注中明确，目标任务为【目标基准及子集】；Inter-CT 从【来源基准】上进化得到的系统出发，继续在目标任务的训练集上进化；Intra-CT 从同一基准的【来源任务类别】上进化得到的系统出发，继续在上述目标任务的训练集上进化。
2. **补全指标层级与定义。** 在 Appendix B.2 中完整列出四个一级维度及其下属子维度，补充 Semantic Similarity 的归属、定义和计算方式，并在 Figure 2a 图注中明确综合得分、一级维度与子维度之间的关系。
3. **提供完整提示词。** 在 Appendix D 中展开 Prompts 1–7（Figures 9–15）中所有被省略的条目，完整呈现输入说明、决策约束和输出格式，并提供代码仓库中的对应文件链接【补充具体链接】，便于读者核对并复现团队构建、协作执行与经验更新过程。

下面问题都是语法问题：

### Comments Suggestions And Typos　B.Q13 至 B.Q19

**英文原文**

> Appendix G (8 pages of self-written FAQ) largely repeats Section 3 and Appendix E; trimming Appendix G would free ample space to include the full untruncated prompts, the specification of agent pool $\mathcal{A}$, and team-library statistics directly in the paper.

<a id="b-q13"></a>

#### B.Q13　能否压缩附录 G，为复现信息留出篇幅？

审稿人认为 8 页 FAQ 与第 3 节及附录 E 重复，建议精简，腾出篇幅放入完整提示词、智能体池规格和团队库统计。

**Typos & Minor Presentation Issues**

<a id="b-q14"></a>

#### B.Q14　第 2 页，第 087 行

**英文原文**

> Page 2, line 087: "interaction rule" → "interaction rules".

修改　interaction rule  →  interaction rules

<a id="b-q15"></a>

#### B.Q15　第 2 页，第 146 行

**英文原文**

> Page 2, line 146: "EVOTEAM consists three key components that enables" → "consists of three key components that enable".

修改　consists three key components that enables  →  consists of three key components that enable

<a id="b-q16"></a>

#### B.Q16　第 3 页，图 2b

**英文原文**

> Page 3, Figure 2b: "Diagnose a configuration issues." → "Diagnose a configuration issue."

修改　Diagnose a configuration issues.  →  Diagnose a configuration issue.

<a id="b-q17"></a>

#### B.Q17　第 4 页，图 3c 右下框

**英文原文**

> Page 4, Figure 3c: Bottom-right box reads "+ user fool" (presumably a typo for "+ use tool").

修改　+ user fool  →  + use tool（审稿人推测，需核对原意）

<a id="b-q18"></a>

#### B.Q18　第 18 页，第 1349 行

**英文原文**

> Page 18, line 1349: Missing period after "assigns complementary responsibilities".

修改　assigns complementary responsibilities  →  assigns complementary responsibilities.（补句号）

<a id="b-q19"></a>

#### B.Q19　第 19 页，图 12，Prompt 4

**英文原文**

> Page 19, Figure 12 (Prompt 4): "Given a interaction need" → "Given an interaction need".

修改　Given a interaction need  →  Given an interaction need

### Limitations And Societal Impact

**英文原文**

> The authors include dedicated Limitations and Ethical Considerations sections on page 9. Reproducibility ratings here estimate documentation sufficiency and do not report an independent reproduction. They appropriately note the lack of evaluation in multimodal interactive environments, reliance on fixed-parameter LLMs interpreting in-context guidance, potential inheritance of base-model biases, unintended tool side effects, and the privacy risk of retaining sensitive data inside persistent execution traces and reusable rules/skills. One constructive addition to Limitations would be acknowledging the reliance on ground-truth/programmatic evaluation feedback during the evolution phase (which may not be available in open-ended user deployments without human or learned verifiers) and potential unbounded growth of team/rule libraries over long lifelong streams.

对应问题　[B.Q3](#b-q3)（团队与规则库长期增长）、[B.Q5](#b-q5)（进化阶段对评测反馈的依赖）。

## 审稿人 C

xmYv · Overall Assessment 2.5 · Confidence 3。 [原评审](https://openreview.net/forum?id=Jw4GRwL1pz\&noteId=z8ppx6h8BD)

### Weakness 1　C.Q1

**英文原文**

> EvoFlow and MaAS are discussed but not evaluated, leaving EvoTeam’s comparative advantage over relevant population-based and query-dependent designs unresolved.

**Comments Suggestions And Typos 中的对应建议 1**

> Why were EvoFlow and MaAS excluded from the experiments, and could a matched comparison clarify EvoTeam’s additional benefit?

<a id="c-q1"></a>

#### C.Q1　为什么未评测 EvoFlow 和 MaAS？同等设置下结果如何？

说明这两个在相关工作中已讨论的方法为何未进入原实验，并在可比设置下与 EvoTeam 对照，交代相对种群式设计和按查询组织系统的额外收益。需要补baseline

### Weakness 2　C.Q2

**英文原文**

> Agent-level prompt and skill evolution is not separately ablated, leaving its incremental contribution to the complete framework unclear.

**Comments Suggestions And Typos 中的对应建议 2**

> What changes when agent-level prompt and skill updates are disabled while the team- and rule-evolution mechanisms remain enabled?

<a id="c-q2"></a>

#### C.Q2　保留团队和规则进化、停用个体提示词与技能更新，结果如何？　🔵 蓝色

单独消融 agent 层的 prompt 和 skill 更新，量化个体进化对完整框架的增量贡献。回复时应说明冻结的是哪些初始能力，以及哪些更新仍启用。与前面问题一致，需要补baseline

**🔵 强相关问题**　[B.Q9](#b-q9)

### Weakness 3　C.Q7

**英文原文**

> Under individual-benchmark evolution, the reported average lead over Meta-Team is 0.55 points, with lower scores on LOCA and BeyondSWE CrossRepo (Table 1).

<a id="c-q7"></a>

#### C.Q7　如何解释单基准平均仅领先 0.55 分以及两项落后？　🟢 绿色

针对 Individual-Benchmark Evolution，解释与 Meta-Team 的平均差距，以及 LOCA、BeyondSWE CrossRepo 上落后的情况。该条与混合设置下的领先结论应分别作答。

**🟢 强相关问题**　[B.Q10](#b-q10)

### Weakness 4　C.Q8

**英文原文**

> The cases illustrate useful mechanisms and corrected intermediate errors, but do not quantify the main failure modes in unsuccessful EvoTeam runs.

<a id="c-q8"></a>

#### C.Q8　最终失败的 EvoTeam 运行主要有哪些失败模式？

现有案例展示了有效机制和被纠正的中间错误。审稿人希望针对最终未成功的运行，归纳主要失败原因并量化各类频次，说明统计样本及分母。

### Comments Suggestions And Typos　其余建议

**建议 3 英文原文**

> Does heterogeneous evolution outperform target-only evolution when the total number of evolution steps or the total cost is matched?

<a id="c-q3"></a>

#### C.Q3　进化步数或成本匹配后，异构进化仍优于仅在目标基准进化吗？

在总进化步数相同或进化总成本相同的条件下，比较 heterogeneous evolution 和 target-only evolution 在同一目标基准上的表现，并说明采用哪一种预算口径

answer :感谢您专业的审稿意见。

在 EvoTeam 中，每完成一个进化任务，系统就有一次根据执行经验调整团队组织、交互规则和个体技能的机会。单目标进化将这些机会全部用于同一类任务，使团队持续适应该类任务的分工与验证要求。异构进化则允许不同任务在协作需求相近时复用同一个团队，但在总步数固定时，系统直接接触目标任务的次数也会减少。因此，等步数比较可以检验，来自其他任务的协作经验能否在有限的进化预算下改善目标任务表现。

原稿表 1 报告了两种设置的最终结果。混合进化的六项平均分为 **59.58**，单目标进化为 **58.85**，具体表现因任务而异。例如，混合进化在 LOCA 上得到 **67.08**，高于单目标进化的 **63.75**；在 GAIA 上，两者分别为 **77.00** 和 **78.00**。根据表 4，混合系统经历了完整的 **116 步**进化，而每个单目标系统经历了 **16 或 20 步**。因此，表 1 回答的是两种完整训练设置下的表现，等步数条件下的差异需要单独比较。

根据您的建议，我们进一步设计了**等进化步数对照**。我们将完成一个进化任务及其任务后更新计为一步。对每个目标评测方向，两个系统均从相同初始状态出发，采用相同的骨干模型和评测协议。单目标系统仅使用该方向的进化任务，混合系统按原始顺序使用异构任务流。达到匹配步数后冻结两个系统，在**同一目标留出集**上按原稿的 avg@3 协议评测。表中同时列出混合序列中来自目标方向的任务数，以说明相同步数下两种系统分别获得了多少直接的目标任务经验。

**建议 4 英文原文**

> How sensitive are the results to task order and independent evolution runs?

<a id="c-q4"></a>

#### C.Q4　改变任务顺序或独立重跑进化，结果有多敏感？

分别考察任务顺序变化和多次独立进化对结果的影响，并报告性能波动。与前面问题重叠，需要补实验

**建议 5 英文原文**

> As a further generalization test, how well does the system transfer to a benchmark family excluded entirely from evolution?

<a id="c-q5"></a>

#### C.Q5　能否迁移到整个进化过程中都未出现的基准家族？

将一个 benchmark family 整体排除在进化数据之外，再在该家族上评测，以检验跨家族迁移。该设置与同一基准内的留出实例、已有跨骨干迁移实验分别回答不同问题。

在gaia上训练，在locobench上测试，需要补实验

**建议 6 英文原文**

> Are checkpoint-based retention results available to show how earlier-task performance changes during later evolution, beyond the reported final held-out scores?

<a id="c-q6"></a>

#### C.Q6　能否用中间检查点追踪先前任务的能力保留？

在后续进化的多个检查点回测相同的早期任务，给出分数变化。审稿人希望看到随进化推进的轨迹，单个最终留出集分数无法呈现这一过程。与前面一致,第一个审稿人的q4
