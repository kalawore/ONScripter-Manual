# 附录 D：未定位 / 不可达 / 别名 命令清单

## D.1 未定位

未定位处理函数的命令：**0 条**。

无。348/348 全部定位到 `ONScripter::` 或 `ScriptParser::` 的定义。

## D.2 不可达注册

| 命令 | 处理函数 | LUT 行 | 原因 |
|---|---|---|---|
| `nsa` | `arcCommand` | 151 | 同字母组内 `nsaCommand`（LUT 行 148）先命中，线性查找先到先得（`ONScripter.cpp:1152-1158`） |

## D.3 同一处理函数承载多个命令名（别名组）

共 **48** 组、覆盖 **126** 条命令条目。

| 处理函数 | 命令名 | 数量 |
|---|---|---|
| `amspCommand` | `amsp2`, `amsp` | 2 |
| `arcCommand` | `nsa`, `arc` | 2 |
| `bgcopyCommand` | `bgcpy`, `bgcopy` | 2 |
| `btndefCommand` | `btndef`, `bclear` | 2 |
| `btntimeCommand` | `btntime2`, `btntime`, `btime` | 3 |
| `btnwaitCommand` | `textbtnwait`, `selectbtnwait`, `btnwait2`, `btnwait`, `bexec` | 5 |
| `clickCommand` | `lrclick`, `click` | 2 |
| `cspCommand` | `csp2`, `csp` | 2 |
| `dwaveCommand` | `dwaveplayloop`, `dwaveplay`, `dwaveloop`, `dwaveload`, `dwave` | 5 |
| `effectCommand` | `windoweffect`, `effect` | 2 |
| `exbtnCommand` | `exbtn_d`, `exbtn`, `cellcheckexbtn`, `bdef` | 4 |
| `getcursorCommand` | `getcursor`, `bcursor` | 2 |
| `getlogCommand` | `getlogtext`, `getlog` | 2 |
| `getmp3volCommand` | `getmp3vol`, `getbgmvol` | 2 |
| `getparamCommand` | `getparam2`, `getparam` | 2 |
| `gettimerCommand` | `gettimer`, `getbtntimer` | 2 |
| `ifCommand` | `notif`, `if` | 2 |
| `itoaCommand` | `itoa2`, `itoa` | 2 |
| `linepageCommand` | `linepage2`, `linepage` | 2 |
| `logspCommand` | `logsp2`, `logsp` | 2 |
| `lsp2Command` | `lsph2sub`, `lsph2add`, `lsph2`, `lsp2sub`, `lsp2add`, `lsp2` | 6 |
| `lspCommand` | `lsph`, `lsp` | 2 |
| `mode_extCommand` | `mode_ext`, `automode` | 2 |
| `movCommand` | `movl`, `mov9`, `mov8`, `mov7`, `mov6`, `mov5`, `mov4`, `mov3`, `mov10`, `mov` | 10 |
| `mp3Command` | `mp3save`, `mp3loop`, `mp3`, `bgmonce`, `bgm` | 5 |
| `mp3fadeinCommand` | `mp3fadein`, `bgmfadein` | 2 |
| `mp3fadeoutCommand` | `mp3fadeout`, `bgmfadeout` | 2 |
| `mp3stopCommand` | `mp3stop`, `bgmstop` | 2 |
| `mp3volCommand` | `mp3vol`, `bgmvol` | 2 |
| `mspCommand` | `msp2`, `msp` | 2 |
| `nsaCommand` | `sar`, `nsa`, `ns3`, `ns2` | 4 |
| `ofscopyCommand` | `ofscpy`, `ofscopy` | 2 |
| `playCommand` | `playonce`, `play` | 2 |
| `quakeCommand` | `quakey`, `quakex`, `quake` | 3 |
| `rndCommand` | `rnd2`, `rnd` | 2 |
| `savegameCommand` | `savegame2`, `savegame` | 2 |
| `savescreenshotCommand` | `savescreenshot2`, `savescreenshot` | 2 |
| `selectCommand` | `selnum`, `selgosub`, `select`, `csel` | 4 |
| `setcursorCommand` | `setcursor`, `abssetcursor` | 2 |
| `soundpressplginCommand` | `spi`, `soundpressplgin` | 2 |
| `spbtnCommand` | `spbtn`, `existspbtn`, `cellcheckspbtn` | 3 |
| `splitCommand` | `splitstring`, `split` | 2 |
| `strspCommand` | `strsph`, `strsp` | 2 |
| `transbtnCommand` | `transbtn`, `btrans` | 2 |
| `trapCommand` | `trap`, `lr_trap` | 2 |
| `vspCommand` | `vsp2`, `vsp` | 2 |
| `waveCommand` | `waveloop`, `wave` | 2 |
| `yesnoboxCommand` | `yesnobox`, `okcancelbox` | 2 |

## D.4 重复命令名

- `nsa`：2 条注册，分别指向 `nsaCommand`, `arcCommand`（LUT 行 148, 151）。
