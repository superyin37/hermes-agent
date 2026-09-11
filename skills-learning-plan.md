# Hermes Skills 系统学习计划

配套记录：[skills-learning.md](skills-learning.md)。

编写日期：2026-09-11。代码核对基线：`26350357d7`。本计划以本地 Python CLI 为主线；函数位置可能随仓库更新移动，优先按函数名搜索。文中的实验供后续学习执行，创建本文不代表实验已经完成。

## 目标与安排

学完后，应能沿着实际代码解释：一份 `SKILL.md` 如何被发现、被选择、进入模型上下文，再影响工具调用；也能判断问题发生在发现、加载、推理、执行还是保存阶段。

| 阶段 | 时间参考 | 核心问题 | 留下的证据 |
| --- | --- | --- | --- |
| 0. 准备 | 20～40 分钟，依赖安装另计 | 我运行的到底是哪份代码、哪个数据目录？ | 环境与路径记录 |
| 1. 最小技能 | 30～45 分钟 | 指令怎样影响一次回答？ | 技能 v1/v2、输入与输出 |
| 2. 发现与解析 | 40～60 分钟 | 文件怎样成为命令条目？ | 元数据与命令条目 |
| 3. 消息构造 | 45～60 分钟 | `/技能名` 如何变成模型可读的消息？ | 展开后的消息、调用关系 |
| 4. 主动加载 | 45～60 分钟 | 显式调用与模型主动读取有什么区别？ | 索引、正文、工具调用对照 |
| 5. 附件与执行 | 45～60 分钟 | 读说明和运行脚本在哪里分开？ | 文件读取、执行成功与失败记录 |
| 6. 修改与安装 | 45～60 分钟 | 技能怎样持久化并供下次使用？ | 文件差异、安装流程图 |

不要求一天完成。每次结束前用 5 分钟填写记录：原先的猜测、看到的证据、修正后的理解、仍未解决的问题。先完成阶段 1～3，再继续后半程。

## 实验约定

- 所有阶段围绕 `meeting-summary`，不同时研究多个复杂技能。
- shell 命令在终端执行，`/meeting-summary` 等斜杠命令在 Hermes 聊天输入框执行，Python 代码块按照标注方式执行。
- “预期现象”是待验证的预测，不能直接抄成实验结果。模型声称读过文件，不等于已经有工具调用证据。
- 直接调用解析、扫描、消息构造函数，不需要模型推理；真实聊天需要可用的模型配置。这些函数仍可能读取配置、写入缓存或使用记录，因此也使用实验数据目录。
- 修改技能后用新 Python 进程或退出并重新启动 Hermes，避免把历史上下文、目录缓存和文件修改混为一谈。主线不做会话中强制刷新系统提示词的实验。
- 这里的“加载主要是交付说明”适用于普通技能。代码还支持模板替换及可配置的 inline shell 预处理，后者留到进阶阅读；实验明确关闭它。

## 阶段 0：准备一个可复现的实验环境

### 0.1 确认代码与解释器

在仓库根目录执行：

```bash
cd /home/yin-hanyang/projects/hermes
git rev-parse --short HEAD
git status --short
python3 --version
```

如果已有用于这个仓库的 Python 环境，先激活它，再检查：

```bash
python -c 'import sys; print(sys.executable)'
python -c 'import yaml, dotenv, rich; print("基础依赖可导入")'
```

编写本文时，当前 shell 未发现仓库内 `.venv/` 或 `venv/`；系统 Python 有 `yaml`，但缺少 `dotenv` 和 `pytest`。这不代表机器上没有其他可用环境，也不代表后续实验已能直接运行。

如果缺少开发环境，先按 [README 的开发环境说明](README.md)准备。已有 `uv` 时，可以采用仓库外的专用环境：

```bash
uv venv ~/.hermes/venvs/hermes-skills-learning --python 3.11
source ~/.hermes/venvs/hermes-skills-learning/bin/activate
uv pip install -e ".[all,dev]"
```

这一步会安装依赖。若同名环境已经存在，直接激活，不必重新创建。没有 `uv` 时先按 README 准备安装工具；不要在依赖未就绪时把 `ModuleNotFoundError` 当作技能实现问题。

### 0.2 创建实验数据目录

在同一个终端、仓库根目录中执行一次：

```bash
export SKILLS_REPO="$PWD"
export SKILLS_LAB="$(mktemp -d /tmp/hermes-skills-lab.XXXXXX)"
export HERMES_HOME="$SKILLS_LAB/home"
mkdir -p "$HERMES_HOME/skills/learning/meeting-summary" "$SKILLS_LAB/work" "$SKILLS_LAB/evidence"
python - <<'PY'
import os
from pathlib import Path
import yaml

lab = Path(os.environ["SKILLS_LAB"])
home = Path(os.environ["HERMES_HOME"])
config = {
    "skills": {"external_dirs": [], "inline_shell": False},
    "terminal": {"backend": "local", "cwd": str(lab / "work")},
    "display": {"interface": "cli"},
}
(home / "config.yaml").write_text(yaml.safe_dump(config), encoding="utf-8")
print("实验目录:", lab)
print("数据目录:", home)
PY
```

这是直接使用 Hermes 已有的 `HERMES_HOME` 隔离机制，不注册命名 profile，也不执行 `profile use`。它将主要配置、技能、缓存和会话状态指向实验目录；它不是操作系统沙箱，也不会自动清除 shell 中已有的环境变量。

把打印出的目录记入学习记录。`/tmp` 可能被系统清理，阶段结束后将重要文字证据复制到记录文档。以后开新终端时，重新激活解释器、恢复这三个变量并 `cd "$SKILLS_REPO"`；不要再次运行 `mktemp` 后误以为还是原来的实验。

### 0.3 定义启动入口

在同一个终端定义：

```bash
skills_lab_cli() (
  cd "$SKILLS_LAB/work" || exit 1
  PYTHONPATH="$SKILLS_REPO${PYTHONPATH:+:$PYTHONPATH}" python -m hermes_cli.main "$@"
)
skills_lab_cli chat --help
```

这个入口从实验工作目录启动，并通过 `PYTHONPATH` 使用当前仓库的源码。它避免仅凭 PATH 中的 `hermes` 命令猜测正在运行哪个安装副本。新终端需要重新定义函数。

若启动仍进入 TUI，检查 shell 是否设置了 `HERMES_TUI`，本次终端中可执行 `unset HERMES_TUI`，然后重试。

真实聊天前运行 `skills_lab_cli setup`，为实验环境配置一个可用模型。只需完成模型所需设置；暂时不用配置消息平台和外部服务。密钥不要写进学习记录。如果暂时不配置模型，可以先完成阶段 1 的文件创建，以及阶段 2～4 的 Python 观察实验，再回头补聊天。

**完成标准：** 能说出源码目录、Python 解释器、`HERMES_HOME` 和聊天工作目录四者的区别。

## 阶段 1：创建并调用最小技能

### 1.1 创建文件

用编辑器在 `$HERMES_HOME/skills/learning/meeting-summary/SKILL.md` 写入下列内容。这里是教学用最小示例，不是待贡献到内置技能目录的完整模板。

```markdown
---
name: meeting-summary
description: "Use when summarizing meeting notes."
---
# Meeting Summary Skill

将用户提供的会议记录整理为中文摘要。

## Procedure
1. 使用三个标题：已确定的决定、待办事项、待确认的问题。
2. 待办事项写明负责人和截止时间。
3. 原文未提供的信息标记为“未说明”，不要自行补全。

## Verification
检查每项决定和待办是否能在原文中找到依据。
```

文件路径中的变量需要替换成阶段 0 的真实目录；不要在 IDE 中创建名字包含字面量 `$HERMES_HOME` 的目录。

### 1.2 做一次无技能基线与一次显式调用

运行 `skills_lab_cli chat`，先只发送普通任务：

```text
请整理这段会议记录：今天决定先修复登录错误，再发布新版。小王负责测试，周五前完成。小李负责更新文档，截止日期未定。上线日期下周再讨论。
```

保存结果并退出。在新启动的聊天中发送：

```text
/meeting-summary 今天决定先修复登录错误，再发布新版。小王负责测试，周五前完成。小李负责更新文档，截止日期未定。上线日期下周再讨论。
```

基线不保证“没有使用技能”：模型可能主动发现并读取它。记录实际工具调用，这个观察本身会在阶段 4 得到解释。

### 1.3 修改一条指令并重新调用

将第一条指令改为“使用三个标题：决定清单、行动清单、开放问题”，保存为 v2。退出并重新启动聊天，发送同样的斜杠命令和会议记录。

比较标题是否变化、缺失日期是否仍被保留为未知。不要把措辞或排版完全一致作为通过条件。

**记录：** v1/v2 的差异、同一段用户输入、两次显式调用输出、实际出现的工具调用。

**自检：** 哪些是技能规定的处理规则？哪些是用户给出的事实？模型若自行编造上线日期，是文件没找到，还是另一类问题？此时可以先保留猜测。

## 阶段 2：从文件追踪到命令条目

### 2.1 找到入口

在仓库根目录执行：

```bash
rg -n '^def (parse_frontmatter|get_all_skills_dirs|get_scan_ordered_skills_dirs)' agent/skill_utils.py
rg -n '^def (scan_skill_commands|get_skill_commands)' agent/skill_commands.py
```

依次阅读 [skill_utils.py](agent/skill_utils.py) 的解析和目录函数，再读 [skill_commands.py](agent/skill_commands.py) 的 `scan_skill_commands()`。对每个函数只先记录输入、返回值、直接调用者和过滤条件。

### 2.2 打印真实数据

保留阶段 0 的环境变量，在仓库根目录执行：

```bash
python - <<'PY'
import json
import os
from pathlib import Path
from agent.skill_utils import parse_frontmatter, get_all_skills_dirs
from agent.skill_commands import scan_skill_commands

path = Path(os.environ["HERMES_HOME"]) / "skills/learning/meeting-summary/SKILL.md"
metadata, body = parse_frontmatter(path.read_text(encoding="utf-8"))
print("扫描目录:", [str(p) for p in get_all_skills_dirs()])
print("元数据:", json.dumps(metadata, ensure_ascii=False, indent=2))
print("正文开头:", body[:200])
entry = scan_skill_commands().get("/meeting-summary")
print("命令条目:", json.dumps(entry, ensure_ascii=False, indent=2, default=str))
PY
```

注意：`get_all_skills_dirs()` 的返回值不能单独代表所有扫描入口的最终顺序。继续看 `scan_skill_commands()` 中项目目录、本地目录、外部目录的组合代码。

### 2.3 制造一个可解释的发现失败

暂时将实验文件改名为 `SKILL.md.disabled`，用新 Python 进程再运行扫描部分，观察命令条目。随后恢复文件名并再次扫描。

这一步只移动实验文件。记录恢复后的条目，不要让实验停在失败状态。

**预期：** 元数据和正文被分开；文件被发现后形成含名称、说明和路径的条目；不再叫 `SKILL.md` 时，该实验技能无法通过这条扫描路径成为命令。

**完成标准：** 能画出“文件路径 → frontmatter → 命令字典”，并解释技能目录、技能名称和斜杠命令键之间的关系。

## 阶段 3：观察真正交给对话的消息

### 3.1 不调用模型，直接展开命令

在仓库根目录执行：

```bash
python - <<'PY'
import os
from pathlib import Path
from agent.skill_commands import build_skill_invocation_message

message = build_skill_invocation_message(
    "/meeting-summary",
    "小王周五前完成测试；上线日期下周再讨论。",
)
if message is None:
    raise SystemExit("技能没有成功加载，请回到阶段 2 检查命令条目")
print(message)
target = Path(os.environ["SKILLS_LAB"]) / "evidence/invocation-message.txt"
target.write_text(message, encoding="utf-8")
print("已保存到:", target)
PY
```

标出：激活说明、技能正文、技能绝对目录、用户任务，以及本次实际出现的其他提示。没有配置项或附件时，不必期待相关块一定存在。

### 3.2 沿调用关系阅读

在 [skill_commands.py](agent/skill_commands.py) 中依次阅读：

```text
build_skill_invocation_message()
  → get_skill_commands()
  → _load_skill_payload()
      → tools.skills_tool.skill_view(..., preprocess=False)
  → _build_skill_message()
```

观察 `_load_skill_payload()` 为什么需要解析 JSON，以及 `_build_skill_message()` 如何加入目录和用户指令。暂时跳过使用统计、模板展开的内部实现，但注明这些分支存在。

### 3.3 找到 CLI 的调用者

```bash
rg -n 'build_skill_invocation_message|_pending_input.put\(msg\)' cli.py
rg -n '_pending_input.get|run_conversation\(' cli.py
```

在 [cli.py](cli.py) 找到实际调用而不仅是导入包装器：查看返回的 `msg` 如何放进输入队列，再追踪队列读取、对话调用和用户消息追加位置。只沿这一条路径走，不通读整个 CLI。

如果需要调试，在 IDE 给 `build_skill_invocation_message()` 的返回前、CLI 放入队列处和对话入口各设一个断点。使用阶段 0 的同一 Python 环境，启动模块 `hermes_cli.main`，参数为 `chat`；工作目录设为实验 `work`，环境变量设置 `HERMES_HOME` 和指向仓库的 `PYTHONPATH`。

**完成标准：** 能指出消息在哪一层还是字符串、在哪一层被赋予对话角色；能说明追加本轮消息为何不同于重写旧系统提示词。不要只依据函数名字猜测消息角色。

## 阶段 4：比较显式调用与模型主动加载

### 4.1 比较索引和正文

先在仓库根目录运行：

```bash
python - <<'PY'
import json
from agent.prompt_builder import build_skills_system_prompt
from tools.skills_tool import skill_view

index = build_skills_system_prompt()
payload = json.loads(skill_view("meeting-summary", preprocess=False))
print("=== 技能索引 ===")
print(index)
print("=== skill_view 返回的字段 ===")
print(sorted(payload.keys()))
print("=== 正文 ===")
print(payload.get("content", payload))
PY
```

这是索引构造函数的直接输出，不等同于某次真实 API 请求的完整系统提示词。查阅 [prompt_builder.py](agent/prompt_builder.py) 的调用处，才能确认它怎样被装配进会话。

然后查看 [skills_tool.py](tools/skills_tool.py) 中 `skills_list()`、`skill_view()` 的定义及底部注册代码。确认 `file_path` 的真实参数名；文档中的简写 `path` 不一定是可以直接发送的工具参数。

### 4.2 做两次真实对话

分别启动两个新会话，保持模型与工具集相同：

```bash
skills_lab_cli chat --toolsets skills,terminal --verbose
```

第一次发送显式 `/meeting-summary ...`。第二次发送：“请先读取我安装的 meeting-summary 技能，再按它整理这段会议记录：……”。

记录第二次是否出现 `skill_view`、参数是什么、是否先调用 `skills_list`。列表调用不是必经步骤：模型可能已从系统索引得知技能名称。日志不显示完整参数时，使用断点或现有会话轨迹，不凭最终回答猜测。

再尝试一次不提技能名的普通请求，记录模型是否自行选择该技能；这属于模型行为观察，不要求每次触发。

### 4.3 整理两条路径

| 比较项 | 显式斜杠调用 | 模型主动加载 |
| --- | --- | --- |
| 谁启动加载？ | CLI 的命令处理路径 | 模型发出的工具调用 |
| 核心加载入口 | `_load_skill_payload()` 调用 `skill_view()` | 工具分发调用 `skill_view()` |
| 正文如何进入对话？ | 展开后作为本轮用户消息的一部分 | 作为工具结果进入对话 |
| 是否一定调用 `skills_list`？ | 不需要模型调用它 | 也不一定，需要实际观察 |

把表中的代码事实与自己的运行证据对应起来。需要跟进工具分发时，只搜索 [model_tools.py](model_tools.py) 的 `handle_function_call()` 和 [run_agent.py](run_agent.py) 中相关调用及工具结果追加位置。

**完成标准：** 能解释“知道有这个技能”和“已读完整正文”的区别，以及两条路径如何复用底层读取逻辑。

## 阶段 5：增加参考文件与真正执行的脚本

### 5.1 创建参考文件

在实验技能目录下新增 `references/output-format.md`：

```markdown
待办事项采用以下表格：
| 事项 | 负责人 | 截止时间 |
| --- | --- | --- |
缺失信息统一填写“未说明”。
```

在 `SKILL.md` 正文追加：“输出前用 `skill_view` 读取 `references/output-format.md`，遵循其中格式。”

先直接观察附件读取：

```bash
python - <<'PY'
import json
from tools.skills_tool import skill_view
result = json.loads(skill_view("meeting-summary", file_path="references/output-format.md"))
print(json.dumps(result, ensure_ascii=False, indent=2))
PY
```

再新开聊天调用技能，检查是否真的读取了附件。区分“主消息列出附件路径”和“附件正文已经加载”。

### 5.2 添加小脚本

在实验技能目录新增 `scripts/count_lines.py`，内容如下：

```python
import sys
from pathlib import Path

path = Path(sys.argv[1])
lines = path.read_text(encoding="utf-8").splitlines()
print(f"line_count={len(lines)}")
```

在 `$SKILLS_LAB/work/meeting.txt` 写入三行会议记录。先由你在终端运行脚本，建立已知正确的执行结果：

```bash
python "$HERMES_HOME/skills/learning/meeting-summary/scripts/count_lines.py" "$SKILLS_LAB/work/meeting.txt"
```

在技能正文增加规则：“当用户给出会议文件时，用 `terminal` 运行技能目录下的 `scripts/count_lines.py`，参数为会议文件的绝对路径；读取会议文件，整理内容，并报告脚本实际输出的行数。脚本路径按技能目录解析。”

新开聊天，输入 `/meeting-summary 请处理文件 <meeting.txt 的真实绝对路径>`。观察模型是否调用终端、路径是否正确、脚本输出是否为 `line_count=3`，以及最终摘要是否基于文件内容。

### 5.3 做一次受控失败

将 `count_lines.py` 临时改名为 `count_lines.py.disabled`，重新调用，要求“若脚本缺失，只报告错误，不创建或修复脚本”。记录终端返回值或错误文本，再恢复文件名重试。

如果模型先检查文件并发现缺失，没有执行 Python，也如实记录；不要编造一次并未发生的失败调用。

**完成标准：** 能区分技能加载成功与脚本执行成功，并解释谁读取说明、谁选择命令、谁执行程序、谁解释结果。

## 阶段 6：理解修改与安装生命周期

### 6.1 先观察公开工具接口

在 [skill_manager_tool.py](tools/skill_manager_tool.py) 找到 `SKILL_MANAGE_SCHEMA`、底部 `registry.register()`、`skill_manage()` 和 `_skill_manage_batch()`。

先读 schema 再读 Python 函数：当前模型可见接口要求 `operations` 数组；Python 层仍保留旧的平铺参数用于兼容。这两者不能混为一谈。

### 6.2 通过模型修改实验技能

先把实验 `SKILL.md` 复制到 `$SKILLS_LAB/evidence/skill-before-manage.md`。在启用 `skills` 工具集的新会话中要求：

```text
请先读取 meeting-summary 技能，然后用 skill_manage 将“未说明”统一替换为“待补充”。只修改这个实验技能，不改变其他规则。请说明实际修改结果。
```

观察实际参数是否类似下面的模型工具参数示例：

```json
{
  "operations": [
    {
      "name": "meeting-summary",
      "action": "patch",
      "old_string": "未说明",
      "new_string": "待补充",
      "replace_all": true
    }
  ]
}
```

上例只修改主文件。阶段 5 的参考文件也含有“未说明”，因此检查模型是否另加了一项针对 `references/output-format.md` 的 patch；如果没有，记录这项遗漏，再明确要求修改参考文件。这能验证“主文件修改”和“整个技能所有文件修改”不是同一件事。

比较文件差异：

```bash
diff -u "$SKILLS_LAB/evidence/skill-before-manage.md" "$HERMES_HOME/skills/learning/meeting-summary/SKILL.md"
```

`diff` 在存在差异时退出码为 1，这是正常结果。若工具返回待审批状态，按当前配置完成审批后再检查磁盘；不要把“已提交修改请求”当作“文件已经写入”。随后新开会话重新调用技能，检查修改是否被加载。

### 6.3 阅读一条 Hub 安装路径

主线只读代码，不要求现在安装外部技能：

1. 在 [hermes_cli/skills_hub.py](hermes_cli/skills_hub.py) 找 `do_install()`，查看它如何取得来源、下载结果和扫描结果。
2. 在 [tools/skills_hub.py](tools/skills_hub.py) 找 `UrlSource`，阅读 `SKILL.md` 与引用附属文件如何组成 bundle。
3. 找 `quarantine_bundle()`、`install_from_quarantine()`、`HubLockFile`，记录哪些步骤落盘、锁文件记录什么，以及失败如何阻止后续安装。
4. 回到阶段 2，回答安装后的文件为什么能被扫描。下载代码无需自行“把技能注册进模型”。

画出“来源 → 下载内容 → 隔离暂存 → 扫描与策略判断 → 安装目录及锁记录 → 后续发现”的关系图，并按真实控制流修正顺序。区分这个下载 bundle 与多个技能组合成斜杠命令的 skill bundle。

### 6.4 用已有测试理解行为约束

从 [test_skill_commands.py](tests/agent/test_skill_commands.py) 选读 `test_uses_shared_skill_loader_for_secure_setup`、`test_supporting_file_hint_uses_file_path_argument`。分别写出它们的准备条件、触发动作、断言与 mock 的边界。

如需运行，必须使用仓库测试入口：

```bash
scripts/run_tests.sh tests/agent/test_skill_commands.py -k 'uses_shared_skill_loader_for_secure_setup or supporting_file_hint_uses_file_path_argument'
```

如果测试脚本未找到你的外部虚拟环境，先阅读 [scripts/run_tests.sh](scripts/run_tests.sh) 的解释器选择逻辑，再调整环境；不要改用裸 `pytest` 绕过仓库的隔离规则。测试通过是该行为的证据，不代表完成了真实模型对话验证。

**完成标准：** 能解释手动编辑、`skill_manage`、Hub 安装最终如何汇合到文件发现路径，并区分模型公开接口、内部兼容函数和用户 CLI 命令。

## 遇到问题时的排查顺序

| 现象 | 先检查 | 暂时不要下的结论 |
| --- | --- | --- |
| Python 导入失败 | 解释器、依赖、运行目录 | 技能功能坏了 |
| 命令条目为 `None` | `HERMES_HOME`、文件名、过滤条件、新进程 | 模型不愿意用技能 |
| 消息构造返回 `None` | 加载返回值、路径解析、错误分支 | 正文一定已经进了上下文 |
| 新技能未被自动选择 | 新会话索引、描述、工具是否可用、模型调用记录 | 安装肯定失败 |
| 输出没按格式 | 实际加载正文、附件、用户指令、模型行为 | 文件一定没有被读取 |
| 脚本找不到 | 技能目录与工作目录、实际命令参数 | skill_view 读取失败 |
| 修改后行为没变化 | 磁盘文件、参考文件、新会话实际消息 | 修改工具没有写入 |

## 学完后的自检与进阶

不看代码，用自己的话回答：

1. 目录中的文件经过哪些步骤，才会出现在模型可见的索引里？
2. 显式调用与模型主动加载，哪些步骤相同，哪些不同？
3. 技能正文、工具 schema、工具执行结果，分别是什么？
4. 参考文件为什么不必在发现时全部加载？脚本又是谁执行的？
5. 修改磁盘文件为何不等于修改当前会话的历史上下文？
6. 如何证明一次失败发生在哪一层，而不是只凭模型最终回答猜测？

之后按兴趣补读条件激活、外部目录与同名优先级、技能组合、密钥配置、模板与 inline shell、缓存和 Curator。每次仍选择一个问题、一条实际路径和一个小实验。

与助手继续学习时，可直接说：“我们开始阶段 2，只带我做 2.1 和 2.2；先让我预测结果，再解释代码。”完成后说：“请根据本次对话更新 skills-learning.md，保留我的原始猜测，把代码结论与实际运行结果分开，未做的实验保持未完成。”
