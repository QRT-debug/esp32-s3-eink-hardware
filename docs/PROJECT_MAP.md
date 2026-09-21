# Project Map

> ⚡ **当前状态见 `docs/NOW.md`**（2026-09-21：原理图 + PCB 完成、**板已打样**、硬件冻结，唯一未决 = 屏装配朝向）。
> 本文件描述**工程结构、EDA 客户端与工具链**，属"稳定理解"，不描述进度。

## Project Identity

- repository type：EasyEDA Pro 硬件工程（当前无源代码）
- primary language(s)：无源代码；工程数据为 EasyEDA Pro 自定义 JSON
- main runtime/platform：**ESP32-S3 + 2.7" 墨水屏主板 —— 设计已完成并打样**（95×95mm / 2 层 / 121 器件 / 102 网络）
- build system or IDE：EasyEDA Pro（嘉立创 EDA 专业版）3.2.175

## Startup Chain

硬件工程无软件启动链；工程文件的实际数据路径：

1. `C:\Users\Admin\Documents\LCEDA-Pro\projects\esp32-s3-eink-hardware.eprj2`（当前有效本地工程，SQLite 数据库）
2. → Codex 只读解析其 `projects`、`documents`、`schematics`、`boards` 表检查元件与网络
3. → 仓库中的 `ProPrj_esp32-s3-eink-hardware_2026-09-10.epro2` 仅是早期空白导出快照，不再作为协作入口

## EDA 客户端与数据布局

- 客户端：`D:\lceda-pro\lceda-pro.exe`（嘉立创 EDA 专业版 V3.2.175，Electron 应用）。
- 运行模式（右键菜单 → 客户端设置 → 运行模式设置，改后需重启）：
  - `ONLINE` 全在线：工程和库存服务器，无 `.eprj2`，「另存为本地」只导出 `.epro2` 压缩包。
  - `HALF_OFFLINE` 半离线：工程和库存本地（`.eprj2`），支持在线系统库（协作推荐）。
  - `OFFLINE` 全离线：工程和库存本地（`.eprj2`），不支持在线系统库。
- 客户端数据目录：`C:\Users\Admin\Documents\LCEDA-Pro\`，关键内容：
  - `config.json`：当前 `"type": "HALF_OFFLINE"`；`APP_PROJECT_DIR` 指定本地工程目录。
  - `projects\`：本地工程（`.eprj2`，SQLite 数据库）默认存放处。
  - `database\web.db`：云端工程本地缓存（SQLite），当前 projects 表为空，数据在云端/内存。
  - `online-projects-backup\`、`projects-recovery\`：云端备份与恢复目录，当前均为空。
- 用户当前使用半离线模式编辑本地 `.eprj2` 工程。
- 协作方式：用户在 EasyEDA Pro GUI 中编辑并保存；Codex 不修改 `.eprj2`，只用 SQLite 只读模式解析检查。

## Automation Toolchain

- 当前自动化方案：`easyeda-agent v1.4.8`（typed action 架构），替代已卸载的 `Run API Gateway` 裸 JS 网关。
- CLI：`C:\Users\Admin\.local\bin\easyeda.exe`，已加入当前用户 PATH 候选目录。
- Codex Skill：`C:\Users\Admin\.codex\skills\easyeda-agent`，与 CLI 同步为 `v1.4.8`。
- Daemon：`easyeda daemon start --auto-update-skill=false`，监听 `127.0.0.1:60832`。
- 日志：`C:\Users\Admin\.local\bin\easyeda-daemon.log` 与 `C:\Users\Admin\.local\bin\easyeda-daemon.err.log`。
- 同版连接器备份：`C:\Users\Admin\Downloads\easyeda-agent-connector-v1.4.8.eext`。
- **当前协作策略（2026-09-10 更新）：用户手动绘图，Codex 只做指导和只读检查，暂不使用该链路自动落图。**

## Active Communication Chains

**已全部实现并验证（2026-09-21）**，规划依据见 `docs/DESIGN.md`，实际连接以活体画布 / `docs/SCHEMATIC_CHECKLIST.md` 为准：

- ESP32-S3 ↔ e-ink：SPI2（GPIO9–14），SSD1680 兼容；两路屏口共用 SPI、**同时只接一个** —— `J6`=24P FPC 直插裸屏，`J1`=9P 排针接微雪驱动板。
- USB-C ×2（供电 + 固件烧录）：J3/J4 经 SS34 或门（D1/D2）汇 `5V_OR` → TP4056 充电（1A）/ TPS63021 升降压；CH340N 提供串口。
- 音频：U4 蓝牙模组（模拟差分）+ PCM5102A（I2S）→ U15 二选一 → NS4150C → J7 喇叭。
- I²C：MAX17048 电量计 + SHT30 温湿度（GPIO1/2）；SD 卡（J2）；EC11 编码器 + 4 按键 + 3 LED。

## Related Projects

- 固件工程：`C:\Users\Admin\Desktop\esp32_s3_eink_test`（ESP-IDF，含已实测的墨水屏驱动；引脚/SPI/控制器参数是硬件设计的权威依据）。
- 引脚资源表：`docs/PIN_MAP.md`（GPIO 分配、模组内部占用、strapping、空闲估算）。

## Core Business Objects

- SCH_PAGE `P1`：uuid `0c45ca5ef2f54c85`，属 `Schematic1`（uuid `e33852be3c71d9f4`）；**已完成**（226 器件 / 99 网络，悬空脚全为设计性 NC）。
- PCB `PCB1`：uuid `11bed811bd472bdb`；**已完成**（121 器件 / 102 网络全布通 / `pcb drc` passed-0），板已打样。
- ⚠️ **`Board1` 绑定**：2026-09-21 已把 `schematic1`（`e33852be3c71d9f4`）绑进 `Board1`（此前 schematicUuid 为 null，导致「从原理图导入变更」被挡）。
- 判读工程是否有内容：读 `.eprj2` 的 `documents` 行数，以及 `project_structures` 最新快照里对应 doc 的 `source` 是否为空串（该版本 `documents` 恒为 0，判据见下）。

## .eprj2 只读解析速查（2026-09-10 复核补充）

- 打开方式：先把 `.eprj2` 复制到临时目录，再用 `sqlite3.connect('file:副本?mode=ro', uri=True)`；直接开原文件在 EDA 未运行时也不稳。
- 关键表：`projects`（工程元信息，含 `updated_at`）、`project_structures`（版本快照，`ticket` 最大者为当前，JSON 内有 boards/schematics/sheets/pcbs 及各自 `source`/`version`）。
- **⚠️ 该客户端版本（3.2.175）的 `documents` 表始终为 0 行，即使画布上画了器件**——不能用它判断画布是否为空。正确判据是 `easyeda sch read` 的 `componentCount` / `netCount`。
- **`project_images` 表是图页缩略图缓存，删除图页/清空画布后不会同步清理**，会残留旧设计的预览图 —— **不能**拿它判断当前画布状态，只能当历史参考。
- **EDA 正在运行时，编辑内容先存在编辑器内存与 `history_data`（加密）里，`.eprj2` 的结构快照可能滞后**。要拿到最新画布，让用户 `Ctrl+S` 后用 `easyeda sch read` 读活体数据。
- `history_data` 是 base64 编码的加密编辑历史，**无密钥不可解**，不必尝试。
- 编码规则：base64 解码后无 zlib 头，非明文 JSON。

## Currently Enabled vs Present In Tree

- 活跃：SCH_PAGE `P1`（成品原理图）、PCB `PCB1`（成品 PCB，已打样）。
- `project_structures` 保留多版历史快照；**该版本的 `documents` 表恒为 0 行** ⇒ 判"画布有没有内容"只能靠 `easyeda sch read` / `pcb dump` 的件数与网络数，不能靠表行数。
- 仓库**已于 2026-09-11 初始化为 git 仓库**，远端 `https://github.com/QRT-debug/esp32-s3-eink-hardware.git`，分支 `main`。
  - 首个提交 `859ccee`（9 文件 / 1044 行，工程文档与交接文件），叠在远端自动生成的 `3a50caf Initial commit`（仅 `README.md`）之上。
  - `.workbuddy/` 已随 `.gitignore` 排除（会话数据、日志、EDA 导出工件不入库）；`.gitattributes` 统一 `eol=lf`。
  - 本机用**系统 Git**（`C:\Program Files\Git\cmd\git.exe`，2.52）+ GCM（`credential.helper=manager`），github.com 凭据已存在，**无需重新授权**。
  - **从 agent 侧跑 git 时会遇到两个坑**：① 沙箱注入 `HTTP_PROXY`/`HTTPS_PROXY`（如 `127.0.0.1:57153`）会挡住 github，**必须在干净环境里运行**（剔除全部 proxy 变量）；② **`refs/remotes/origin/*` 引用文件不落地**，会导致 `git status` 显示 `[gone]`——修法是手动写 `.git/refs/remotes/origin/main`（内容为 `git rev-parse HEAD`）。
  - **网络不稳定，直连与代理都要会试**：2026-09-11 实测中直连时而可用、时而 `Failed to connect … port 443` / `Recv failure: Connection was reset`；此时改走本机代理 `http://127.0.0.1:7897` 即可（实测可用）。**不要把 `http.proxy` 写进仓库配置**——用户的代理并非常开，写死会让他在自己终端里反而推不动。
  - **每次推送后必须核实**：`git ls-remote origin main` 与本地 `rev-parse HEAD` 比对哈希；`push` 返回 0 不代表成功（本轮就遇到 push 报成功但随后查询失败的情况，靠哈希比对才确认真正落地）。
  - **`.gitignore` 覆盖 `.workbuddy/`（会话数据）与 `.easyeda/`（easyeda-agent 导出工件）**，这两类都不入库。
  - **当前同步状态（2026-09-21 22:30 实测）**：远端 `main` = `9a19f63`；本地**领先若干笔待推送** ——
    **笔数以 `git status -sb` 的 `ahead N` 为准**（交接整理完成时为 3 笔）。
    ⚠️ **agent 侧无法推送**（`git push` 报 `could not read Username for 'https://github.com': terminal prompts disabled`，
    凭据需交互输入）⇒ **推送要在用户自己的终端执行** `git push origin main`。
  - 本机 `refs/remotes/origin/main` 引用文件**会缺失**（`git branch -vv` 显示 `[origin/main: gone]`，属假警报）。
    修法：把 `git rev-parse HEAD` 的结果写入 `.git/refs/remotes/origin/main`（2026-09-21 已补写一次）。

## Known Suspicious Areas

- `.epro2` 为 ZIP + 自定义 JSON，手工修改极易损坏；所有电路编辑必须在 EasyEDA Pro GUI 完成。
- `.eprj2`（SQLite）与 `web.db` 是 EDA 内部数据，EDA 打开期间不可改动；Codex 只做只读解析。
- 交接文件最初为空模板，本文件为首次会话回填。
