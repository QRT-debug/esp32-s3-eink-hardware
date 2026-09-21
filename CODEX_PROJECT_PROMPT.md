# Project Prompt

## Purpose

ESP32-S3 驱动电子墨水屏（e-ink）的硬件设计工程，使用嘉立创 EDA 专业版（EasyEDA Pro）。
**当前状态（2026-09-21）：原理图 + PCB 全部完成，板已打样且实物在手，硬件设计冻结。**
唯一未决是**屏（2.7" 黑白 GDEY027T91）的装配朝向**（排线横向错位 + 需翻面插）—— 见 `docs/NOW.md`。
设计目标与方案对比见 `docs/DESIGN.md`（规划文档，已被实际设计取代，仅作背景）。

## Reading Priorities

1. `docs/NOW.md`（**现状与接续卡，约 6KB —— 先读这个**：当前状态 / 已定稿结论 / 唯一阻塞 / 待办 / 省上下文纪律）
2. `docs/HANDOFF.md`（完整交接记录；**只读开头 30 行**，其余按需 Grep，不要整篇读）
3. `docs/PROJECT_MAP.md`（工程文件结构、EDA 客户端/工具链、`.eprj2` 只读解析速查）
4. 按主题再读 `docs/` 其它大文档（`EPD_SPEC.md` 95KB / `SCHEMATIC_CHECKLIST.md` 136KB / `ROUTING_REPORT.md` / `PCB_PLAN.md` / `BOM.md`）——**一律用 Grep 或 offset+limit 定点读**
5. `ProPrj_esp32-s3-eink-hardware_2026-09-10.epro2`（早期空白导出快照；**已过时，勿作依据**）

## Runtime Facts

- 无软件运行时；目标硬件为 ESP32-S3 电子墨水屏主板（**已完成设计、已打样**）。
- 设计工具：EasyEDA Pro，编辑器版本 3.2.175。画布活体数据只能经 `easyeda` CLI 读（见 `docs/PROJECT_MAP.md`）。
- 工程内容：原理图页 `P1`（uuid `0c45ca5ef2f54c85`，属文档 `Schematic1`）/ PCB `PCB1`（uuid `11bed811bd472bdb`）—— **均为成品**：PCB 121 器件 / 102 网络，`pcb drc` passed-0。
- 实物板：Gerber `Desktop/Gerber_PCB1_2026-09-19.zip` = 终态铜箔；**板框 95×95mm，2 层，ENIG**。

## Important Distinctions

- `.epro2` 是 ZIP 容器，内部 `*.epru` 为自定义成对 JSON 文本格式。
- `.eprj2` 是本地工程文件（SQLite 数据库）；用户当前打开的是本地工程（HALF_OFFLINE），与仓库里的 `.epro2` 导出快照不互通。
- 结构类改动（板子绑定、器件替换、导入变更）**必须在 EasyEDA Pro GUI 中完成**；CLI 用于读取、校验与"单点"写入（如 `pcb modify`），但 `board rebind` 这类会删旧板重建的命令**禁用**。
- `docs/DESIGN.md` 是早期规划文档，实际设计以 `docs/SCHEMATIC_CHECKLIST.md` + 画布活体为准。

## Watchouts

- 禁止手改 `.epro2` / `.epru` 内部 JSON，容易损坏工程。
- 禁止在 EDA 打开工程时改动 `.eprj2` 或 `database\web.db`。
- **★ 硬件已冻结：不要再改任何铜箔**（本版已知瑕疵见 `docs/NOW.md`，一律留给 V2）。
- **换料/换器件后必须逐盘比对 `padNumber / net / 坐标`** —— 同封装 drop-in 只在机械层成立，网络层可能被对调（本项目已踩过一次）。
- **本仓库 = 画布的二手记录**：任何结论都要先 dump 活体核对再写文档。
- 交接文件必须随实质性工作同步更新（见下方 Closeout Workflow）。

## Continuation Workflow

在开始新会话时：

1. 若本地技能 `project-handoff-resume` 可用，则使用它。
2. 读本文件。
3. 读 `docs/NOW.md`（**现状卡，先读它**）。
4. 读 `docs/PROJECT_MAP.md`。
5. 读 `docs/HANDOFF.md`（只读开头 30 行；历史细节按需 Grep，**不要整篇读**）。
6. 从最后一个未决问题继续（当前 = 屏装配朝向），不要从头重新勘察仓库。

**省上下文纪律（直接决定会话卡不卡）**：单日日志最大 130KB、`SCHEMATIC_CHECKLIST.md` 136KB、`EPD_SPEC.md` 95KB ⇒
查历史先看 `.workbuddy/memory/DAILY_INDEX.md` 定位行号再定点读；大 JSON 先落盘只回传摘要；一个对话只做一件事。

## Closeout Workflow

实质性工作结束后、结束回合前刷新仓库记忆：

1. 若结论、未决问题或下一步建议发生变化，更新 `docs/HANDOFF.md`（顶部"最新状态"块）与 `docs/NOW.md`。
2. 若稳定架构理解发生变化，更新 `docs/PROJECT_MAP.md`。
3. 更新 `.workbuddy/memory/YYYY-MM-DD.md`（当日日志，追加式）并跑 `python Temp/make_daily_index.py` 重建索引。
4. **`MEMORY.md` 只放"跨会话判断索引"且必须保持精简**（长期目标 ≤8KB）：新增内容前先把已被 docs/技能覆盖的细节移入 `MEMORY_DETAIL.md`。
5. 仅当默认阅读顺序或仓库工作流变化时，才更新本文件。
