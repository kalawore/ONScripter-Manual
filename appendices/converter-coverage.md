# 附录 E：转换器覆盖自检（白名单 × 实现 × 测试）

> 由 `converter/coord2x.py` 的 `WHITELIST` 与 `tests/case7_whitelist` 的实测报告生成。

> 「×2 参数」为 1-based 参数序号；「复核」列的参数原样保留并写入报告。


| 命令 | ×2 参数 | 复核参数 | 实测 |
|---|---|---|---|
| `abssetcursor` | — | P3, P4 | 仅复核（按要求不改） |
| `amsp` | P2, P3 | — | 已按规则修改 |
| `amsp2` | P2, P3 | — | 已按规则修改 |
| `bar` | P3, P4, P5, P6 | — | 已按规则修改 |
| `blt` | P1, P2, P3, P4 | P5, P6, P7, P8 | 已按规则修改 |
| `btn` | P2, P3 | P4, P5, P6, P7 | 已按规则修改 |
| `cselbtn` | P3, P4 | — | 已按规则修改 |
| `drawbg2` | P1, P2 | — | 已按规则修改 |
| `drawsp` | P4, P5 | — | 已按规则修改 |
| `drawsp2` | P4, P5 | — | 已按规则修改 |
| `drawsp3` | P4, P5 | — | 已按规则修改 |
| `getscreenshot` | P1, P2 | — | 已按规则修改 |
| `logsp` | P3, P4 | — | 已按规则修改 |
| `logsp2` | P3, P4, P5, P6, P7, P8 | — | 已按规则修改 |
| `lsp` | P3, P4 | — | 已按规则修改 |
| `lsp2` | P3, P4 | — | 已按规则修改 |
| `lsp2add` | P3, P4 | — | 已按规则修改 |
| `lsp2sub` | P3, P4 | — | 已按规则修改 |
| `lsph` | P3, P4 | — | 已按规则修改 |
| `lsph2` | P3, P4 | — | 已按规则修改 |
| `lsph2add` | P3, P4 | — | 已按规则修改 |
| `lsph2sub` | P3, P4 | — | 已按规则修改 |
| `menusetwindow` | P1, P2, P3, P4 | — | 已按规则修改 |
| `movemousecursor` | P1, P2 | — | 已按规则修改 |
| `msp` | P2, P3 | — | 已按规则修改 |
| `msp2` | P2, P3 | — | 已按规则修改 |
| `prnum` | P3, P4, P5, P6 | — | 已按规则修改 |
| `quake` | P1 | — | 已按规则修改 |
| `quakex` | P1 | — | 已按规则修改 |
| `quakey` | P1 | — | 已按规则修改 |
| `rubyon` | P1, P2 | — | 已按规则修改 |
| `setcursor` | — | P3, P4 | 仅复核（按要求不改） |
| `setwindow` | 特殊（见附录 B.1.4） | — | 已按规则修改 |
| `setwindow3` | 特殊（见附录 B.1.4） | — | 已按规则修改 |
| `shadedistance` | P1, P2 | — | 已按规则修改 |
| `strsp` | P3, P4, P7, P8, P9, P10 | — | 已按规则修改 |
| `strsph` | P3, P4, P7, P8, P9, P10 | — | 已按规则修改 |
| `underline` | P1 | — | 已按规则修改 |

## 统计

- 白名单命令：38 条
- 实测产生修改：36 条
- 仅复核：2 条（`setcursor`、`abssetcursor`）
- 字符单位保护：`locate`、`indent` 不在白名单，测试中确认未修改
- 白名单外的命令：`converter/coord2x.py` 不做任何数值修改，仅在报告 [5] 中计数
