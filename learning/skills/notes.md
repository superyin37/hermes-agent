# Hermes Skills 学习记录

返回[学习总览](../README.md)。

学习路线：[plan.md](plan.md)。

创建日期：2026-09-11；计划最初核对的源码基线：`26350357d7`。实际进度和当前实验路径以本文件“当前进度”与“环境与复现信息”为准；计划中的旧行号需要对照当前生产代码。

## 如何使用

- 每次学习后写几句话即可，不必一次填完全部表格。
- 保留“原先怎么想”，用新段落补充修正，避免只留下整理后的正确答案。
- 证据分为：**代码阅读、直接函数运行、真实模型对话、已有测试**。不同证据不能相互冒充。
- 不清楚的内容写“待验证”；复制计划中的预期时注明“预期”，不要写成已观察结果。
- 可以自己填写，也可以让助手根据对话更新。助手不应代替我认定“我已理解”，或把没有执行的实验勾为完成。
- 每次学习问答结束、继续下一步之前，助手应判断本轮整体内容是否具有长期学习价值；有价值时立即记录到本文，并区分事实、运行证据和待验证推测，无需等待再次提醒。
- 学习重点是 Skills/Agent 的机制与数据流。普通 Python 语法和逐行实现细节只在确实暴露理解问题、影响 Agent 机制判断或具有长期排障价值时记录；当场即可解决的基础细节不逐问归档。
- 助手要求阅读某个函数时，应附上可点击的源码路径和行号，并明确它位于当前文件还是需要切换到另一个文件，避免只给函数名让用户自行搜索。
- 记录目录、模型名称和错误信息即可，不保存密钥、令牌或完整敏感配置。

## 当前进度

| 阶段 | 状态 | 日期 | 证据位置 | 下一步 |
| --- | --- | --- | --- | --- |
| 0. 环境准备 | 已完成（0.1～0.3 与自检；后续多次重建临时实验目录） | 2026-09-15～2026-09-25 | 本文“环境与复现信息”及“阶段 0 自检” | 已进入阶段 3 |
| 1. 最小技能 | 已完成（1.1～1.3 与自检） | 2026-09-16 | 实验 `SKILL.md`；会话 `20260916_172929_a2cd43`、`20260916_173136_64b59c` 与 `20260916_175614_8a8d7d`；本文 1.2～1.3 对照结果 | 进入阶段 2，追踪文件如何变成命令条目 |
| 2. 发现与解析 | 已完成（2.1～2.3、受控失败与最终自检） | 2026-09-18～2026-09-24 | 本文“阶段 2”的代码阅读、直接函数运行和最终自检 | 进入阶段 3，观察显式命令真正生成的消息 |
| 3. 消息构造 | 进行中（3.1～3.2 已完成） | 2026-09-25 | 本文“阶段 3”及实验 `evidence/invocation-message.txt` | 3.3 追踪 CLI 队列与消息角色 |
| 4. 主动加载 | 未开始 | — | — | 对比两条加载路径 |
| 5. 自主创建与生命周期 | 未开始 | — | — | 前台/后台模型判断、`skill_manage(create)`、修改与安装 |
| 6. 附件与执行 | 未开始 | — | — | 读取附件并运行脚本 |

状态可用：未开始、进行中、已完成、遇到阻碍。完成一部分时注明具体小节，不必提前勾选整个阶段。

## 我的起点

### 已表达的理解与困惑

根据开始学习前的对话：我已经读过 Skills 用户文档，大概知道这个系统是什么，但仍觉得理解不够深入，也不知道具体实现。

### 我自己的初始解释

用三五句话回答：“我现在认为 skill 是什么？输入 `/技能名` 后发生了什么？”

> 待我填写。

### 最想解决的三个问题

1. 待填写。
2. 待填写。
3. 待填写。

## 环境与复现信息

| 项目 | 本次实际值 |
| --- | --- |
| 学习日期 | 2026-09-15～2026-09-24 |
| 实际源码提交 | 最初 `f58635bd62`；阶段 1 时 `d0fd9fa10d`；官方 `upstream/main` 已同步到 `c62bd9f207`，当前学习分支合并提交 `a47466e36c` |
| 仓库绝对路径 / SKILLS_REPO | `/home/yin-hanyang/projects/hermes` |
| Python 可执行文件与版本 | `/home/yin-hanyang/.hermes/venvs/hermes-skills-learning/bin/python`；Python 3.11.16 |
| 虚拟环境 | `/home/yin-hanyang/.hermes/venvs/hermes-skills-learning` |
| SKILLS_LAB | 当前 `/tmp/hermes-skills-lab.o61rOY`；此前的实验目录均已被系统清理 |
| HERMES_HOME | `/tmp/hermes-skills-lab.o61rOY/home` |
| 实验工作目录 | `/tmp/hermes-skills-lab.o61rOY/work` |
| 使用界面 | Python 经典 CLI；2026-09-24 已核对最小配置、`meeting-summary` v2、`work/` 与 `evidence/`，尚未在新目录重复聊天入口检查 |
| 模型 / provider | 阶段 1 的旧目录曾配置 `upstage/solar-pro4:free` / `nous` 与 `xhigh`；当前新目录未配置 provider，阶段 2.2 不需要模型；不记录密钥 |
| 启用工具集 | 阶段 1 的成功会话可用 Skills 工具并实际出现 `skill_view`；当前新目录尚未启动 Agent |
| 证据保存目录 | `/tmp/hermes-skills-lab.o61rOY/evidence`；关键结论仍需写入本文，不能只依赖 `/tmp` |

计划编写时的环境探测仅供参考：仓库内未发现 `.venv/` 或 `venv/`，系统 Python 缺少 `dotenv` 和 `pytest`。开始实验时应重新核对，不能据此推断机器上没有其他环境。

### 恢复实验环境所需步骤

记录实际使用的激活命令、变量恢复命令和启动方法。不要填入占位路径后直接执行。

```text
cd /home/yin-hanyang/projects/hermes
source ~/.hermes/venvs/hermes-skills-learning/bin/activate
export SKILLS_REPO="/home/yin-hanyang/projects/hermes"
export SKILLS_LAB="/tmp/hermes-skills-lab.o61rOY"
export HERMES_HOME="$SKILLS_LAB/home"

skills_lab_cli() (
  cd "$SKILLS_LAB/work" || exit 1
  PYTHONPATH="$SKILLS_REPO${PYTHONPATH:+:$PYTHONPATH}" \
    python -m hermes_cli.main "$@"
)

以上变量和 skills_lab_cli 函数只在当前 shell 及其子进程中有效。
新终端需要重新设置；若 /tmp 中的实验目录再次被清理，则重新执行阶段 0.2。
```

### 为什么实验要创建最小 `config.yaml`

我的问题是：阶段 0.2 中的配置依据什么写出；配置 Agent 是否必须先写这样的
`config.yaml`。

代码结论：`get_config_path()` 将配置文件定位为 `HERMES_HOME/config.yaml`；
`load_config()` 先复制 `DEFAULT_CONFIG`，文件存在时才把用户配置深度合并进去。
缺少配置文件是合法的首次运行状态，因此手写这份文件不是 Agent 启动或 Skills
发现的必经步骤。正常使用通常由 `hermes setup` 和配置命令维护用户配置；本实验
直接写文件，是为了建立小而明确、可复现的实验条件。

| 实验配置 | 当前内置默认值 | 放进实验文件的目的 | 启动必需 |
| --- | --- | --- | --- |
| `skills.external_dirs: []` | `[]` | 明确不引入额外技能目录，减少发现来源 | 否 |
| `skills.inline_shell: false` | `false` | 明确关闭技能正文中的 inline shell 预处理，只观察普通技能加载 | 否 |
| `terminal.backend: local` | `local` | 明确使用本机终端后端，避免 Docker、SSH 等远端变量 | 否 |
| `terminal.cwd: <实验 work>` | `.` | 表达实验工作目录；本地 CLI 实际会采用启动进程的当前目录，所以 `skills_lab_cli` 中的 `cd` 才是主线的直接保证 | 否 |
| `display.interface: cli` | `cli` | 明确采用经典 CLI，减少 TUI 带来的路径差异；显式启动参数和 `HERMES_TUI` 仍可能覆盖它 | 否 |

证据类型是代码阅读：默认值来自 `hermes_cli/config_defaults.py`；配置路径和合并行为
来自 `hermes_cli/config.py`；本地 CLI 对工作目录的处理来自 `cli.py`。实际运行已经
确认配置文件写入隔离目录；`skills_lab_cli chat --help` 随后正常显示，证明 CLI
入口能使用专用解释器和当前仓库源码启动。帮助输出不会调用模型，因此它尚未证明
provider 或真实聊天已配置成功。

### 阶段 0 自检：四个位置的分工

我的原始回答保留如下：

1. `SKILLS_REPO` 是环境变量，指向原本的 Hermes 目录；后续通过 Python 执行 main、启动 Agent 等行为都要通过它去原本的 Hermes 目录执行。
2. Python 解释器：不确定。
3. `HERMES_HOME` 指向临时创建的 `SKILLS_LAB/home`，不是原来的 Hermes 目录，而是专为这次学习和测试创建；后续操作产生的临时结果都会放在这里。
4. `$SKILLS_LAB/work` 指向实验的 work 目录，是这次学习中 CLI 的默认工作目录。

校准后的边界：

| 对象 | 它决定什么 | 不应扩大成什么 |
| --- | --- | --- |
| `SKILLS_REPO` | 当前 checkout 的源码根目录；实验函数把它放进 `PYTHONPATH`，让 Python 从这里导入 `hermes_cli.main` 等模块 | 不是所有命令和文件操作的执行目录 |
| Python 解释器 | 哪个 Python 程序执行源码，以及使用哪个版本和哪套已安装依赖；当前是学习虚拟环境中的 Python 3.11 | 不决定 Hermes 源码、用户数据或相对路径的位置 |
| `HERMES_HOME` | Hermes 管理的用户数据根目录，例如配置、实验技能、缓存和会话状态 | 不是所有临时结果的总目录；工作文件仍可写在 `work` 或显式指定的其他路径 |
| `$SKILLS_LAB/work` | CLI 进程的当前工作目录，也是本地终端工具解析相对路径的基点 | 不是操作系统沙箱，绝对路径仍可指向目录之外 |

对 `python -m hermes_cli.main` 这次启动，可以拆成四个互不替代的选择：shell 中的
`python` 解析到学习虚拟环境的解释器；`PYTHONPATH=$SKILLS_REPO` 选择当前 checkout
里的源码；`HERMES_HOME` 选择隔离的 Hermes 数据；函数中的 `cd` 选择相对路径的
工作目录。

我随后追问“Python 解释器和 `SKILLS_REPO` 在启动 Hermes 时有什么区别”，用户指出
二者本来不是同类对象：解释器是执行 Python 的程序，`SKILLS_REPO` 是保存路径的
环境变量。这个质疑正确，原问题把不同层次的对象硬作比较，表达不清。更准确的
问题应是“启动命令分别怎样使用它们”：shell 执行虚拟环境中的 `python`；同时把
`SKILLS_REPO` 的值放入该进程的 `PYTHONPATH`，供解释器查找 Hermes 模块。
`SKILLS_REPO` 单独存在时没有 Python 或 Hermes 的特殊语义，是实验启动函数赋予了
它这个用途。阶段 0 的完成只表示环境操作和区分对象的自检已经发生，不代替用户
声称掌握了所有相关机制。

后续实际观察：在函数外执行 `echo "$PYTHONPATH"` 没有输出。这符合启动函数使用的
shell 语法：`PYTHONPATH="..." python -m hermes_cli.main` 是给单次 `python` 命令设置
环境，而不是修改父 shell。Python 进程及其子进程能看到该值，命令结束后，当前
终端原有的 `PYTHONPATH` 仍保持不变。函数外层的圆括号还会创建子 shell，因此即使
函数内部另行修改或导出变量，也不会反向写回当前终端。验证时应在带有该临时赋值
的 Python 进程内部打印 `os.environ["PYTHONPATH"]` 或模块的 `__file__`，不能用函数
执行结束后的父 shell `echo` 判断该进程当时是否收到变量。

## 阶段 1：最小技能

- [x] 已亲自核对技能 v1（文件最初由助手预置；用户已查看并解释 frontmatter 与正文的分工）。
- [x] 已记录普通请求是否主动加载技能。
- [x] 已完成一次显式斜杠调用。
- [x] 已修改为 v2，并在新会话观察结果。

| 项目 | 我的记录 |
| --- | --- |
| 操作前的预测 | 显式 `/meeting-summary` 应出现三个标题，明确记录原文中已有的日期或截止时间，并把缺失日期标记为“未说明”；普通请求对这三点均没有保证 |
| 固定的会议原文 | “今天决定先修复登录错误，再发布新版。小王负责测试，周五前完成。小李负责更新文档，截止日期未定。上线日期下周再讨论。” |
| v1/v2 的文件差异 | v1 规定“已确定的决定、待办事项、待确认的问题”；v2 只将这一行改为“决定清单、行动清单、开放问题”，其他规则未变 |
| 两次输出的关键差异 | v1 显式调用输出旧的三个标题；v2 显式调用输出“决定清单、行动清单、开放问题”。v2 保留“周五前完成”与“截止日期未定”，未编造具体上线日期 |
| 实际出现的工具调用 | v1 普通请求：模型调用 `skill_view` 一次。v1 显式调用：CLI 已展开正文，模型未再调用工具。v2 显式调用：CLI 同样已展开正文，但模型又额外调用 `skill_view` 一次；这是冗余加载，不改变显式斜杠的 CLI 预加载机制 |
| 哪些是规则，哪些是输入事实 | 技能规则：三个指定标题、待办项需负责人与截止时间、缺失信息写“未说明”、核对原文依据。用户事实：先修复登录错误再发布、小王测试且周五前完成、小李更新文档且日期未定、上线日期下周再讨论。表格或项目符号、是否冗余调用 `skill_view` 是模型执行选择 |

我的理解变化与剩余问题：1.1 已区分加载前的索引元数据与加载后的完整正文；1.2 实际观察到普通请求由模型根据已有索引选择技能，再通过 `skill_view` 加载完整正文；显式斜杠调用则由 CLI 预先展开正文。`skills_list` 负责列出技能元数据，`skill_view` 负责读取指定技能的完整内容；技能本身是被加载和遵循的指令，不是工具调用。自检中用户确认：在已有正文加载证据时，若模型编造具体上线日期，是模型执行/遵循指令层的问题，不是文件发现失败。

1.2 自检中，我最初说“`skill_view` 是让 Agent 看到 skill 列表的 tool”，并认为普通请求是“先调用 `skill_view` 看到 `meeting-summary`，再判断使用”。前半句“`skill_view` 是工具而不是技能”是对的，但“用它看列表”和判断顺序不对：技能列表由系统提示词中的索引或 `skills_list` 提供；本次模型已从索引看到 `meeting-summary` 的 `name + description`，先判断它相关，然后才调用 `skill_view(name="meeting-summary")` 读取完整正文。

v2 显式调用中的冗余 `skill_view` 还说明了消息角色边界：该会话的 system prompt 只有 `meeting-summary` 的索引条目，没有完整正文。CLI 将第一份完整正文展开在本轮 `user` 消息中；模型随后额外调用 `skill_view`，工具结果又返回第二份正文。因此第二次模型请求的整个对话上下文确实含有两份正文，但分别处于 `user` 消息和 `tool` 结果，不是 system prompt 里有两份。这会增加该回合的上下文量，但没有改写或破坏已缓存的 system prompt。

## 阶段 2：发现与解析

- [x] 已阅读解析函数和扫描函数。
- [x] 已打印实验技能的真实元数据与命令条目。
- [x] 已观察文件改名后的结果，并恢复文件。

| 数据流节点 | 实际值或摘要 | 负责的函数 | 证据类型 |
| --- | --- | --- | --- |
| 扫描根目录 | `/tmp/hermes-skills-lab.nS7b4j/home/skills`；当前没有 `create_dir` 或 external 来源 | `get_all_skills_dirs()` | 直接函数运行 |
| SKILL.md 路径 | `/tmp/hermes-skills-lab.nS7b4j/home/skills/learning/meeting-summary/SKILL.md` | 实验脚本按当前 `HERMES_HOME` 构造；扫描器枚举精确命名的 `SKILL.md` | 直接函数运行 |
| frontmatter | `name: meeting-summary`；`description: Use when summarizing meeting notes.`，返回为 Python 字典 | `parse_frontmatter()` | 直接函数运行 |
| Markdown 正文 | 与 frontmatter 分离，从 `# Meeting Summary Skill` 开始，包含 v2 的三个标题规则与 Verification | `parse_frontmatter()` 返回值中的 `body` | 直接函数运行 |
| 命令键与条目 | `/meeting-summary` → `name`、`description`、`skill_md_path`、`skill_dir`，路径均指向当前隔离目录 | `scan_skill_commands()`、`_scan_skill_md()` | 直接函数运行 |

我原以为：待填写。

文件改名实验的实际结果：用户运行带自动恢复的受控命令并报告已确认预期，即文件改名
为 `SKILL.md.disabled` 后 `/meeting-summary` 条目消失。随后只读核对确认 `SKILL.md`
已经恢复、`.disabled` 不存在，重新扫描再次得到包含名称、说明和两个定位路径的命令条目。

现在如何解释“文件存在但技能没出现”：先区分“文件在磁盘上”和“扫描器枚举到了它”。
主文件必须精确命名为 `SKILL.md` 并位于实际扫描根下，才会传入 `_scan_skill_md()`；到达
该函数后还需通过路径、平台/环境、禁用、同名优先级、slug 有效性及命令冲突等过滤。
因此应先定位失败发生在枚举前还是过滤后，不能直接归因于模型行为。

2.3 运行前预测：用户预测把 `SKILL.md` 改为 `SKILL.md.disabled` 后，
`scan_skill_commands().get("/meeting-summary")` 返回空值；结果预测正确，但最初认为
改名文件仍会传入 `_scan_skill_md()`，再被该函数跳过。源码校准是扫描迭代器只枚举
精确名为 `SKILL.md` 的文件，因此改名文件根本不会到达 `_scan_skill_md()`。这与配置
中的 disabled skill 不同：后者仍被枚举，然后才由 `_scan_skill_md()` 根据 disabled
名称集合过滤。

2.2 的完成与证据边界：用户指出仅执行命令不等于完成学习；随后已把输出对应回
“扫描根目录 → `SKILL.md` → frontmatter/body → 命令键与条目”，并正确说明命令条目
只保存名称、说明和定位信息而不保存正文，因此 2.2 完成。本次没有启动 Agent 或调用
模型，只能证明实验文件可被当前代码解析并生成斜杠命令条目，不能证明模型会主动
选择、加载或正确遵循该技能。

2.2 自检中的原始解释与修正：用户正确指出 `_scan_skill_md()` 构造的条目只有
`name`、`description` 和定位信息，没有正文；但随后推断显式执行
`/meeting-summary` 时模型还需要调用读取正文的工具。这个推断混合了两条路径：显式
斜杠调用由 CLI 使用条目中的路径，在模型请求前通过共享 loader 读取正文并展开进本轮
用户消息，模型不必再发出 `skill_view`；普通请求下的主动加载才通常由模型从索引判断
相关性后调用 `skill_view`。因此“命令条目不含正文”表示发现层保持轻量、加载层按需
读取，并不表示两种入口都由模型触发读取。用户进一步指出这条 loader 控制流尚未在
阶段 2 学习；计划核对确认它属于阶段 3.1～3.2，而两条加载路径的系统比较属于阶段 4。
本轮只把它作为纠正错误推断的预告，不作为 2.2 的掌握要求。后续教学应保持阶段边界：
可以标明后续结论，但不能用尚未学习的控制流考核当前阶段。

### 阶段 2 最终自检

- **用户已经建立的数据流：** Hermes 从技能根目录枚举精确命名的 `SKILL.md`，
  `scan_skill_commands()` 把文件交给 `_scan_skill_md()`；后者解析 frontmatter、应用过滤、
  将技能名规范化成 `/slug`，并生成包含 `name`、`description`、`skill_md_path`、
  `skill_dir` 的命令条目。最终返回值是命令键到条目的字典。
- **目录来源校准：** 当前斜杠扫描的实际顺序是 trusted project → 当前 profile-local →
  external；仅由 `skills.create_dir` 引入的目录因已复现的遗漏没有进入这条扫描路径。
  profile-local 应理解为当前有效 `HERMES_HOME/skills`，不总是字面上的
  `~/.hermes/skills/`。
- **trusted project 的边界：** Agent 的工作目录只用于向上寻找最近的 Git 根目录，不能
  自动使项目受信。该根目录还必须显式列在 `skills.trusted_project_dirs` 中，且
  `skills.project_discovery` 没有关闭；之后只发现根目录下现有的 `.hermes/skills` 和
  `.agents/skills`。受信项目技能仍要经过项目技能安全扫描，不是对工作目录下任意文件
  的自动加载。
- **信任与放置原则：** Hermes 的项目 trust 通常由用户对每个仓库执行一次
  `hermes skills trust [path]`，命令把根目录写入配置，无需手改 YAML，也不应自动信任
  任意 clone。跨项目适用的个人工作流放 profile-local skills；只对当前仓库成立、需要
  随代码版本化和团队共享的可重复工作流优先放项目 `.agents/skills`（Hermes 专用时可用
  `.hermes/skills`）；每次会话都必须知道的项目规则应写 `AGENTS.md`，而不是做成按需
  加载的 skill。
- **frontmatter 校准：** 标准边界是三个 ASCII 连字符 `---`。`name` 和 `description`
  是规范技能通常应提供的元数据，但 `_scan_skill_md()` 对缺失值有 fallback：名称可取
  父目录名，说明可取正文首个非标题行或默认说明；因此“缺少字段”不能直接当作命令
  消失的充分原因。
- **分层排查结论：** 先检查扫描根与精确文件名，确认文件是否被枚举；再检查路径排除、
  platform/environment、配置禁用、同名 first-wins、slug 有效性、核心命令/slug 冲突等
  过滤；只有进入后续模型阶段后，才讨论模型是否选择或遵循技能。
- **完成判断：** 用户能够解释“目录 → 文件 → frontmatter/body → 命令键与条目”，并用
  改名实验区分枚举失败和 `_scan_skill_md()` 内部过滤；阶段 2 完成。

2026-09-18 进度纠正：用户确认此前只完成到阶段 1；上一会话曾把“先由用户说明
`parse_frontmatter()`”口头称作阶段 2.0。本轮确认它适合作为 2.1 的开场自检，但没有
必要成为计划外的正式小节。助手此前单方面执行的后续源码核对和实验仍不计入用户
学习进度；阶段 2 从用户实际说明该函数时才开始。

### 2.1 开场自检：`parse_frontmatter()`

- **用户的原始说明：** “`parse_frontmatter()` 就是一个把 `SKILL.md` 拆成‘元数据’和‘正文’的函数，其中元数据是 YAML 格式。”
- **关键理解：** 该函数接收已经读出的文本，把 YAML frontmatter 解析成 Python `dict`，并把剩余 Markdown 作为正文 `str` 返回；它本身不负责打开 `SKILL.md`。这一步让后续发现逻辑可以使用 `name`、`description` 等元数据，而不必把完整正文当成索引。
- **实际出现的理解修正：** “元数据是 YAML 格式”应收窄为“文件中用 YAML 表示，返回后是 Python 字典”；用户曾把 `{}` 称为“空数组”，已校准为空字典。用户能够正确判断边界缺失时返回空字典和正文、成功分割后不保留 `---`，以及异常时存在简单的 `key:value` fallback。
- **记录边界：** BOM、正则空白范围、切片下标等属于本次当场核对的基础实现细节，不再逐项保留；除非以后它们成为实际发现失败的原因。
- **阶段性判断：** `parse_frontmatter()` 初步自检完成；目录函数和扫描函数尚未完成，因此阶段 2 的总检查项仍不勾选。

### 2.1 目录来源：`get_all_skills_dirs()`

- **用户的说明：** 返回顺序是 Hermes 默认的本地 skills 目录、配置的 `create_dir`、`get_external_skills_dirs()` 提供的外部目录。
- **关键补充：** `create_dir` 只有实际存在且为目录时才加入，外部目录不会重复加入已有路径。trusted project skills 有意不在这个函数中，而由独立项目发现路径处理，以承载更高优先级和项目技能安全检查。
- **`create_dir` 的行为边界：** 它是新技能创建位置的可选重定向，不是启用 Agent 创建技能的开关。未配置时，`skill_manage` 把新技能写入当前 profile 的本地 skills 目录；配置后才改写到 `create_dir`。现有技能是否可被查找和修改由发现与管理路径决定，不由“是否位于 `create_dir`”定义所有权。
- **用户发现的路径差异：** `scan_skill_commands()` 实际只组合 trusted project、当前 profile-local 和 `get_external_skills_dirs()`，没有使用 `get_all_skills_dirs()`，因此遗漏了仅通过 `skills.create_dir` 配置的目录。`tools.skills_tool._skill_search_dirs()` 也存在同样遗漏。
- **设计意图与代码证据：** 引入 `create_dir` 的提交 `42c2838674` 明确要求创建后的技能被发现、读取和修改；用户文档进一步声明它应进入索引、`skills_list`、`skill_view` 和斜杠命令。在官方 `upstream/main` 提交 `c62bd9f207` 上，`scan_skill_commands()` 和 `tools.skills_tool._skill_search_dirs()` 仍各自手工组合目录，仍然未包含 `create_dir`。
- **最新代码上的运行证据：** 在临时 `HERMES_HOME` 中配置 `skills.create_dir`，并只在该目录放置 `create-only-skill`。`get_all_skills_dirs()` 包含该目录，系统提示词索引也包含该技能；但 `skills_list()` 不列出它，`skill_view()` 返回 `Skill 'create-only-skill' not found.`，`scan_skill_commands()` 也没有注册 `/create-only-skill`。因此“真实实现遗漏”已从代码推断升级为运行复现结论。
- **测试缺口：** 规范脚本 `scripts/run_tests.sh tests/tools/test_skill_create_dir.py -q` 在同步后代码上 16/16 通过，但该文件仍只覆盖 `get_all_skills_dirs()`、创建与再次修改，没有覆盖 `skills_list`、`skill_view` 或斜杠扫描；所以绿色测试不否定上述复现。
- **上游协作状态：** 官方 issue `#108157` 已经记录了同一问题，其作者也已提交修复 PR `#108160`；截至 2026-09-18 两者均为 Open。因此这是适合贡献的真实 bug，但当前不适合再独立提交相同修复；可以通过复核 PR、提供新的测试证据、与作者协作，或选择其明确列为 out-of-scope 的后续问题参与。
- **用户对 `_scan_skill_md()` 的说明：** 会跳过位于排除路径中的文件、平台或环境不匹配的技能、已见同名或配置禁用的技能，以及无法生成有效命令名的技能；成功条目包含 `name`、`description`、`skill_md_path`、`skill_dir`。
- **关键补充：** 还会跳过与 Hermes 核心命令/别名冲突的 slug，以及不同技能名规范化后产生的重复命令键。`description` 优先使用 frontmatter，缺失时取正文首个非标题行，最后才生成默认说明。
- **用户对扫描与缓存的说明：** `scan_skill_commands()` 真正执行扫描；`get_skill_commands()` 可能复用结果，在命令表为空，或当前 platform / Hermes home 与缓存标签不一致时重新扫描。
- **关键补充：** 这里的 home 是当前有效 profile 的 Hermes home，而不只是某个本地 `skills/` 路径。`scan_skill_commands()` 扫描完成后一次性发布命令表及 platform/home 标签；空结果因 `bool(commands)` 为假，不会被当作可复用的 fresh cache。
- **2.1 完成判断：** 用户已沿实际代码说明 frontmatter 解析、目录来源、扫描集合、单文件过滤、命令条目及缓存复用边界；进入 2.2 的直接函数实验。

## 阶段 3：消息构造

- [x] 已保存实际生成的 invocation message。
- [x] 已标注正文、目录提示和用户任务的来源。
- [ ] 已追踪 CLI 队列和进入对话的位置。

### 3.1 运行前预测与直接函数证据

用户的原始说法：“我没有看`build_skill_invocation_message` 的实现所以我不知道消息具体如何构造。但我预测肯定会有skill的正文，来自`SKILL.md`，`/meeting-summary` 中对应的四个键值对信息应该也会有。`"小王周五前完成测试；上线日期下周再讨论。"` 这条用户消息肯定也有，应该会放在消息最后。”

**直接函数运行证据：** 用户在重建后的 `/tmp/hermes-skills-lab.o61rOY` 运行 3.1，输出包含激活说明、v2 技能内容（含 `name`、`description`）、技能绝对目录及相对路径提示，最后是用户任务；消息保存到 `evidence/invocation-message.txt`，只读核对该文件存在。四个命令条目字段并未逐项作为键值对输出：`skill_md_path` 不出现，`skill_dir` 对应的目录以提示文字出现。此次没有模型调用或对话角色证据。

**用户的解释与源码校准：** 用户说：“不是，仅从`skill_md_path` 没出现在消息中，不能判断 Hermes 没有用命令条目定位技能文件。可能函数以及解析并读取了路径，只是这个路径不需要直接出现在最终构造的消息里而已。”这个证据边界判断正确。当前 `build_skill_invocation_message()` 实际取命令条目的 `skill_dir` 交给 `_load_skill_payload()`，并没有直接取 `skill_md_path`；内部定位信息不必逐项出现在返回字符串中。用户已对照预测、输出与这一边界作出解释，3.1 完成。3.2 将继续追踪 loader；当前计划中从入口直达 `_build_skill_message()` 的草图少了生产代码的 `_render_skill_block()` 一层。

### 3.2 `skill_view()` 返回 JSON 字符串的边界

用户的原始解释是：“因为`skill_view()` 的返回值是一个json字符串，需要把它转换为python字典以方便处理。”随后追问为什么 `skill_view()` 不直接返回 Python `dict`。源码事实：`skill_view()` 构造含 `success`、`content`、路径等字段的结果，并经 `_json()` 序列化为字符串；它注册为模型可调用的工具。当前工具 registry 的普通结果契约接受字符串，普通 `dict` 会作为不支持的返回类型报错（多模态结果是单独例外）。斜杠命令在 Python 内直接复用这个工具函数，`_load_skill_payload()` 因而用 `json.loads()` 恢复字典以检查成功状态、读取内容和目录。这是 Hermes 工具接口的约定，不是 Python 函数不能返回字典，也不意味着再次调用了模型。3.2 的完整加载与拼接链尚未核对。

随后用户核对 `_load_skill_payload()` 的成功返回值：“loaded_skill的完整结果，skill_dir，loaded_skill中的\"name\"字段或normalize_skill_lookup_name得到的skill名，其中skill_dir直接对应`[Skill directory: ...]`。”源码与此一致：返回的是已解析的结果字典、`Path | None` 类型的技能目录，以及显示名称；构造消息时以目录值生成该提示。

用户对拼接顺序的解释：`parts` 先放技能内容和目录提示，后放 `user_instruction`；前者作为可复用的 `stable_prefix`，后者及 `runtime_note` 是变化的尾部。源码进一步限定：`append_user_instruction()` 返回的前缀结束于固定的用户指令引导文字，实际任务文字不在前缀内；`register_stable_prefix()` 只是登记缓存边界，真正的请求期标记由 `agent/prompt_caching.py` 处理。3.1 的直接运行不证明发生过缓存命中。

用户的 3.2 调用链自检：“slash command → `build_skill_invocation_message()` → 找到并加载 skill → `_render_skill_block()` → 记录 skill 使用次数 → `_build_skill_message()` → 生成最终 message。”源码校准：入口通过 `get_skill_commands()` 找条目，以条目的 `skill_dir` 调 `_load_skill_payload()`；`_render_skill_block()` 拆开三项结果并尽力记录使用，再委托 `_build_skill_message()` 拼接。入口本身最终返回字符串，并非只“准备调用”。当前生产代码比计划草图多了 `_render_skill_block()` 一层；3.2 完成。尚未追踪 CLI 怎样接收该字符串和赋予消息角色。

消息证据路径：`/tmp/hermes-skills-lab.o61rOY/evidence/invocation-message.txt`。

```text
在这里粘贴必要片段，不必重复整份长正文。
```

| 问题 | 我的答案 | 代码或运行证据 |
| --- | --- | --- |
| 谁识别斜杠命令？ | 待填写 | 待填写 |
| 谁读取技能文件？ | 待填写 | 待填写 |
| 返回值什么时候还是字符串？ | 待填写 | 待填写 |
| 谁决定它的消息角色？ | 待填写 | 待填写 |
| 是否改写了已有系统提示词？ | 待填写 | 待填写 |

我的调用关系草图：待填写。

我的理解变化与剩余问题：待填写。

## 阶段 4：主动加载

- [ ] 已观察索引构造函数输出与完整正文的区别。
- [ ] 已阅读工具 schema 和注册入口。
- [ ] 已对比显式调用与模型调用 skill_view。
- [ ] 已尝试不提技能名称的请求，并如实记录是否触发。

| 观察项 | 显式调用 | 主动加载 |
| --- | --- | --- |
| 用户输入 | 待填写 | 待填写 |
| 是否为新会话 | 待填写 | 待填写 |
| 加载触发者 | 待填写 | 待填写 |
| skills_list 是否被调用 | 待填写 | 待填写 |
| skill_view 参数 | 待填写 | 待填写 |
| 正文进入对话的形式 | 待填写 | 待填写 |
| 运行证据位置 | 待填写 | 待填写 |

我如何区分“模型知道技能存在”和“模型已经读取技能”：待填写。

## 阶段 5：自主创建、修改与安装

- [ ] 已区分前台模型自主判断、后台 review 和用户明确要求创建技能。
- [ ] 已核对 `skill_manage(create)` 的模型参数、工具结果与实际文件；若未发生自主创建，已保留这一证据缺口。
- [ ] 已通过模型修改现有技能，并比较文件差异及新会话加载结果。
- [ ] 已阅读一条 Hub 安装路径。
- [ ] 已阅读创建、共享加载器与附件提示的现有测试，说明断言与 mock 的范围。

| 路径 | 模型判断或用户指令证据 | `skill_manage` 调用与结果 | 实际文件与新会话发现 |
| --- | --- | --- | --- |
| 前台自主判断 | 待观察 | 待观察 | 待观察 |
| 后台 review | 待观察 | 待观察 | 待观察 |
| 明确 `/learn`（如需） | 待观察；不作为自主判断证据 | 待观察 | 待观察 |
| 修改现有 `meeting-summary` | 待观察 | 待观察 | 待观察 |

修改前文件、实际工具参数与磁盘差异：待填写。

| 安装环节 | 对应函数 | 输入与输出 | 是否实际执行过 |
| --- | --- | --- | --- |
| 来源解析与下载 | 待填写 | 待填写 | 待填写 |
| 暂存与扫描 | 待填写 | 待填写 | 待填写 |
| 策略判断与安装 | 待填写 | 待填写 | 待填写 |
| 锁文件记录 | 待填写 | 待填写 | 待填写 |
| 后续扫描 | 待填写 | 待填写 | 待填写 |

已有测试阅读记录及规范测试运行结果（未运行就写未运行）：待填写。

## 阶段 6：附件与脚本

- [ ] 已创建参考文件，并观察其读取结果。
- [ ] 已核对参考文件与主技能当前的缺失信息规则一致。
- [ ] 已手动运行三行文本的统计脚本。
- [ ] 已观察模型实际执行脚本的调用。
- [ ] 已记录一次缺失脚本的行为，并恢复脚本。

| 实验 | 预期 | 实际观察 | 判断发生在哪一层 |
| --- | --- | --- | --- |
| 读取参考文件 | 待填写 | 待填写 | 待填写 |
| 手动统计三行文本 | 待填写 | 待填写 | 待填写 |
| 模型按技能运行脚本 | 待填写 | 待填写 | 待填写 |
| 脚本临时改名 | 待填写 | 待填写 | 待填写 |
| 恢复后重试 | 待填写 | 待填写 | 待填写 |

实际终端命令、返回值与错误片段：待填写。

“技能加载成功不代表任务成功”的个人解释：待填写。

## 可重复使用的单次学习记录

每次学习可以复制以下模板，追加到本文末尾。

### 日期 / 阶段 / 本次问题

- **今天只解决什么：**
- **操作前我的猜测：**
- **读了哪些函数：**
- **实际执行的步骤或命令：**
- **原始输出或证据路径：**
- **代码能确认什么：**
- **实验实际确认什么：**
- **哪些仍只是推测：**
- **现在我会怎样解释：**
- **下一步最小动作：**

### 2026-09-15 / 阶段 0 / 实验环境变量为什么临时导出

- **今天只解决什么：** 为什么用 `export SKILLS_REPO="$PWD"`，以及关闭终端后如何继续实验。
- **操作前我的猜测：** 用户指出这些变量可能在重开 terminal 和会话后全部消失。
- **读了哪些函数：** 本次没有阅读 Skills 源码；讨论的是 shell 环境变量机制和实验隔离约定。
- **实际执行的步骤或命令：** 在仓库根目录导出 `SKILLS_REPO`、`SKILLS_LAB`、`HERMES_HOME`，随后由 Python 子进程打印出对应路径。
- **原始输出或证据路径：** 源码目录为 `/home/yin-hanyang/projects/hermes`；实验目录为 `/tmp/hermes-skills-lab.YL4I5Y`；数据目录为 `/tmp/hermes-skills-lab.YL4I5Y/home`；工作目录为 `/tmp/hermes-skills-lab.YL4I5Y/work`。
- **通用机制能确认什么：** `export` 使变量对当前 shell 启动的子进程可见；`SKILLS_REPO="$PWD"` 在赋值时保存当时的仓库路径，之后切换工作目录不会随之变化。关闭当前终端后，变量和 shell 函数不会自动出现在新终端。
- **实验实际确认什么：** Python 子进程成功读取了三个导出的变量，且实验配置写入隔离的 `HERMES_HOME`。
- **哪些仍只是推测：** 尚未实际重开终端验证恢复流程；`/tmp` 目录何时被系统清理由宿主环境决定。
- **现在我会怎样解释：** 临时导出让源码位置、实验数据位置和运行目录彼此独立，并避免实验用 `HERMES_HOME` 泄漏到日常 Hermes 会话。新终端需要使用已记录的真实路径显式恢复；若临时目录消失，则创建新实验目录。
- **下一步最小动作：** 用自己的话区分源码目录、Python 解释器、`HERMES_HOME` 和聊天工作目录，再进入阶段 1.1。

### 2026-09-15 / 阶段 0 / `PATH`、解释器与 `PYTHONPATH` 的混乱和最终理解

- **今天只解决什么：** 弄清虚拟环境、Python 解释器、`PATH`、`PYTHONPATH`、`sys.path` 和 `SKILLS_REPO` 在启动命令中各自处于哪一层。
- **核心的初始混乱：** 一开始没有分清 `PATH` 和 `PYTHONPATH`，把它们都理解成了某种“Python 的路径”。因此看到“虚拟环境已激活，所以实际启动的是 `~/.hermes/venvs/hermes-skills-learning/bin/python`”时，会拿这个解释器路径与指向源码仓库的 `PYTHONPATH` 对照，并因为二者不一致而无法理解解释器路径从哪里来。
- **由此产生的追问：** “这个 Python 的路径是什么时候被设置的？”“它和 `PYTHONPATH` 是什么关系？”“给这个 Python 进程设置 `PYTHONPATH` 又是什么意思？”后来又观察到启动函数写了 `PYTHONPATH=... python -m hermes_cli.main`，但函数结束后在当前终端执行 `echo "$PYTHONPATH"` 没有输出，使这个疑问更加具体。
- **中间的表述障碍：** 把 Python 解释器和 `SKILLS_REPO` 直接拿来比较时，感觉二者一个是程序、一个是保存路径的变量，本来就不是同类对象，因此“二者在启动时有什么区别”也难以回答。
- **解开混乱的关键：** 完整回答先区分两个名字相似但作用不同的变量，再把启动过程拆成两个查找阶段。Shell 先根据 `PATH` 找到要启动的 `python` 可执行文件；Python 启动后，再根据 `sys.path` 查找 `hermes_cli.main`，而命令收到的 `PYTHONPATH` 会成为 `sys.path` 的来源之一。解释器路径不需要、也不应该与 `PYTHONPATH` 相符。
- **虚拟环境的作用：** `source <venv>/bin/activate` 把虚拟环境的 `bin` 放到当前 shell 的 `PATH` 前面，所以输入 `python` 时，Shell 先找到学习虚拟环境的解释器。该解释器决定 Python 版本以及默认使用的标准库和 `site-packages` 依赖。
- **`SKILLS_REPO` 的作用：** 它只是实验自定义的路径变量，本身对 Shell、Python 和 Hermes 都没有特殊语义。启动函数把它拼进命令级 `PYTHONPATH` 后，它才帮助解释器从当前 checkout 查找 Hermes 模块。
- **命令级环境变量的作用域：** `变量=值 命令` 只把该值交给这一次命令及其子进程，不修改父 shell。因此 Python 进程内部可以读取 `PYTHONPATH`，进程结束后父 shell 中的 `echo "$PYTHONPATH"` 仍然为空；函数使用圆括号创建子 shell，又进一步阻止内部环境变化回写父 shell。
- **最终理解：** `PATH` 回答“Shell 启动哪个 Python”；解释器回答“谁执行代码、使用哪个 Python 版本和依赖”；`PYTHONPATH` 回答“给这个 Python 增加哪些模块搜索目录”；`sys.path` 是 Python 实际使用的模块搜索路径集合；`SKILLS_REPO` 是被启动函数放进 `PYTHONPATH` 的源码目录值。
- **实际运行证据：** `sys.executable` 已显示 `/home/yin-hanyang/.hermes/venvs/hermes-skills-learning/bin/python`；`skills_lab_cli chat --help` 正常显示；父 shell 中 `echo "$PYTHONPATH"` 没有输出。附带文字中的进程内 `PYTHONPATH` / `sys.path` 打印命令是解释和后续复现实验，本次没有报告其实际输出。
- **理解变化：** 真正的转折点不是单独记住每个变量的定义，而是看清“激活虚拟环境修改 `PATH` → Shell 选择解释器 → 命令给该进程临时传入 `PYTHONPATH` → Python 通过 `sys.path` 查找模块”的完整顺序。用户在读完这段完整解释后，明确表示“总算理解了这部分内容”。
- **下一步最小动作：** 进入阶段 1.1，在隔离的 `HERMES_HOME` 中创建 `meeting-summary` 最小技能。

### 2026-09-15 / 概念澄清 / “当前没有系统级长期记忆接口”是什么意思

- **今天只解决什么：** 区分当前 Codex 对话宿主、Hermes Agent 的长期记忆机制，以及项目笔记文件这三个层次。
- **操作前我的猜测：** “没有系统级长期记忆接口”可能会被理解成 Hermes 完全没有跨会话记忆。
- **读了哪些函数：** `agent/memory_provider.py::MemoryProvider`、`agent/memory_manager.py::MemoryManager`、`agent/agent_init.py` 中内置记忆和外部 provider 的初始化路径、`agent/system_prompt.py::_memory_parts`、`agent/turn_context.py::_memory_turn_start_and_prefetch`、`agent/turn_finalizer.py` 的回合完成同步，以及 `tools/memory_tool.py::memory_tool`。
- **实际执行的步骤或命令：** 只读检索并阅读上述源码和 `website/docs/developer-guide/memory-provider-plugin.md`；本次未运行 Hermes，也未调用真实 memory provider。
- **本会话证据能确认什么：** 当前 Codex 宿主向助手提供的工具中没有独立的长期记忆读写工具；因此助手不能把信息写入一个由宿主自动跨会话召回的隐藏记忆库。项目文件（例如本文）仍可在获准后显式读写，但这是文件持久化，不是宿主级记忆服务。
- **代码能确认什么：** 当前 Hermes 源码并非“没有长期记忆接口”。它有内置的 `MEMORY.md` / `USER.md` 文件记忆和模型侧 `memory` 工具，也有面向外部后端的 `MemoryProvider` ABC、`MemoryManager` 生命周期编排及插件发现/注册机制。外部 provider 可实现 `prefetch`、`sync_turn`、工具 schema 与工具调用等能力，并通过 `memory.provider` 单选启用。
- **哪些仍只是推测：** 原句出现时“系统级”究竟特指 Codex 宿主、Hermes 核心，还是“任意 skill 都能直接调用的统一业务 API”，需要结合原句上下文确认；本次没有验证某个具体 provider 的网络端到端行为。
- **现在我会怎样解释：** “系统级长期记忆接口”通常指由运行时统一拥有、可持久化、可跨会话检索并自动注入上下文的稳定契约，而不是把文字写进当前聊天或普通 Markdown。对当前 Codex 宿主来说，本会话没有暴露该接口；对 Hermes 来说，该契约已经存在，只是内置文件记忆与外部 provider 接口分成了两条路径。
- **下一步最小动作：** 若目标是学习 Skills，继续阶段 0.3；若目标是扩展记忆，先用一个最小的目录式 `MemoryProvider` 插件验证 `register -> initialize -> sync_turn -> prefetch`，不要新增 core tool。

### 2026-09-16 / 阶段 1 准备 / 临时实验恢复与最小技能 v1 预置

- **今天只解决什么：** 在原 `/tmp` 实验目录消失后恢复可复现环境，并为阶段 1 预置 `meeting-summary` v1。这是环境准备，不代表用户已完成 1.1。
- **操作前的状态：** 记录中的 `/tmp/hermes-skills-lab.YL4I5Y` 已不存在；学习虚拟环境仍存在；仓库已从记录时的 `f58635bd62` 前进到 `d0fd9fa10d`。用户尚未对本阶段输出做预测。
- **实际执行的步骤或命令：** 新建 `/tmp/hermes-skills-lab.VuNjyi`，重建 `home`、`work` 和 `evidence`；写入最小 `config.yaml`；在 `home/skills/learning/meeting-summary/SKILL.md` 保存计划中的 v1；用显式 `HERMES_HOME` 和 `PYTHONPATH` 运行导入检查及 `chat --help`。
- **原始输出或证据路径：** Python 实际路径为 `/home/yin-hanyang/.hermes/venvs/hermes-skills-learning/bin/python`；`hermes_cli.main.__file__` 为 `/home/yin-hanyang/projects/hermes/hermes_cli/main.py`；CLI 帮助保存于 `/tmp/hermes-skills-lab.VuNjyi/evidence/chat-help.txt`；v1 位于 `/tmp/hermes-skills-lab.VuNjyi/home/skills/learning/meeting-summary/SKILL.md`。
- **实验实际确认什么：** 新路径下的学习解释器能导入当前 checkout 的 CLI，帮助入口可运行，v1 文件已实际落盘。这些只是助手完成的环境与文件操作，不能作为用户已完成 1.1 的证据。
- **不能由此确认什么：** `chat --help` 不调用模型，因此尚未证明 provider 可用、普通请求会否主动加载技能，或斜杠调用的输出是否符合 v1。
- **现在我会怎样解释：** `/tmp` 实验目录是可丢弃的运行数据，学习记录才是恢复路径和区分历史证据的持久依据；重建后必须用新路径，不能把旧路径当成仍然有效。
- **用户的纠正：** 用户指出对 1.1 没有执行印象，因此不应直接进入 1.2。该纠正成立：助手代为写入文件不等于用户经历了学习步骤。
- **下一步最小动作：** 回到 1.1，由用户查看并核对 v1 的实际路径、frontmatter 和三条正文规则；在用户确认前不进入 1.2。

### 2026-09-16 / 阶段 1.1 / frontmatter 与正文的分工

- **今天只解决什么：** 亲自核对 v1，区分模型在技能加载前能看到的索引线索，与加载后才能看到的任务说明。
- **用户的原始解释：** frontmatter 中的 `description` 描述“技能是什么”，在调用前就被 Agent 看到；Agent 根据 `name` 和 `description` 决定是否加载。“将用户提供的会议记录整理为中文摘要”也在描述技能，但是正文总结句，加载后才可见，不决定是否加载。`# Meeting Summary Skill` 之后的正文，包括 `Procedure` 和 `Verification`，决定技能怎样完成任务。
- **代码核对：** `agent.prompt_builder.build_skills_system_prompt()` 为系统提示词生成紧凑技能索引；当前索引条目使用 frontmatter 的 `name` 和截断后的 `description`，并要求模型对匹配或部分相关的技能调用 `skill_view(name)`。`tools.skills_tool.skill_view()` 读取主 `SKILL.md` 并在结果的 `content` 中返回完整内容。
- **需要收窄的表述：** “Agent 根据 `name` 和 `description` 决定是否加载”适用于普通请求下的模型主动加载路径，且是提示词影响下的模型选择，不是确定性的程序条件。对显式 `/meeting-summary`，加载由用户输入和 CLI 命令路径触发，不需要模型先根据描述作出选择。
- **完成判断：** 用户已亲自查看 v1，并能准确解释 frontmatter、正文总结句、`Procedure` 和 `Verification` 的不同作用；阶段 1.1 完成。
- **下一步最小动作：** 进入 1.2，先记录对普通请求与显式斜杠调用的预测，再做两个新会话的对照。

### 2026-09-16 / 阶段 1.2 / 运行前预测

- **今天只解决什么：** 在看到模型结果前，先记下对普通请求与显式斜杠调用的可检验预测。
- **用户的原始预测：** “`/meeting-summary` 会：1. 正常有三个标题；2. 原文中有的日期或截止时间会明确记录，原文中没有的日期会标记未说明；3. 会留下技能加载痕迹。普通请求以上三点均没有保证。”
- **预测的优点：** 它分别覆盖可观察的输出结构、缺失信息处理和加载证据；对普通请求使用“没有保证”，保留了模型主动加载技能的可能性。
- **尚待实验回答：** 两次实际输出是什么，普通请求是否主动加载，显式斜杠调用在当前 CLI 中留下什么证据。
- **下一步最小动作：** 使用同一模型和工具集启动两个新会话，先运行普通请求，再运行 `/meeting-summary`，保存原始输出。

### 2026-09-16 / 阶段 1.2 / 基线尝试在 provider 检查处终止

- **今天只解决什么：** 尝试运行普通请求基线，并确定请求未成功时停在哪一层。
- **第一次尝试：** 启动命令遗漏实验 `HERMES_HOME`，CLI 因而尝试初始化日常 `/home/yin-hanyang/.hermes/cron`，并在只读文件系统处报 `HomeInitializationError`。这次尝试没有进入技能扫描或模型回合，不算基线对话结果。
- **第二次尝试：** 显式传入 `HERMES_HOME=/tmp/hermes-skills-lab.VuNjyi/home` 和当前仓库 `PYTHONPATH`，在实验 `work` 目录以 `--toolsets skills,terminal --verbose --oneshot` 发送固定会议原文。
- **实际输出：** CLI 报告 `Hermes is not connected to any AI provider yet.`，建议使用 `hermes model`、聊天中的 `/login`、`hermes auth add <provider>` 或在实验 `.env` 中配置 provider 密钥。
- **实验能确认什么：** 隔离的 `HERMES_HOME` 目前没有可用 provider；请求在 Agent 回合和工具调用之前终止，因此当前没有普通请求输出，也没有技能是否被主动加载的证据。
- **不应下的结论：** 不能因没有加载痕迹就说普通请求不会加载技能，也不能说 `meeting-summary` 发现或加载失败；这些路径尚未执行。
- **下一步最小动作：** 在该隔离 `HERMES_HOME` 中配置一个可用 provider，然后用相同命令重做普通请求；在此之前不运行显式斜杠对照。

### 2026-09-16 / 阶段 1.2 / provider 已配置

- **用户实际操作：** 在隔离的 `HERMES_HOME` 中完成模型选择。
- **实际配置结果：** 默认模型为 `upstage/solar-pro4:free`，`model.provider=nous`，推理强度为 `xhigh`；配置写入 `/tmp/hermes-skills-lab.VuNjyi/home/config.yaml`。
- **证据边界：** 这证明 provider 和默认模型选择已持久化，但尚未证明真实推理请求成功，也尚未观察技能加载。
- **下一步最小动作：** 使用该模型、`xhigh` 和 `skills,terminal` 工具集重做普通请求，再在独立新会话中做显式斜杠调用。

### 2026-09-16 / 阶段 1.2 / 真实模型请求的网络与外发边界

- **实际执行：** 使用已配置的 `nous` provider、`upstage/solar-pro4:free`、`xhigh` 和 `skills,terminal` 启动普通请求的独立 one-shot 会话。
- **实际进展：** Agent 已成功初始化，并确认了 provider、模型和工具集；随后连接 `https://inference-api.nousresearch.com/v1` 时因当前执行沙箱无法解析域名而三次失败。会话记录显示 1 条用户消息、0 次工具调用，没有模型回答。
- **升级请求结果：** 尝试申请在沙箱外连接 Nous，安全审查因会议文本可能属于私有内容而拒绝，要求用户在知道目标为 Nous 推理服务后明确授权该负载的外发。本次未绕过审查，也未将负载成功发送给模型。
- **证据边界：** 可以确认实验配置和 Agent 初始化链路已通；不能确认模型是否主动加载技能，也不能评价输出是否符合预测。
- **下一步最小动作：** 由用户明确决定是否允许将计划中的固定教学会议文本发送到 Nous 推理服务；获得授权后重跑两个独立会话。

### 2026-09-16 / 阶段 1.2 概念深入 / 系统提示词技能索引与 `skills_list`

- **用户的问题：** 如果系统提示词已经自动包含 `meeting-summary` 等技能索引，这个索引如何构造，`skills_list` 工具是否因而多余。
- **构造路径：** `agent.system_prompt.build_system_prompt_parts()` 调用 `_skills_prompt(agent)`；只有当当前 Agent 的有效工具中至少有 `skills_list`、`skill_view` 或 `skill_manage` 之一时，才调用 `agent.prompt_builder.build_skills_system_prompt()`。后者以 Agent 自己的 profile `skills/` 为本地根，结合项目技能目录和外部目录，读取 `SKILL.md` frontmatter，取 `name` 和最多 60 字符的 `description`，应用禁用、平台、环境、工具/工具集条件及同名优先级，再按 category 渲染成 `<available_skills>` 索引。
- **会话缓存边界：** 索引在新 Agent/会话构造系统提示词时生成，位于 prompt 的 volatile tier；“volatile”表示跨重建更容易变化，不表示每轮重扫。完整系统提示词会缓存在 Agent 上，会话中途不因磁盘技能变化而重写历史前缀。扫描本身还有进程内 LRU 和由 `SKILL.md`/`DESCRIPTION.md` 文件签名验证的 `.skills_prompt_snapshot.json` 磁盘快照。
- **本次运行证据：** 普通请求会话的实际系统提示词在 `learning` 分类下包含 `meeting-summary: Use when summarizing meeting notes.`；模型因此没有调用 `skills_list`，而是直接调用 `skill_view(name="meeting-summary")`。
- **`skills_list` 仍有的作用：** 它返回当前可查询的结构化 JSON（`name + description + category`），支持 category 过滤；能反映会话启动后重载/新增的技能，而无需破坏已缓存的系统提示词；也会合并运行时注册的 plugin skills。在 coding focus 中某些索引分类可能被压缩为只有名称，`skills_list` 仍可返回完整的索引元数据。
- **结论：** 对“新会话启动时已出现在索引里的普通本地技能”，模型通常不需要再调用 `skills_list`；但系统提示词索引是会话启动快照，`skills_list` 是运行时查询接口，两者的时间边界和输出形式不同。
- **用户的理解确认：** 用户已能用自己的话说明：大多数普通情况下，模型无需调用 `skills_list` 就已从系统提示词索引获得项目和本地 profile 技能的 `name + description`；技能在会话中途变化，或 focus 模式压缩了索引信息时，才更需要 `skills_list` 查询当前结构化元数据。补充前提是 Skills 工具面已启用、技能通过可见性过滤，且索引还可包含配置的外部技能目录。

### 2026-09-18 / 进度纠正与 2.1 开始

- **用户纠正：** 此前其他会话只推进到阶段 1 结束；阶段 2 的第一步是先由用户说明 `parse_frontmatter()`。
- **错误原因：** 当前书面 `plan.md` 从 2.1 开始，`notes.md` 也只写了“即将开始 2.1”；本轮助手只依据文件恢复进度，并把“继续推进”误解成可以直接代做 2.1～2.3，没有先向用户核对未写入文件的会话检查点。
- **历史核对：** 当前可见的 Git 提交历史中，`plan.md` 没有出现过 2.0；因此 2.0 应是上一会话临时加入但未持久化的教学步骤，不能据此否定用户对实际学习进度的回忆。
- **如何处理：** 助手本轮自行运行的扫描和改名实验不计入用户学习进度；阶段 2 的检查项与结果栏均恢复为未完成。新的临时实验目录可以用于后续复现，但不代表阶段 2 已学习。随后确认所谓 2.0 更适合作为计划内 2.1 的开场自检，不另设正式编号。
- **2.1 的关键理解：** `parse_frontmatter()` 把已读出的技能文本拆成供发现逻辑使用的元数据字典和 Markdown 正文。用户已掌握主要分支；关键修正是区分“文件中用 YAML 表示”和“函数返回 Python 字典”，以及把 `{}` 从“空数组”校准为空字典。
- **学习粒度调整：** 用户指出此前对正则、切片等基础 Python 实现追问过细。后续以 Agent/Skills 机制为主；基础代码只在理解错误会影响机制判断时深入，笔记也只保留实际误解、关键概念和可复用排障证据。
- **下一步最小动作：** 阅读 `get_all_skills_dirs()`，先由用户说明它返回哪些目录以及为什么明确不包含 trusted project skills。

## 疑问清单

| 编号 | 问题 | 当前猜测 | 需要什么证据 | 状态 |
| --- | --- | --- | --- | --- |
| Q1 | Hermes 如何由模型自主判断并创建技能？ | 前台或后台模型判断；`skill_manage(create)` 落盘 | 新阶段 5 沿前台提示、回合后后台 review、创建工具与审批/落盘分别取证 | 已纳入计划，尚未学习；不计入当前 3.3 |

**范围核对与计划调整（2026-09-25）：** 用户原话：“这才是hermes的主要卖点之一，应该作为阶段6的主要内容。原来的内容‘学习技能修改与安装’也要，但是是一起学习。同时把这个阶段提前到3阶段之后或4阶段之后，具体放到哪你自己决定。”旧计划的阶段 6.2 只安排通过 `skill_manage` 修改已有技能，6.3 是 Hub 安装，没有自主判断新建的实验。现将这整个阶段移到阶段 4 之后，成为新阶段 5，并以自主创建为主线；原阶段 5 的附件与执行改为阶段 6。当前生产代码中，前台系统提示词在有 `skill_manage` 时引导模型保存非平凡工作流；回合结束的后台 review 在满足条件时由独立模型判断是否更新/创建；`skill_manage(create)` 负责实际写入。Curator 主要管理已有技能，开启模型整合时也可能创建 umbrella 技能。以上只是代码预告，尚无本计划中的真实模型创建实验；阶段 3.3 进度不变。

## 术语：用自己的话解释

| 术语 | 我的解释 | 能举出的实验例子 |
| --- | --- | --- |
| Skill | 待填写 | 待填写 |
| Frontmatter | 待填写 | 待填写 |
| 技能索引 | 待填写 | 待填写 |
| 系统提示词 | 待填写 | 待填写 |
| 工具 schema | 待填写 | 待填写 |
| 工具调用与工具结果 | 待填写 | 待填写 |
| 渐进式披露 | 待填写 | 待填写 |
| 会话缓存与磁盘文件 | 待填写 | 待填写 |
| 下载 bundle 与技能组合 | 待填写 | 待填写 |

## 最终自检

以下由我在能解释、能指出证据时勾选：

- [ ] 我能从磁盘文件追踪到技能索引。
- [ ] 我能画出显式调用和主动加载两条路径。
- [ ] 我能指出正文、schema、执行结果分别在哪里。
- [ ] 我能解释相对脚本路径应基于哪个目录。
- [ ] 我能区分修改文件和修改已有会话上下文。
- [ ] 我能根据调用与返回值判断失败发生在哪一层。

不看源码时，我对整个系统的完整解释：

> 待填写。

## 与助手协作更新记录

可使用以下请求：

> 请根据这次学习对话更新 learning/skills/notes.md。保留我的原始猜测，分别记录代码事实、实际实验结果和待验证推测。只勾选确实完成的步骤；不要替我填写“我已理解”。补上下一次最小可执行动作。

| 日期 | 更新内容 | 依据 |
| --- | --- | --- |
| 2026-09-11 | 创建学习记录框架，所有学习阶段保持未开始 | 用户请求；尚未执行学习实验 |
| 2026-09-15 | 完成阶段 0.1～0.3，记录环境证据、`PATH → 解释器 → PYTHONPATH/sys.path → 模块` 的混乱与最终理解、最小配置文件、CLI 帮助入口验证和四类路径自检；加入有长期学习价值时自动更新笔记的协作约定 | 用户实际命令输出、自检回答、附带总结文字、本次问答与源码阅读 |
| 2026-09-18 | 纠正阶段 2 进度：此前仅完成阶段 1；助手越序执行不计入学习进度。随后将口头称作 2.0 的 `parse_frontmatter()` 说明统一归入 2.1 开场自检，并记录用户的初始解释与返回类型校准 | 用户对实际会话进度的纠正；当前 Git 历史核对；本轮源码说明 |
| 2026-09-18 | 将学习分支合并到最新官方 `main` (`c62bd9f207`)，并在临时 `HERMES_HOME` 上复现 `create_dir` 技能只进入系统索引、不进入 `skills_list` / `skill_view` / 斜杠命令的不一致；确认现有 16 个 `create_dir` 测试仍未覆盖这三个入口 | 最新源码阅读；临时目录运行复现；规范测试脚本输出 |
| 2026-09-18 | 核对官方协作状态：同一问题已有 Open issue `#108157` 和 Open PR `#108160`，因此不再建议创建重复 PR，而应复核、协作或选择未被占用的后续问题 | 官方 GitHub issue、PR 与 `CONTRIBUTING.md` 的重复检查要求 |
