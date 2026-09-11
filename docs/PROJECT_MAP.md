# Project Map

## Project Identity

- repository type：EasyEDA Pro 硬件工程（当前无源代码）
- primary language(s)：无源代码；工程数据为 EasyEDA Pro 自定义 JSON
- main runtime/platform：目标硬件 ESP32-S3 + 电子墨水屏（尚未设计）
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

尚未建立。规划中的链路（详见 `docs/DESIGN.md`）：

- ESP32-S3 ↔ e-ink：SPI2（GPIO9-14，4 MHz，Mode 0），SSD1680 兼容三色屏（详见 `docs/DESIGN.md`）
- USB-C：供电与固件烧录（ESP32-S3 原生 USB OTG）

## Related Projects

- 固件工程：`C:\Users\Admin\Desktop\esp32_s3_eink_test`（ESP-IDF，含已实测的墨水屏驱动；引脚/SPI/控制器参数是硬件设计的权威依据）。
- 引脚资源表：`docs/PIN_MAP.md`（GPIO 分配、模组内部占用、strapping、空闲估算）。

## Core Business Objects

- SCH_PAGE `P1`：uuid `0c45ca5ef2f54c85`，属 `Schematic1`（uuid `e33852be3c71d9f4`）；当前以用户手动绘制为主，按 `docs/SCHEMATIC_CHECKLIST.md` 阶段 1 推进；具体画布内容以用户实际保存状态为准。
- PCB `PCB1`：uuid `11bed811bd472bdb`；待原理图完成后再导入并布局布线。
- 判读工程是否有内容：读 `.eprj2` 的 `documents` 行数，以及 `project_structures` 最新快照里对应 doc 的 `source` 是否为空串。两者都空 = 空白画布。

## .eprj2 只读解析速查（2026-09-10 复核补充）

- 打开方式：先把 `.eprj2` 复制到临时目录，再用 `sqlite3.connect('file:副本?mode=ro', uri=True)`；直接开原文件在 EDA 未运行时也不稳。
- 关键表：`projects`（工程元信息，含 `updated_at`）、`project_structures`（版本快照，`ticket` 最大者为当前，JSON 内有 boards/schematics/sheets/pcbs 及各自 `source`/`version`）。
- **⚠️ 该客户端版本（3.2.175）的 `documents` 表始终为 0 行，即使画布上画了器件**——不能用它判断画布是否为空。正确判据是 `easyeda sch read` 的 `componentCount` / `netCount`。
- **`project_images` 表是图页缩略图缓存，删除图页/清空画布后不会同步清理**，会残留旧设计的预览图 —— **不能**拿它判断当前画布状态，只能当历史参考。
- **EDA 正在运行时，编辑内容先存在编辑器内存与 `history_data`（加密）里，`.eprj2` 的结构快照可能滞后**。要拿到最新画布，让用户 `Ctrl+S` 后用 `easyeda sch read` 读活体数据。
- `history_data` 是 base64 编码的加密编辑历史，**无密钥不可解**，不必尝试。
- 编码规则：base64 解码后无 zlib 头，非明文 JSON。

## Currently Enabled vs Present In Tree

- 活跃：SCH_PAGE `P1`、PCB `PCB1`（均仍为空白骨架；2026-09-10 22:37 复核确认画布为空）。
- 存在但无实际设计内容：BOARD、Panel、CONFIG 段（仅元数据壳）；`project_structures` 有 49 条历史版本快照，但内容均为空结构。
- 仓库**已于 2026-09-11 初始化为 git 仓库**，远端 `https://github.com/QRT-debug/esp32-s3-eink-hardware.git`，分支 `main`。
  - 首个提交 `859ccee`（9 文件 / 1044 行，工程文档与交接文件），叠在远端自动生成的 `3a50caf Initial commit`（仅 `README.md`）之上。
  - `.workbuddy/` 已随 `.gitignore` 排除（会话数据、日志、EDA 导出工件不入库）；`.gitattributes` 统一 `eol=lf`。
  - 本机用**系统 Git**（`C:\Program Files\Git\cmd\git.exe`，2.52）+ GCM（`credential.helper=manager`），github.com 凭据已存在，**无需重新授权**。
  - **从 agent 侧跑 git 时会遇到两个坑**：① 沙箱注入 `HTTP_PROXY`/`HTTPS_PROXY`（如 `127.0.0.1:57153`）会挡住 github，**必须在干净环境里运行**（剔除全部 proxy 变量；实测直连可用，无需配 `http.proxy`）；② **`refs/remotes/origin/*` 引用文件不落地**，会导致 `git status` 显示 `[gone]`——修法是手动写 `.git/refs/remotes/origin/main`（内容为 `git rev-parse HEAD`）。

## Known Suspicious Areas

- `.epro2` 为 ZIP + 自定义 JSON，手工修改极易损坏；所有电路编辑必须在 EasyEDA Pro GUI 完成。
- `.eprj2`（SQLite）与 `web.db` 是 EDA 内部数据，EDA 打开期间不可改动；Codex 只做只读解析。
- 交接文件最初为空模板，本文件为首次会话回填。
