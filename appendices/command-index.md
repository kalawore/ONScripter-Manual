# 附录 A：命令索引（全量 348 条）

> 由 `analysis/commands.json` 对 `func_lut` 全量解析生成。`分册` 指向 `docs/commands/` 下的明细；`坐标` 列 ● 表示该命令在附录 B 的白名单中（含需人工复核项）。

| # | 命令 | 处理函数 | 源码位置 | LUT 行 | 分册 | 坐标 |
|---|---|---|---|---|---|---|
| 1 | `abssetcursor` | `setcursorCommand` | src/onsyuri/ONScripter_command.cpp:755 | 400 | part-06 | ● |
| 2 | `add` | `addCommand` | src/onsyuri/ScriptParser_command.cpp:1493 | 399 | part-01 |  |
| 3 | `addkinsoku` | `addkinsokuCommand` | src/onsyuri/ScriptParser_command.cpp:1480 | 398 | part-01 |  |
| 4 | `allsp2hide` | `allsp2hideCommand` | src/onsyuri/ONScripter_command.cpp:3960 | 397 | part-01 |  |
| 5 | `allsp2resume` | `allsp2resumeCommand` | src/onsyuri/ONScripter_command.cpp:3929 | 396 | part-01 |  |
| 6 | `allsphide` | `allsphideCommand` | src/onsyuri/ONScripter_command.cpp:3972 | 395 | part-01 |  |
| 7 | `allspresume` | `allspresumeCommand` | src/onsyuri/ONScripter_command.cpp:3941 | 394 | part-01 |  |
| 8 | `amsp` | `amspCommand` | src/onsyuri/ONScripter_command.cpp:3888 | 393 | part-01 | ● |
| 9 | `amsp2` | `amspCommand` | src/onsyuri/ONScripter_command.cpp:3888 | 392 | part-01 | ● |
| 10 | `arc` | `arcCommand` | src/onsyuri/ScriptParser_command.cpp:1451 | 391 | part-01 |  |
| 11 | `atoi` | `atoiCommand` | src/onsyuri/ScriptParser_command.cpp:1439 | 390 | part-01 |  |
| 12 | `autoclick` | `autoclickCommand` | src/onsyuri/ONScripter_command.cpp:3881 | 389 | part-01 |  |
| 13 | `automode` | `mode_extCommand` | src/onsyuri/ScriptParser_command.cpp:642 | 388 | part-04 |  |
| 14 | `automode_time` | `automode_timeCommand` | src/onsyuri/ONScripter_command.cpp:3874 | 387 | part-01 |  |
| 15 | `autosaveoff` | `autosaveoffCommand` | src/onsyuri/ScriptParser_command.cpp:1429 | 386 | part-01 |  |
| 16 | `avi` | `aviCommand` | src/onsyuri/ONScripter_command.cpp:3858 | 385 | part-01 |  |
| 17 | `bar` | `barCommand` | src/onsyuri/ONScripter_command.cpp:3816 | 383 | part-01 | ● |
| 18 | `barclear` | `barclearCommand` | src/onsyuri/ONScripter_command.cpp:3804 | 382 | part-01 |  |
| 19 | `bclear` | `btndefCommand` | src/onsyuri/ONScripter_command.cpp:3539 | 381 | part-01 |  |
| 20 | `bcursor` | `getcursorCommand` | src/onsyuri/ONScripter_command.cpp:2612 | 380 | part-03 |  |
| 21 | `bdef` | `exbtnCommand` | src/onsyuri/ONScripter_command.cpp:2819 | 379 | part-02 |  |
| 22 | `bdown` | `bdownCommand` | src/onsyuri/ONScripter_command.cpp:3797 | 378 | part-01 |  |
| 23 | `bexec` | `btnwaitCommand` | src/onsyuri/ONScripter_command.cpp:3355 | 377 | part-01 |  |
| 24 | `bg` | `bgCommand` | src/onsyuri/ONScripter_command.cpp:3764 | 376 | part-01 |  |
| 25 | `bgcopy` | `bgcopyCommand` | src/onsyuri/ONScripter_command.cpp:3750 | 375 | part-01 |  |
| 26 | `bgcpy` | `bgcopyCommand` | src/onsyuri/ONScripter_command.cpp:3750 | 374 | part-01 |  |
| 27 | `bgm` | `mp3Command` | src/onsyuri/ONScripter_command.cpp:1453 | 373 | part-04 |  |
| 28 | `bgmfadein` | `mp3fadeinCommand` | src/onsyuri/ONScripter_command.cpp:1446 | 372 | part-05 |  |
| 29 | `bgmfadeout` | `mp3fadeoutCommand` | src/onsyuri/ONScripter_command.cpp:1439 | 371 | part-05 |  |
| 30 | `bgmonce` | `mp3Command` | src/onsyuri/ONScripter_command.cpp:1453 | 370 | part-04 |  |
| 31 | `bgmstop` | `mp3stopCommand` | src/onsyuri/ONScripter_command.cpp:1404 | 369 | part-05 |  |
| 32 | `bgmvol` | `mp3volCommand` | src/onsyuri/ONScripter_command.cpp:1396 | 368 | part-05 |  |
| 33 | `blt` | `bltCommand` | src/onsyuri/ONScripter_command.cpp:3665 | 367 | part-01 | ● |
| 34 | `br` | `brCommand` | src/onsyuri/ONScripter_command.cpp:3641 | 366 | part-01 |  |
| 35 | `break` | `breakCommand` | src/onsyuri/ScriptParser_command.cpp:1409 | 365 | part-01 |  |
| 36 | `bsp` | `bspCommand` | src/onsyuri/ONScripter_command.cpp:3612 | 364 | part-01 |  |
| 37 | `btime` | `btntimeCommand` | src/onsyuri/ONScripter_command.cpp:3512 | 363 | part-01 |  |
| 38 | `btn` | `btnCommand` | src/onsyuri/ONScripter_command.cpp:3572 | 362 | part-01 | ● |
| 39 | `btndef` | `btndefCommand` | src/onsyuri/ONScripter_command.cpp:3539 | 361 | part-01 |  |
| 40 | `btndown` | `btndownCommand` | src/onsyuri/ONScripter_command.cpp:3532 | 360 | part-01 |  |
| 41 | `btntime` | `btntimeCommand` | src/onsyuri/ONScripter_command.cpp:3512 | 359 | part-01 |  |
| 42 | `btntime2` | `btntimeCommand` | src/onsyuri/ONScripter_command.cpp:3512 | 358 | part-01 |  |
| 43 | `btnwait` | `btnwaitCommand` | src/onsyuri/ONScripter_command.cpp:3355 | 357 | part-01 |  |
| 44 | `btnwait2` | `btnwaitCommand` | src/onsyuri/ONScripter_command.cpp:3355 | 356 | part-01 |  |
| 45 | `btrans` | `transbtnCommand` | src/onsyuri/ONScripter_command.cpp:220 | 355 | part-06 |  |
| 46 | `caption` | `captionCommand` | src/onsyuri/ONScripter_command.cpp:3337 | 353 | part-01 |  |
| 47 | `cell` | `cellCommand` | src/onsyuri/ONScripter_command.cpp:3326 | 352 | part-01 |  |
| 48 | `cellcheckexbtn` | `exbtnCommand` | src/onsyuri/ONScripter_command.cpp:2819 | 351 | part-02 |  |
| 49 | `cellcheckspbtn` | `spbtnCommand` | src/onsyuri/ONScripter_command.cpp:604 | 350 | part-06 |  |
| 50 | `checkkey` | `checkkeyCommand` | src/onsyuri/ONScripter_command.cpp:3312 | 349 | part-01 |  |
| 51 | `checkpage` | `checkpageCommand` | src/onsyuri/ONScripter_command.cpp:3287 | 348 | part-01 |  |
| 52 | `chvol` | `chvolCommand` | src/onsyuri/ONScripter_command.cpp:3275 | 347 | part-01 |  |
| 53 | `cl` | `clCommand` | src/onsyuri/ONScripter_command.cpp:3249 | 346 | part-01 |  |
| 54 | `click` | `clickCommand` | src/onsyuri/ONScripter_command.cpp:3232 | 345 | part-01 |  |
| 55 | `clickstr` | `clickstrCommand` | src/onsyuri/ScriptParser_command.cpp:1397 | 344 | part-01 |  |
| 56 | `clickvoice` | `clickvoiceCommand` | src/onsyuri/ScriptParser_command.cpp:1386 | 343 | part-01 |  |
| 57 | `cmp` | `cmpCommand` | src/onsyuri/ScriptParser_command.cpp:1368 | 342 | part-01 |  |
| 58 | `cos` | `cosCommand` | src/onsyuri/ScriptParser_command.cpp:1357 | 341 | part-01 |  |
| 59 | `csel` | `selectCommand` | src/onsyuri/ONScripter_command.cpp:777 | 340 | part-05 |  |
| 60 | `cselbtn` | `cselbtnCommand` | src/onsyuri/ONScripter_command.cpp:3200 | 339 | part-02 | ● |
| 61 | `cselgoto` | `cselgotoCommand` | src/onsyuri/ONScripter_command.cpp:3180 | 338 | part-02 |  |
| 62 | `csp` | `cspCommand` | src/onsyuri/ONScripter_command.cpp:3131 | 337 | part-02 |  |
| 63 | `csp2` | `cspCommand` | src/onsyuri/ONScripter_command.cpp:3131 | 336 | part-02 |  |
| 64 | `date` | `dateCommand` | src/onsyuri/ScriptParser_command.cpp:1341 | 334 | part-02 |  |
| 65 | `dec` | `decCommand` | src/onsyuri/ScriptParser_command.cpp:1333 | 333 | part-02 |  |
| 66 | `defaultfont` | `defaultfontCommand` | src/onsyuri/ScriptParser_command.cpp:1323 | 332 | part-02 |  |
| 67 | `defaultspeed` | `defaultspeedCommand` | src/onsyuri/ScriptParser_command.cpp:1313 | 331 | part-02 |  |
| 68 | `definereset` | `defineresetCommand` | src/onsyuri/ONScripter_command.cpp:3109 | 330 | part-02 |  |
| 69 | `defmp3vol` | `defmp3volCommand` | src/onsyuri/ScriptParser_command.cpp:1306 | 329 | part-02 |  |
| 70 | `defsevol` | `defsevolCommand` | src/onsyuri/ScriptParser_command.cpp:1299 | 328 | part-02 |  |
| 71 | `defsub` | `defsubCommand` | src/onsyuri/ScriptParser_command.cpp:1285 | 327 | part-02 |  |
| 72 | `defvoicevol` | `defvoicevolCommand` | src/onsyuri/ScriptParser_command.cpp:1278 | 326 | part-02 |  |
| 73 | `delay` | `delayCommand` | src/onsyuri/ONScripter_command.cpp:3096 | 325 | part-02 |  |
| 74 | `deletescreenshot` | `deletescreenshotCommand` | src/onsyuri/ONScripter_command.cpp:3087 | 324 | part-02 |  |
| 75 | `dim` | `dimCommand` | src/onsyuri/ScriptParser_command.cpp:1268 | 323 | part-02 |  |
| 76 | `div` | `divCommand` | src/onsyuri/ScriptParser_command.cpp:1257 | 322 | part-02 |  |
| 77 | `draw` | `drawCommand` | src/onsyuri/ONScripter_command.cpp:3079 | 321 | part-02 |  |
| 78 | `drawbg` | `drawbgCommand` | src/onsyuri/ONScripter_command.cpp:3052 | 320 | part-02 |  |
| 79 | `drawbg2` | `drawbg2Command` | src/onsyuri/ONScripter_command.cpp:3063 | 319 | part-02 | ● |
| 80 | `drawclear` | `drawclearCommand` | src/onsyuri/ONScripter_command.cpp:3045 | 318 | part-02 |  |
| 81 | `drawfill` | `drawfillCommand` | src/onsyuri/ONScripter_command.cpp:3034 | 317 | part-02 |  |
| 82 | `drawsp` | `drawspCommand` | src/onsyuri/ONScripter_command.cpp:3013 | 316 | part-02 | ● |
| 83 | `drawsp2` | `drawsp2Command` | src/onsyuri/ONScripter_command.cpp:2992 | 315 | part-02 | ● |
| 84 | `drawsp3` | `drawsp3Command` | src/onsyuri/ONScripter_command.cpp:2961 | 314 | part-02 | ● |
| 85 | `drawtext` | `drawtextCommand` | src/onsyuri/ONScripter_command.cpp:2950 | 313 | part-02 |  |
| 86 | `dwave` | `dwaveCommand` | src/onsyuri/ONScripter_command.cpp:2903 | 312 | part-02 |  |
| 87 | `dwaveload` | `dwaveCommand` | src/onsyuri/ONScripter_command.cpp:2903 | 311 | part-02 |  |
| 88 | `dwaveloop` | `dwaveCommand` | src/onsyuri/ONScripter_command.cpp:2903 | 310 | part-02 |  |
| 89 | `dwaveplay` | `dwaveCommand` | src/onsyuri/ONScripter_command.cpp:2903 | 309 | part-02 |  |
| 90 | `dwaveplayloop` | `dwaveCommand` | src/onsyuri/ONScripter_command.cpp:2903 | 308 | part-02 |  |
| 91 | `dwavestop` | `dwavestopCommand` | src/onsyuri/ONScripter_command.cpp:2888 | 307 | part-02 |  |
| 92 | `effect` | `effectCommand` | src/onsyuri/ScriptParser_command.cpp:1232 | 305 | part-02 |  |
| 93 | `effectblank` | `effectblankCommand` | src/onsyuri/ScriptParser_command.cpp:1215 | 303 | part-02 |  |
| 94 | `effectcut` | `effectcutCommand` | src/onsyuri/ScriptParser_command.cpp:1205 | 302 | part-02 |  |
| 95 | `effectskip` | `effectskipCommand` | src/onsyuri/ScriptParser_command.cpp:1225 | 304 | part-02 |  |
| 96 | `end` | `endCommand` | src/onsyuri/ONScripter_command.cpp:2880 | 301 | part-02 |  |
| 97 | `english` | `englishCommand` | src/onsyuri/ScriptParser_command.cpp:1194 | 300 | part-02 |  |
| 98 | `erasetextwindow` | `erasetextwindowCommand` | src/onsyuri/ONScripter_command.cpp:2873 | 299 | part-02 |  |
| 99 | `exbtn` | `exbtnCommand` | src/onsyuri/ONScripter_command.cpp:2819 | 298 | part-02 |  |
| 100 | `exbtn_d` | `exbtnCommand` | src/onsyuri/ONScripter_command.cpp:2819 | 297 | part-02 |  |
| 101 | `exec_dll` | `exec_dllCommand` | src/onsyuri/ONScripter_command.cpp:2742 | 296 | part-02 |  |
| 102 | `existspbtn` | `spbtnCommand` | src/onsyuri/ONScripter_command.cpp:604 | 295 | part-06 |  |
| 103 | `fileexist` | `fileexistCommand` | src/onsyuri/ONScripter_command.cpp:2731 | 292 | part-02 |  |
| 104 | `filelog` | `filelogCommand` | src/onsyuri/ScriptParser_command.cpp:1183 | 291 | part-02 |  |
| 105 | `flushout` | `flushoutCommand` | src/onsyuri/ONScripter_command.cpp:2710 | 293 | part-02 |  |
| 106 | `for` | `forCommand` | src/onsyuri/ScriptParser_command.cpp:1132 | 290 | part-02 |  |
| 107 | `game` | `gameCommand` | src/onsyuri/ONScripter_command.cpp:2654 | 288 | part-02 |  |
| 108 | `getbgmvol` | `getmp3volCommand` | src/onsyuri/ONScripter_command.cpp:2475 | 287 | part-03 |  |
| 109 | `getbtntimer` | `gettimerCommand` | src/onsyuri/ONScripter_command.cpp:2153 | 286 | part-03 |  |
| 110 | `getcselnum` | `getcselnumCommand` | src/onsyuri/ONScripter_command.cpp:2639 | 285 | part-02 |  |
| 111 | `getcselstr` | `getcselstrCommand` | src/onsyuri/ONScripter_command.cpp:2620 | 284 | part-02 |  |
| 112 | `getcursor` | `getcursorCommand` | src/onsyuri/ONScripter_command.cpp:2612 | 283 | part-03 |  |
| 113 | `getcursorpos` | `getcursorposCommand` | src/onsyuri/ONScripter_command.cpp:2593 | 282 | part-03 |  |
| 114 | `getcursorpos2` | `getcursorpos2Command` | src/onsyuri/ONScripter_command.cpp:2582 | 281 | part-03 |  |
| 115 | `getenter` | `getenterCommand` | src/onsyuri/ONScripter_command.cpp:2574 | 280 | part-03 |  |
| 116 | `getfunction` | `getfunctionCommand` | src/onsyuri/ONScripter_command.cpp:2567 | 279 | part-03 |  |
| 117 | `getinsert` | `getinsertCommand` | src/onsyuri/ONScripter_command.cpp:2560 | 278 | part-03 |  |
| 118 | `getlog` | `getlogCommand` | src/onsyuri/ONScripter_command.cpp:2503 | 277 | part-03 |  |
| 119 | `getlogtext` | `getlogCommand` | src/onsyuri/ONScripter_command.cpp:2503 | 276 | part-03 |  |
| 120 | `getmclick` | `getmclickCommand` | src/onsyuri/ONScripter_command.cpp:2468 | 275 | part-03 |  |
| 121 | `getmouseover` | `getmouseoverCommand` | src/onsyuri/ONScripter_command.cpp:2493 | 274 | part-03 |  |
| 122 | `getmousepos` | `getmouseposCommand` | src/onsyuri/ONScripter_command.cpp:2482 | 273 | part-03 |  |
| 123 | `getmp3vol` | `getmp3volCommand` | src/onsyuri/ONScripter_command.cpp:2475 | 272 | part-03 |  |
| 124 | `getpage` | `getpageCommand` | src/onsyuri/ONScripter_command.cpp:2380 | 271 | part-03 |  |
| 125 | `getpageup` | `getpageupCommand` | src/onsyuri/ONScripter_command.cpp:2373 | 270 | part-03 |  |
| 126 | `getparam` | `getparamCommand` | src/onsyuri/ScriptParser_command.cpp:1074 | 269 | part-03 |  |
| 127 | `getparam2` | `getparamCommand` | src/onsyuri/ScriptParser_command.cpp:1074 | 268 | part-03 |  |
| 128 | `getreg` | `getregCommand` | src/onsyuri/ONScripter_command.cpp:2405 | 267 | part-03 |  |
| 129 | `getret` | `getretCommand` | src/onsyuri/ONScripter_command.cpp:2388 | 266 | part-03 |  |
| 130 | `getsavestr` | `getsavestrCommand` | src/onsyuri/ONScripter_command.cpp:2356 | 265 | part-03 |  |
| 131 | `getscreenshot` | `getscreenshotCommand` | src/onsyuri/ONScripter_command.cpp:2333 | 264 | part-03 | ● |
| 132 | `getsevol` | `getsevolCommand` | src/onsyuri/ONScripter_command.cpp:2326 | 263 | part-03 |  |
| 133 | `getspmode` | `getspmodeCommand` | src/onsyuri/ONScripter_command.cpp:2315 | 262 | part-03 |  |
| 134 | `getsppos` | `getspposCommand` | src/onsyuri/ONScripter_command.cpp:2302 | 261 | part-03 |  |
| 135 | `getspsize` | `getspsizeCommand` | src/onsyuri/ONScripter_command.cpp:2286 | 260 | part-03 |  |
| 136 | `gettab` | `gettabCommand` | src/onsyuri/ONScripter_command.cpp:2279 | 259 | part-03 |  |
| 137 | `gettag` | `gettagCommand` | src/onsyuri/ONScripter_command.cpp:2215 | 258 | part-03 |  |
| 138 | `gettaglog` | `gettaglogCommand` | src/onsyuri/ONScripter_command.cpp:2194 | 257 | part-03 |  |
| 139 | `gettext` | `gettextCommand` | src/onsyuri/ONScripter_command.cpp:2175 | 256 | part-03 |  |
| 140 | `gettimer` | `gettimerCommand` | src/onsyuri/ONScripter_command.cpp:2153 | 255 | part-03 |  |
| 141 | `getversion` | `getversionCommand` | src/onsyuri/ONScripter_command.cpp:2145 | 254 | part-03 |  |
| 142 | `getvoicevol` | `getvoicevolCommand` | src/onsyuri/ONScripter_command.cpp:2138 | 253 | part-03 |  |
| 143 | `getzxc` | `getzxcCommand` | src/onsyuri/ONScripter_command.cpp:2131 | 252 | part-03 |  |
| 144 | `globalon` | `globalonCommand` | src/onsyuri/ScriptParser_command.cpp:1065 | 251 | part-03 |  |
| 145 | `gosub` | `gosubCommand` | src/onsyuri/ScriptParser_command.cpp:1057 | 250 | part-03 |  |
| 146 | `goto` | `gotoCommand` | src/onsyuri/ScriptParser_command.cpp:1038 | 249 | part-03 |  |
| 147 | `humanorder` | `humanorderCommand` | src/onsyuri/ONScripter_command.cpp:2107 | 247 | part-03 |  |
| 148 | `humanz` | `humanzCommand` | src/onsyuri/ScriptParser_command.cpp:1031 | 246 | part-03 |  |
| 149 | `if` | `ifCommand` | src/onsyuri/ScriptParser_command.cpp:912 | 244 | part-03 |  |
| 150 | `inc` | `incCommand` | src/onsyuri/ScriptParser_command.cpp:904 | 243 | part-03 |  |
| 151 | `indent` | `indentCommand` | src/onsyuri/ONScripter_command.cpp:2100 | 242 | part-03 |  |
| 152 | `input` | `inputCommand` | src/onsyuri/ONScripter_command.cpp:2074 | 241 | part-03 |  |
| 153 | `intlimit` | `intlimitCommand` | src/onsyuri/ScriptParser_command.cpp:890 | 240 | part-03 |  |
| 154 | `isdown` | `isdownCommand` | src/onsyuri/ONScripter_command.cpp:2057 | 239 | part-03 |  |
| 155 | `isfull` | `isfullCommand` | src/onsyuri/ONScripter_command.cpp:2035 | 238 | part-03 |  |
| 156 | `ispage` | `ispageCommand` | src/onsyuri/ONScripter_command.cpp:2023 | 237 | part-03 |  |
| 157 | `isskip` | `isskipCommand` | src/onsyuri/ONScripter_command.cpp:2043 | 236 | part-04 |  |
| 158 | `itoa` | `itoaCommand` | src/onsyuri/ScriptParser_command.cpp:866 | 235 | part-04 |  |
| 159 | `itoa2` | `itoaCommand` | src/onsyuri/ScriptParser_command.cpp:866 | 234 | part-04 |  |
| 160 | `jumpb` | `jumpbCommand` | src/onsyuri/ONScripter_command.cpp:2014 | 229 | part-04 |  |
| 161 | `jumpf` | `jumpfCommand` | src/onsyuri/ONScripter_command.cpp:2001 | 228 | part-04 |  |
| 162 | `kidokumode` | `kidokumodeCommand` | src/onsyuri/ScriptParser_command.cpp:856 | 232 | part-04 |  |
| 163 | `kidokuskip` | `kidokuskipCommand` | src/onsyuri/ScriptParser_command.cpp:848 | 231 | part-04 |  |
| 164 | `kinsoku` | `kinsokuCommand` | src/onsyuri/ONScripter_command.cpp:1987 | 226 | part-04 |  |
| 165 | `labellog` | `labellogCommand` | src/onsyuri/ScriptParser_command.cpp:837 | 224 | part-04 |  |
| 166 | `layermessage` | `layermessageCommand` | src/onsyuri/ONScripter_command.cpp:1967 | 223 | part-04 |  |
| 167 | `ld` | `ldCommand` | src/onsyuri/ONScripter_command.cpp:1906 | 222 | part-04 |  |
| 168 | `len` | `lenCommand` | src/onsyuri/ScriptParser_command.cpp:825 | 221 | part-04 |  |
| 169 | `linepage` | `linepageCommand` | src/onsyuri/ScriptParser_command.cpp:808 | 220 | part-04 |  |
| 170 | `linepage2` | `linepageCommand` | src/onsyuri/ScriptParser_command.cpp:808 | 219 | part-04 |  |
| 171 | `loadgame` | `loadgameCommand` | src/onsyuri/ONScripter_command.cpp:1865 | 218 | part-04 |  |
| 172 | `loadgosub` | `loadgosubCommand` | src/onsyuri/ScriptParser_command.cpp:798 | 217 | part-04 |  |
| 173 | `locate` | `locateCommand` | src/onsyuri/ONScripter_command.cpp:1856 | 216 | part-04 |  |
| 174 | `logsp` | `logspCommand` | src/onsyuri/ONScripter_command.cpp:1788 | 215 | part-04 | ● |
| 175 | `logsp2` | `logspCommand` | src/onsyuri/ONScripter_command.cpp:1788 | 214 | part-04 | ● |
| 176 | `lookbackbutton` | `lookbackbuttonCommand` | src/onsyuri/ONScripter_command.cpp:1777 | 213 | part-04 |  |
| 177 | `lookbackcolor` | `lookbackcolorCommand` | src/onsyuri/ScriptParser_command.cpp:790 | 212 | part-04 |  |
| 178 | `lookbackflush` | `lookbackflushCommand` | src/onsyuri/ONScripter_command.cpp:1764 | 211 | part-04 |  |
| 179 | `lookbacksp` | `lookbackspCommand` | src/onsyuri/ScriptParser_command.cpp:772 | 210 | part-04 |  |
| 180 | `loopbgm` | `loopbgmCommand` | src/onsyuri/ONScripter_command.cpp:1749 | 209 | part-04 |  |
| 181 | `loopbgmstop` | `loopbgmstopCommand` | src/onsyuri/ONScripter_command.cpp:1732 | 208 | part-04 |  |
| 182 | `lr_trap` | `trapCommand` | src/onsyuri/ONScripter_command.cpp:179 | 206 | part-06 |  |
| 183 | `lrclick` | `clickCommand` | src/onsyuri/ONScripter_command.cpp:3232 | 207 | part-01 |  |
| 184 | `lsp` | `lspCommand` | src/onsyuri/ONScripter_command.cpp:1699 | 205 | part-04 | ● |
| 185 | `lsp2` | `lsp2Command` | src/onsyuri/ONScripter_command.cpp:1650 | 204 | part-04 | ● |
| 186 | `lsp2add` | `lsp2Command` | src/onsyuri/ONScripter_command.cpp:1650 | 203 | part-04 | ● |
| 187 | `lsp2sub` | `lsp2Command` | src/onsyuri/ONScripter_command.cpp:1650 | 202 | part-04 | ● |
| 188 | `lsph` | `lspCommand` | src/onsyuri/ONScripter_command.cpp:1699 | 201 | part-04 | ● |
| 189 | `lsph2` | `lsp2Command` | src/onsyuri/ONScripter_command.cpp:1650 | 200 | part-04 | ● |
| 190 | `lsph2add` | `lsp2Command` | src/onsyuri/ONScripter_command.cpp:1650 | 199 | part-04 | ● |
| 191 | `lsph2sub` | `lsp2Command` | src/onsyuri/ONScripter_command.cpp:1650 | 198 | part-04 | ● |
| 192 | `luacall` | `luacallCommand` | src/onsyuri/ScriptParser_command.cpp:761 | 197 | part-04 |  |
| 193 | `luasub` | `luasubCommand` | src/onsyuri/ScriptParser_command.cpp:746 | 196 | part-04 |  |
| 194 | `maxkaisoupage` | `maxkaisoupageCommand` | src/onsyuri/ScriptParser_command.cpp:737 | 194 | part-04 |  |
| 195 | `menu_automode` | `menu_automodeCommand` | src/onsyuri/ONScripter_command.cpp:1641 | 190 | part-04 |  |
| 196 | `menu_click_def` | `menu_click_defCommand` | src/onsyuri/ONScripter_command.cpp:1635 | 189 | part-04 |  |
| 197 | `menu_click_page` | `menu_click_pageCommand` | src/onsyuri/ONScripter_command.cpp:1629 | 188 | part-04 |  |
| 198 | `menu_full` | `menu_fullCommand` | src/onsyuri/ONScripter_command.cpp:1623 | 187 | part-04 |  |
| 199 | `menu_window` | `menu_windowCommand` | src/onsyuri/ONScripter_command.cpp:1617 | 186 | part-04 |  |
| 200 | `menuselectcolor` | `menuselectcolorCommand` | src/onsyuri/ScriptParser_command.cpp:723 | 193 | part-04 |  |
| 201 | `menuselectvoice` | `menuselectvoiceCommand` | src/onsyuri/ScriptParser_command.cpp:712 | 192 | part-04 |  |
| 202 | `menusetwindow` | `menusetwindowCommand` | src/onsyuri/ScriptParser_command.cpp:690 | 191 | part-04 | ● |
| 203 | `mid` | `midCommand` | src/onsyuri/ScriptParser_command.cpp:662 | 185 | part-04 |  |
| 204 | `mod` | `modCommand` | src/onsyuri/ScriptParser_command.cpp:651 | 184 | part-04 |  |
| 205 | `mode_ext` | `mode_extCommand` | src/onsyuri/ScriptParser_command.cpp:642 | 183 | part-04 |  |
| 206 | `mode_saya` | `mode_sayaCommand` | src/onsyuri/ScriptParser_command.cpp:633 | 182 | part-04 |  |
| 207 | `mode_wave_demo` | `mode_wave_demoCommand` | src/onsyuri/ScriptParser_command.cpp:624 | 181 | part-04 |  |
| 208 | `monocro` | `monocroCommand` | src/onsyuri/ONScripter_command.cpp:1595 | 180 | part-04 |  |
| 209 | `mov` | `movCommand` | src/onsyuri/ScriptParser_command.cpp:587 | 179 | part-04 |  |
| 210 | `mov10` | `movCommand` | src/onsyuri/ScriptParser_command.cpp:587 | 178 | part-04 |  |
| 211 | `mov3` | `movCommand` | src/onsyuri/ScriptParser_command.cpp:587 | 177 | part-04 |  |
| 212 | `mov4` | `movCommand` | src/onsyuri/ScriptParser_command.cpp:587 | 176 | part-04 |  |
| 213 | `mov5` | `movCommand` | src/onsyuri/ScriptParser_command.cpp:587 | 175 | part-04 |  |
| 214 | `mov6` | `movCommand` | src/onsyuri/ScriptParser_command.cpp:587 | 174 | part-04 |  |
| 215 | `mov7` | `movCommand` | src/onsyuri/ScriptParser_command.cpp:587 | 173 | part-04 |  |
| 216 | `mov8` | `movCommand` | src/onsyuri/ScriptParser_command.cpp:587 | 172 | part-04 |  |
| 217 | `mov9` | `movCommand` | src/onsyuri/ScriptParser_command.cpp:587 | 171 | part-04 |  |
| 218 | `movemousecursor` | `movemousecursorCommand` | src/onsyuri/ONScripter_command.cpp:1583 | 170 | part-04 | ● |
| 219 | `movie` | `movieCommand` | src/onsyuri/ONScripter_command.cpp:1536 | 169 | part-04 |  |
| 220 | `movl` | `movCommand` | src/onsyuri/ScriptParser_command.cpp:587 | 168 | part-04 |  |
| 221 | `mp3` | `mp3Command` | src/onsyuri/ONScripter_command.cpp:1453 | 167 | part-04 |  |
| 222 | `mp3fadein` | `mp3fadeinCommand` | src/onsyuri/ONScripter_command.cpp:1446 | 166 | part-05 |  |
| 223 | `mp3fadeout` | `mp3fadeoutCommand` | src/onsyuri/ONScripter_command.cpp:1439 | 165 | part-05 |  |
| 224 | `mp3loop` | `mp3Command` | src/onsyuri/ONScripter_command.cpp:1453 | 164 | part-04 |  |
| 225 | `mp3save` | `mp3Command` | src/onsyuri/ONScripter_command.cpp:1453 | 163 | part-04 |  |
| 226 | `mp3stop` | `mp3stopCommand` | src/onsyuri/ONScripter_command.cpp:1404 | 162 | part-05 |  |
| 227 | `mp3vol` | `mp3volCommand` | src/onsyuri/ONScripter_command.cpp:1396 | 161 | part-05 |  |
| 228 | `mpegplay` | `mpegplayCommand` | src/onsyuri/ONScripter_command.cpp:1381 | 160 | part-05 |  |
| 229 | `msp` | `mspCommand` | src/onsyuri/ONScripter_command.cpp:1337 | 159 | part-05 | ● |
| 230 | `msp2` | `mspCommand` | src/onsyuri/ONScripter_command.cpp:1337 | 158 | part-05 | ● |
| 231 | `mul` | `mulCommand` | src/onsyuri/ScriptParser_command.cpp:576 | 157 | part-05 |  |
| 232 | `nega` | `negaCommand` | src/onsyuri/ONScripter_command.cpp:1309 | 154 | part-05 |  |
| 233 | `next` | `nextCommand` | src/onsyuri/ScriptParser_command.cpp:545 | 150 | part-05 |  |
| 234 | `nextcsel` | `nextcselCommand` | src/onsyuri/ONScripter_command.cpp:1318 | 155 | part-05 |  |
| 235 | `notif` | `ifCommand` | src/onsyuri/ScriptParser_command.cpp:912 | 149 | part-03 |  |
| 236 | `ns2` | `nsaCommand` | src/onsyuri/ScriptParser_command.cpp:527 | 153 | part-05 |  |
| 237 | `ns3` | `nsaCommand` | src/onsyuri/ScriptParser_command.cpp:527 | 152 | part-05 |  |
| 238 | `nsa` | `nsaCommand` | src/onsyuri/ScriptParser_command.cpp:527 | 148 | part-05 |  |
| 239 | `nsa` | `arcCommand` | src/onsyuri/ScriptParser_command.cpp:1451 | 151 | part-01 |  |
| 240 | `nsadir` | `nsadirCommand` | src/onsyuri/ScriptParser_command.cpp:513 | 147 | part-05 |  |
| 241 | `numalias` | `numaliasCommand` | src/onsyuri/ScriptParser_command.cpp:499 | 146 | part-05 |  |
| 242 | `ofscopy` | `ofscopyCommand` | src/onsyuri/ONScripter_command.cpp:1297 | 144 | part-05 |  |
| 243 | `ofscpy` | `ofscopyCommand` | src/onsyuri/ONScripter_command.cpp:1297 | 143 | part-05 |  |
| 244 | `okcancelbox` | `yesnoboxCommand` | src/onsyuri/ONScripter_command.cpp:49 | 142 | part-07 |  |
| 245 | `pagetag` | `pagetagCommand` | src/onsyuri/ScriptParser_command.cpp:489 | 140 | part-05 |  |
| 246 | `play` | `playCommand` | src/onsyuri/ONScripter_command.cpp:1264 | 139 | part-05 |  |
| 247 | `playonce` | `playCommand` | src/onsyuri/ONScripter_command.cpp:1264 | 138 | part-05 |  |
| 248 | `playstop` | `playstopCommand` | src/onsyuri/ONScripter_command.cpp:1257 | 137 | part-05 |  |
| 249 | `pretextgosub` | `pretextgosubCommand` | src/onsyuri/ScriptParser_command.cpp:479 | 136 | part-05 |  |
| 250 | `print` | `printCommand` | src/onsyuri/ONScripter_command.cpp:1246 | 135 | part-05 |  |
| 251 | `prnum` | `prnumCommand` | src/onsyuri/ONScripter_command.cpp:1199 | 134 | part-05 | ● |
| 252 | `prnumclear` | `prnumclearCommand` | src/onsyuri/ONScripter_command.cpp:1187 | 133 | part-05 |  |
| 253 | `puttext` | `puttextCommand` | src/onsyuri/ONScripter_command.cpp:1170 | 132 | part-05 |  |
| 254 | `quake` | `quakeCommand` | src/onsyuri/ONScripter_command.cpp:1142 | 130 | part-05 | ● |
| 255 | `quakex` | `quakeCommand` | src/onsyuri/ONScripter_command.cpp:1142 | 129 | part-05 | ● |
| 256 | `quakey` | `quakeCommand` | src/onsyuri/ONScripter_command.cpp:1142 | 128 | part-05 | ● |
| 257 | `repaint` | `repaintCommand` | src/onsyuri/ONScripter_command.cpp:1134 | 126 | part-05 |  |
| 258 | `reset` | `resetCommand` | src/onsyuri/ONScripter_command.cpp:1113 | 125 | part-05 |  |
| 259 | `resettimer` | `resettimerCommand` | src/onsyuri/ONScripter_command.cpp:1106 | 124 | part-05 |  |
| 260 | `return` | `returnCommand` | src/onsyuri/ScriptParser_command.cpp:449 | 123 | part-05 |  |
| 261 | `rmenu` | `rmenuCommand` | src/onsyuri/ScriptParser_command.cpp:424 | 122 | part-05 |  |
| 262 | `rmode` | `rmodeCommand` | src/onsyuri/ONScripter_command.cpp:1098 | 121 | part-05 |  |
| 263 | `rnd` | `rndCommand` | src/onsyuri/ONScripter_command.cpp:1074 | 120 | part-05 |  |
| 264 | `rnd2` | `rndCommand` | src/onsyuri/ONScripter_command.cpp:1074 | 119 | part-05 |  |
| 265 | `roff` | `roffCommand` | src/onsyuri/ScriptParser_command.cpp:415 | 118 | part-05 |  |
| 266 | `rubyoff` | `rubyoffCommand` | src/onsyuri/ScriptParser_command.cpp:408 | 117 | part-05 |  |
| 267 | `rubyon` | `rubyonCommand` | src/onsyuri/ScriptParser_command.cpp:383 | 116 | part-05 | ● |
| 268 | `sar` | `nsaCommand` | src/onsyuri/ScriptParser_command.cpp:527 | 114 | part-05 |  |
| 269 | `savedir` | `savedirCommand` | src/onsyuri/ScriptParser_command.cpp:349 | 113 | part-05 |  |
| 270 | `savefileexist` | `savefileexistCommand` | src/onsyuri/ONScripter_command.cpp:1060 | 112 | part-05 |  |
| 271 | `savegame` | `savegameCommand` | src/onsyuri/ONScripter_command.cpp:1042 | 111 | part-05 |  |
| 272 | `savegame2` | `savegameCommand` | src/onsyuri/ONScripter_command.cpp:1042 | 110 | part-05 |  |
| 273 | `savename` | `savenameCommand` | src/onsyuri/ScriptParser_command.cpp:335 | 109 | part-05 |  |
| 274 | `savenumber` | `savenumberCommand` | src/onsyuri/ScriptParser_command.cpp:328 | 108 | part-05 |  |
| 275 | `saveoff` | `saveoffCommand` | src/onsyuri/ONScripter_command.cpp:1031 | 107 | part-05 |  |
| 276 | `saveon` | `saveonCommand` | src/onsyuri/ONScripter_command.cpp:1023 | 106 | part-05 |  |
| 277 | `savepoint` | `savepointCommand` | src/onsyuri/ONScripter_command.cpp:1016 | 105 | part-05 |  |
| 278 | `savescreenshot` | `savescreenshotCommand` | src/onsyuri/ONScripter_command.cpp:968 | 104 | part-05 |  |
| 279 | `savescreenshot2` | `savescreenshotCommand` | src/onsyuri/ONScripter_command.cpp:968 | 103 | part-05 |  |
| 280 | `savetime` | `savetimeCommand` | src/onsyuri/ONScripter_command.cpp:942 | 102 | part-05 |  |
| 281 | `select` | `selectCommand` | src/onsyuri/ONScripter_command.cpp:777 | 101 | part-05 |  |
| 282 | `selectbtnwait` | `btnwaitCommand` | src/onsyuri/ONScripter_command.cpp:3355 | 100 | part-01 |  |
| 283 | `selectcolor` | `selectcolorCommand` | src/onsyuri/ScriptParser_command.cpp:317 | 99 | part-06 |  |
| 284 | `selectvoice` | `selectvoiceCommand` | src/onsyuri/ScriptParser_command.cpp:306 | 98 | part-06 |  |
| 285 | `selgosub` | `selectCommand` | src/onsyuri/ONScripter_command.cpp:777 | 97 | part-05 |  |
| 286 | `selnum` | `selectCommand` | src/onsyuri/ONScripter_command.cpp:777 | 96 | part-05 |  |
| 287 | `setcursor` | `setcursorCommand` | src/onsyuri/ONScripter_command.cpp:755 | 95 | part-06 | ● |
| 288 | `setkinsoku` | `setkinsokuCommand` | src/onsyuri/ScriptParser_command.cpp:293 | 94 | part-06 |  |
| 289 | `setlayer` | `setlayerCommand` | src/onsyuri/ScriptParser_command.cpp:252 | 93 | part-06 |  |
| 290 | `setwindow` | `setwindowCommand` | src/onsyuri/ONScripter_command.cpp:741 | 92 | part-06 | ● |
| 291 | `setwindow2` | `setwindow2Command` | src/onsyuri/ONScripter_command.cpp:722 | 91 | part-06 |  |
| 292 | `setwindow3` | `setwindow3Command` | src/onsyuri/ONScripter_command.cpp:708 | 90 | part-06 | ● |
| 293 | `sevol` | `sevolCommand` | src/onsyuri/ONScripter_command.cpp:647 | 89 | part-06 |  |
| 294 | `shadedistance` | `shadedistanceCommand` | src/onsyuri/ScriptParser_command.cpp:238 | 88 | part-06 | ● |
| 295 | `sin` | `sinCommand` | src/onsyuri/ScriptParser_command.cpp:227 | 87 | part-06 |  |
| 296 | `skip` | `skipCommand` | src/onsyuri/ScriptParser_command.cpp:211 | 86 | part-06 |  |
| 297 | `skipoff` | `skipoffCommand` | src/onsyuri/ONScripter_command.cpp:640 | 85 | part-06 |  |
| 298 | `soundpressplgin` | `soundpressplginCommand` | src/onsyuri/ScriptParser_command.cpp:186 | 84 | part-06 |  |
| 299 | `sp_rgb_gradation` | `sp_rgb_gradationCommand` | src/onsyuri/ONScripter_command.cpp:434 | 76 | part-06 |  |
| 300 | `spbtn` | `spbtnCommand` | src/onsyuri/ONScripter_command.cpp:604 | 83 | part-06 |  |
| 301 | `spclclk` | `spclclkCommand` | src/onsyuri/ONScripter_command.cpp:597 | 82 | part-06 |  |
| 302 | `spi` | `soundpressplginCommand` | src/onsyuri/ScriptParser_command.cpp:186 | 81 | part-06 |  |
| 303 | `split` | `splitCommand` | src/onsyuri/ONScripter_command.cpp:550 | 80 | part-06 |  |
| 304 | `splitstring` | `splitCommand` | src/onsyuri/ONScripter_command.cpp:550 | 79 | part-06 |  |
| 305 | `spreload` | `spreloadCommand` | src/onsyuri/ONScripter_command.cpp:534 | 78 | part-06 |  |
| 306 | `spstr` | `spstrCommand` | src/onsyuri/ONScripter_command.cpp:527 | 77 | part-06 |  |
| 307 | `stop` | `stopCommand` | src/onsyuri/ONScripter_command.cpp:426 | 75 | part-06 |  |
| 308 | `stralias` | `straliasCommand` | src/onsyuri/ScriptParser_command.cpp:172 | 74 | part-06 |  |
| 309 | `strsp` | `strspCommand` | src/onsyuri/ONScripter_command.cpp:366 | 73 | part-06 | ● |
| 310 | `strsph` | `strspCommand` | src/onsyuri/ONScripter_command.cpp:366 | 72 | part-06 | ● |
| 311 | `sub` | `subCommand` | src/onsyuri/ScriptParser_command.cpp:161 | 71 | part-06 |  |
| 312 | `systemcall` | `systemcallCommand` | src/onsyuri/ONScripter_command.cpp:357 | 70 | part-06 |  |
| 313 | `tablegoto` | `tablegotoCommand` | src/onsyuri/ONScripter_command.cpp:341 | 68 | part-06 |  |
| 314 | `tal` | `talCommand` | src/onsyuri/ONScripter_command.cpp:316 | 67 | part-06 |  |
| 315 | `tan` | `tanCommand` | src/onsyuri/ScriptParser_command.cpp:150 | 66 | part-06 |  |
| 316 | `tateyoko` | `tateyokoCommand` | src/onsyuri/ONScripter_command.cpp:309 | 65 | part-06 |  |
| 317 | `texec` | `texecCommand` | src/onsyuri/ONScripter_command.cpp:295 | 64 | part-06 |  |
| 318 | `textbtnwait` | `btnwaitCommand` | src/onsyuri/ONScripter_command.cpp:3355 | 63 | part-01 |  |
| 319 | `textclear` | `textclearCommand` | src/onsyuri/ONScripter_command.cpp:289 | 62 | part-06 |  |
| 320 | `textcolor` | `textcolorCommand` | src/onsyuri/ONScripter_command.cpp:282 | 61 | part-06 |  |
| 321 | `textgosub` | `textgosubCommand` | src/onsyuri/ScriptParser_command.cpp:140 | 60 | part-06 |  |
| 322 | `texthide` | `texthideCommand` | src/onsyuri/ONScripter_command.cpp:273 | 59 | part-06 |  |
| 323 | `textoff` | `textoffCommand` | src/onsyuri/ONScripter_command.cpp:261 | 58 | part-06 |  |
| 324 | `texton` | `textonCommand` | src/onsyuri/ONScripter_command.cpp:249 | 57 | part-06 |  |
| 325 | `textshow` | `textshowCommand` | src/onsyuri/ONScripter_command.cpp:240 | 56 | part-06 |  |
| 326 | `textspeed` | `textspeedCommand` | src/onsyuri/ONScripter_command.cpp:233 | 55 | part-06 |  |
| 327 | `textspeeddefault` | `textspeeddefaultCommand` | src/onsyuri/ONScripter_command.cpp:227 | 54 | part-06 |  |
| 328 | `time` | `timeCommand` | src/onsyuri/ScriptParser_command.cpp:123 | 53 | part-06 |  |
| 329 | `transbtn` | `transbtnCommand` | src/onsyuri/ONScripter_command.cpp:220 | 52 | part-06 |  |
| 330 | `transmode` | `transmodeCommand` | src/onsyuri/ScriptParser_command.cpp:109 | 51 | part-06 |  |
| 331 | `trap` | `trapCommand` | src/onsyuri/ONScripter_command.cpp:179 | 50 | part-06 |  |
| 332 | `underline` | `underlineCommand` | src/onsyuri/ScriptParser_command.cpp:102 | 48 | part-06 | ● |
| 333 | `useescspc` | `useescspcCommand` | src/onsyuri/ScriptParser_command.cpp:91 | 47 | part-06 |  |
| 334 | `usewheel` | `usewheelCommand` | src/onsyuri/ScriptParser_command.cpp:81 | 46 | part-06 |  |
| 335 | `versionstr` | `versionstrCommand` | src/onsyuri/ScriptParser_command.cpp:67 | 44 | part-06 |  |
| 336 | `voicevol` | `voicevolCommand` | src/onsyuri/ONScripter_command.cpp:161 | 43 | part-06 |  |
| 337 | `vsp` | `vspCommand` | src/onsyuri/ONScripter_command.cpp:139 | 42 | part-06 |  |
| 338 | `vsp2` | `vspCommand` | src/onsyuri/ONScripter_command.cpp:139 | 41 | part-06 |  |
| 339 | `wait` | `waitCommand` | src/onsyuri/ONScripter_command.cpp:131 | 39 | part-06 |  |
| 340 | `waittimer` | `waittimerCommand` | src/onsyuri/ONScripter_command.cpp:120 | 38 | part-06 |  |
| 341 | `wave` | `waveCommand` | src/onsyuri/ONScripter_command.cpp:105 | 37 | part-07 |  |
| 342 | `waveloop` | `waveCommand` | src/onsyuri/ONScripter_command.cpp:105 | 36 | part-07 |  |
| 343 | `wavestop` | `wavestopCommand` | src/onsyuri/ONScripter_command.cpp:93 | 35 | part-07 |  |
| 344 | `windowback` | `windowbackCommand` | src/onsyuri/ScriptParser_command.cpp:58 | 34 | part-07 |  |
| 345 | `windowchip` | `windowchipCommand` | src/onsyuri/ScriptParser_command.cpp:49 | 33 | part-07 |  |
| 346 | `windoweffect` | `effectCommand` | src/onsyuri/ScriptParser_command.cpp:1232 | 32 | part-02 |  |
| 347 | `yesnobox` | `yesnoboxCommand` | src/onsyuri/ONScripter_command.cpp:49 | 30 | part-07 |  |
| 348 | `zenkakko` | `zenkakkoCommand` | src/onsyuri/ScriptParser_command.cpp:40 | 28 | part-07 |  |
