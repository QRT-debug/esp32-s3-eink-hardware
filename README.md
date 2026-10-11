# esp32-s3-eink-hardware

ESP32-S3 桌面设备主板（V1 已打样、实物在手；**当前正在改 V2**）。设计工具：嘉立创 EDA 专业版。

---

## ★ 先分清两样东西（最容易混的地方）

| | 在哪 | 是什么 |
|---|---|---|
| **EDA 工程**（原理图 / PCB）| **不在本仓库**：<br>`C:\Users\Admin\Documents\LCEDA-Pro\projects\esp32-s3-eink-hardware.eprj2` | **唯一的工作副本**。改图就在这儿改 |
| **本仓库** | `C:\Users\Admin\Desktop\esp32-s3-eink-hardware\` | **设计文档 + 存档**。**不存工程本体** |

> ⚠️ `.eprj2` 必须待在 EDA 指定的目录里 —— **挪走 EDA 就找不到工程**。
> 所以"把工程文件也放进仓库"做不到，仓库只能管"文档"和"存档"。

---

## 目录

```
esp32-s3-eink-hardware/
├── docs/                    ← ★ 交付面：全部设计文档
├── snapshots/               ← 设计存档（V1 的只读副本）
├── .workbuddy/              ← 会话工作区：记忆 + 分析脚本（gitignored）
├── Temp/                    ← 工具产物：脚本/网表快照/渲染图（gitignored）
├── snapshots_V2/            ← 空壳（历史遗留，无影响）
├── .gitattributes           ← 换行策略 + 二进制标记
├── .gitignore
├── README.md                ← 本文件
├── CODEX_PROJECT_PROMPT.md  ← 给 AI 会话的项目提示（⚠️ 状态描述停在 2026-09-21）
└── nul.txt                  ← 0 字节垃圾文件（某次命令的产物）
```

## docs/ —— 交付面

| 类别 | 文件 |
|---|---|
| **当前主线** | `NOW.md`（现状卡，**先读它**）· **`V2_PLAN.md`**（V2 规划，当前主要工作）· **`V2_DELETE_LIST.md`**（V2 删除清单，对着改原理图用）|
| **硬件设计** | `BOM.md` · `PURCHASE_LIST.md` + `purchase_list.csv` · `CAPACITOR_PURCHASE.md` · `PIN_MAP.md` · `DESIGN.md`（早期规划，已被取代，仅作背景）|
| **审计 / 校核** | `SCHEMATIC_CHECKLIST.md`（原理图审计记录）· `REFERENCE_DESIGN_DIFF.md` |
| **PCB / 制造** | `PCB_PLAN.md` · `ROUTING_REPORT.md` · `BRINGUP.md`（上电调试）|
| **专项** | `EPD_SPEC.md`（墨水屏规格与实测）· `AUDIO_COMPARE.md` |
| **流程** | `PROJECT_MAP.md`（工程结构 / 工具链 / `.eprj2` 解析）· `HANDOFF.md`（交接记录）|
| **其它** | `silk_art/`（丝印图形）· `pour_island_experiment.png` |

## snapshots/ —— 存档

| 文件 | 内容 |
|---|---|
| `v1-dev-2026-09-13.eprj2` | V1 开发期快照 |
| `v1-final-2026-10-11.epro2` | **V1 冻结终态**（也是 V2 的起点）|

**还原方式两种格式不同**（`.eprj2` 覆盖文件 / `.epro2` 导入工程）—— 详见 `snapshots/README.md`。

## 临时区（都不入库）

- `.workbuddy/` —— 项目记忆（`.workbuddy/memory/`）+ 历次分析脚本
- `Temp/` —— 约 2570 个文件：脚本、网表快照、渲染图、数据手册

> ⚠️ **本机删除操作会进回收站**（环境的 `safe-delete` 策略），所以清理这些目录时要注意回收站会涨。

## 从哪读起

1. **`docs/NOW.md`** —— 现状卡（约 6 KB，先读这个）
2. **`docs/V2_PLAN.md`** —— V2 规划
3. **`docs/V2_DELETE_LIST.md`** —— V2 要对原理图删哪些件
4. `docs/PROJECT_MAP.md` —— 需要动 EDA / 工具链时
5. 其余按主题用 **Grep 定点读**，别整篇读（几个大文档 100 KB+）

> ⚠️ `CODEX_PROJECT_PROMPT.md` 里的"当前状态"停在 2026-09-21（那时硬件刚冻结）；
> **V2 相关一律以 `docs/V2_PLAN.md` 为准。**
