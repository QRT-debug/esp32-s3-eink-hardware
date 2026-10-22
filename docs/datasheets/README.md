# datasheets — V2 在册器件手册归档

> 2026-10-22 建。此目录**只读**，用于保存核查依据，避免 `Temp/` 被清理后丢失一手资料。
> 文件名格式：`型号_厂家_文档类型.pdf`

## 一、V2 在册器件（按位号）

| 位号 | 型号 | 文件 | 用途 |
|---|---|---|---|
| `U1` | ESP32-S3-WROOM-1 | `ESP32-S3-WROOM-1_乐鑫_模组规格书.pdf` | 主控模组（16 MB flash + 8 MB PSRAM）|
| `U3` / `U19` | LP5907（3.3 V 固定版）| `LP5907_TI_datasheet.pdf` | 背光 LDO / 音频 LDO |
| `U4` | EWM104-BT60SP3 | `EWM104-BT60SP3_亿佰特_用户手册.pdf` | 蓝牙音频接收（A2DP + BLE + AT）|
| `U12` | USBLC6-2SC6 | `USBLC6-2SC6_ST_datasheet.pdf` | USB D± ESD 保护 |
| `U14` | PCM5102A | `PCM5102A_TI_datasheet.pdf` | I²S DAC（图 33 为外围设计依据）|
| `U15` | TS5A23157 | `TS5A23157_TI_datasheet.pdf` | 双 SPDT 模拟开关（蓝牙 / PCM 切换）|
| `U16` | NS4150C | `NS4150C_纳芯威_datasheet.pdf` | 3 W 单声道 D 类功放 |
| `U17` | MCP6002 | `MCP6002_Microchip_datasheet.pdf` | 双运放（line-out 缓冲 + 频谱取样）|
| `U18` | LMR50410 | `LMR50410_TI_datasheet.pdf` | 5 V→3.3 V buck（表 9-1 为设计依据）|
| `Q2` | AO3401A | `AO3401A_AOS_datasheet.pdf` | PMOS 软启动 / 防反接 |
| `CN1` | PJ-342E | `PJ-342E_星坤_datasheet.pdf` | 3.5 mm 线路输出座 |
| `J6` + 屏 | CL32CK305-18A | `CL32CK305-18A_SPEC_V0_鸿讯.pdf`<br>`CL32CK305-18A_接线图.pdf`<br>`ILI9341V_DS_V100.pdf` | 3.2" IPS 240×320 屏 + 驱动 IC |
| `MK1` | **GSA3526**（赢海，实际采购）| `GSA3526_赢海_麦克风_datasheet.pdf` | I²S MEMS 麦克风 |

## 二、缺失与替代说明

| 位号 | 型号 | 情况 |
|---|---|---|
| `MK1` | ICS-43434 | **手册未能下载**（TDK 官网需注册，直链返回 HTML）。**但实际采购已改为赢海 GSA3526**（见 `AI_VOICE_TERMINAL §8.5d/e`），画布保留 ICS-43434 符号仅为兼容封装，**真机焊的是 GSA3526** ⇒ 以 `GSA3526_赢海_麦克风_datasheet.pdf` 为准 |
| `J2` | TF-01A | **无官方 PDF**。厂家（HRO 韩荣 / 元迪 / 辛译）只提供网页参数：9P、DC 5V 0.5A、接触电阻 ≤100 mΩ、5000 次插拔。**电气设计依据用 SD 协会规范**（CMD/DAT 上拉 10~100 kΩ、VDD 0.1 µF + 10 µF），已在 `HARDWARE_AUDIT` 核对 |

## 三、设计核查结论索引

各器件与此目录手册的逐项对照结果见：

- `docs/HARDWARE_AUDIT.md` — 全板「手册级」核查报告（含 6 个 IC 的逐项表 + 5 条风险）
- `docs/V2_SCHEMATIC_GUIDE.md §9.7 / §9.8` — PTC 选型与 buck 输出电容调整记录
- 会话记录（2026-10-22 夜）：PCM5102A 三处修正、屏背光 Vf 分析、功放 CTRL 极性、模拟开关 Ron 影响

## 四、历史 / 已作废器件

以下手册仍留在 `Temp/`，与本版设计无关，**不要当作依据**：

`DEPG0270BBS800F7_spec.pdf` · `gdey027t91_spec.pdf` · `epd27_bv2_spec.pdf` · `ssd1680_ds.pdf` · `ws27_bw_spec.pdf`（V1 墨水屏系列）
`ds_tps63021.pdf`（V1 buck）· `ds_max17048.pdf`（V1 电量计）· `ds_tp4056.pdf`（V1 充电）· `fs8205a.pdf`（V1 电池保护）
`bt3095_spec.pdf`（旧蓝牙模块）· `xw18.pdf` / `XW05200-18_ds.pdf`（电池保护）
`C165948*.pdf`（Type-C 座，V1 用 J4 前的型号）

---

**维护约定**：更换器件时，**先把新手册放进本目录并更新上表**，再改画布。避免"改了料找不到依据"。
