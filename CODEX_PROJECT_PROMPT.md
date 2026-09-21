# Project Prompt

## Purpose

ESP32-S3 驱动电子墨水屏（e-ink）的硬件设计工程，使用嘉立创 EDA 专业版（EasyEDA Pro）。当前为新建空白骨架，设计目标、方案对比与推荐 BOM 思路见 `docs/DESIGN.md`。

## Reading Priorities

1. `docs/NOW.md`（**现状与接续卡，约 4KB —— 先读这个**：当前状态 / 已定稿结论 / 唯一阻塞 / 待办 / 省上下文纪律）
2. `docs/HANDOFF.md`（完整交接记录；**只读开头 30 行**，其余按需 Grep，不要整篇读）
3. `docs/PROJECT_MAP.md`（工程文件结构与 `.epro2` 解析方法）
4. 按主题再读 `docs/` 其它大文档（`EPD_SPEC.md` 95KB / `SCHEMATIC_CHECKLIST.md` 134KB / `DESIGN.md` / `BOM.md` / `ROUTING_REPORT.md`）——**一律用 Grep 或 offset+limit 定点读**
5. `ProPrj_esp32-s3-eink-hardware_2026-09-10.epro2`（EasyEDA 工程本体，只读参考；已过时，勿作依据）

## Runtime Facts

- 无软件运行时；目标硬件为 ESP32-S3 电子墨水屏主板（尚未设计）。
- 设计工具：EasyEDA Pro，编辑器版本 3.2.175。
- 当前唯一原理图页为 `P1`，唯一 PCB 为 `PCB1`，二者均为空白骨架。

## Important Distinctions

- `.epro2` 是 ZIP 容器，内部 `*.epru` 为自定义成对 JSON 文本格式。
- `.eprj2` 是本地工程文件（SQLite 数据库）；用户当前打开的是云端工程，与仓库里的 `.epro2` 导出快照不互通。
- 电路与 PCB 编辑必须在 EasyEDA Pro GUI 中完成；脚本仅做只读解析与校验。
- `docs/DESIGN.md` 是规划文档，尚未落实到原理图。

## Watchouts

- 禁止手改 `.epro2` / `.epru` 内部 JSON，容易损坏工程。
- 禁止在 EDA 打开工程时改动 `.eprj2` 或 `database\web.db`。
- 屏幕型号/驱动方案是第一阻塞决策，未确定前不要开始绘制原理图。
- 交接文件必须随实质性工作同步更新。

## Continuation Workflow

在开始新会话时：

1. 若本地技能 `project-handoff-resume` 可用，则使用它。
2. 读本文件。
3. 读 `docs/NOW.md`（**现状卡，先读它**）。
4. 读 `docs/PROJECT_MAP.md`。
5. 读 `docs/HANDOFF.md`（只读开头 30 行；历史细节按需 Grep，**不要整篇读**）。
6. 从最后一个未决问题继续，不要从头重新勘察仓库。

**省上下文纪律（直接决定会话卡不卡）**：单日日志最大 130KB、`SCHEMATIC_CHECKLIST.md` 134KB、`EPD_SPEC.md` 95KB ⇒
查历史先看 `.workbuddy/memory/DAILY_INDEX.md` 定位行号再定点读；大 JSON 先落盘只回传摘要；一个对话只做一件事。

## Closeout Workflow

实质性工作结束后、结束回合前刷新仓库记忆：

1. 若结论、未决问题或下一步建议发生变化，更新 `docs/HANDOFF.md`。
2. 若稳定架构理解发生变化，更新 `docs/PROJECT_MAP.md`。
3. 仅当默认阅读顺序或仓库工作流变化时，才更新本文件。
