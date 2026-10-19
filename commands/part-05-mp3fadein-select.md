# 命令分册 05：处理函数 `mp3fadeinCommand` – `selectCommand`

> 本册覆盖 `func_lut` 中 `mp3fadeinCommand`…`selectCommand` 区间的 44 个处理函数、62 个注册命令名（含别名与重复注册）。
> 行号均指快照 `src/onsyuri/`。参数按**源码读取顺序**列出，逗号分隔；`[ ]` 表示可选（多数由 `script_h.getEndStatus() & END_COMMA` 判断）。
> 「坐标」判定：**是** = 含逻辑坐标/尺寸参数；**否** = 不含；**部分** = 仅部分参数是坐标。
> 判定依据：参数是否直接写入 `orig_pos.x/y/w/h`、`pos`、`image_rect`、`top_xy`、`num_xy`、`font_size_xy`、`pitch_xy`、`max_width`、`dst_rect/src_rect`，或与 `screen_width/height`、`screen_ratio1/2` 做像素运算。
> 证据一律写作 `文件:行`（省略目录前缀 `src/onsyuri/`）。凡源码无法确证之处标注「待验证」，不作推测性断言。

---

## M

### `mp3fadein` / `bgmfadein`
- 处理函数：`mp3fadeinCommand` — `ONScripter_command.cpp:1446`
- 注册名：`mp3fadein`（`ONScripter_lut.cpp:166`）、`bgmfadein`（`ONScripter_lut.cpp:372`）
- 语法：`mp3fadein <时长>`
- 参数：
  - P1 `duration`：淡入时长，单位毫秒（`readInt()`，写入 `mp3fadein_duration`，`:1448`）。类型 `Uint32`（`ONScripter.h:728`），源码未做范围校验。
- 可选参数：无（`readInt()` 无条件读取）。
- 语义：仅登记淡入时长；真正生效发生在之后的 `mp3`/`bgm` 起播时——起播前把 `music_volume` 置 0，再挂 20ms 定时器回调 `bgmfadeCallback` 逐步提升（`ONScripter_command.cpp:1502-1528`）。OGG 不等待淡入结束，其它格式进入 `WAIT_TIMER_MODE` 等待（`:1520-1529`）。
- 坐标：**否**（时间量）。
- 备注：负值写入 `Uint32` 后的实际行为**待验证**（`:1448` + `ONScripter.h:728`）。
- 证据：`ONScripter_command.cpp:1446-1451`；`ONScripter.h:728`；`ONScripter_command.cpp:1502-1528`。

### `mp3fadeout` / `bgmfadeout`
- 处理函数：`mp3fadeoutCommand` — `ONScripter_command.cpp:1439`
- 注册名：`mp3fadeout`（`ONScripter_lut.cpp:165`）、`bgmfadeout`（`ONScripter_lut.cpp:371`）
- 语法：`mp3fadeout <时长>`
- 参数：
  - P1 `duration`：淡出时长，单位毫秒（`readInt()`，写入 `mp3fadeout_duration`，`:1441`）。类型 `Uint32`（`ONScripter.h:727`），无范围校验。
- 可选参数：无。
- 语义：只登记时长；真正的淡出由 `mp3stop`/`bgmstop` 触发（`:1409-1432`）。`reset`、读档等内部流程会临时把该值置 0 以禁用淡出（`:1115-1118`、`:1869-1901`）。
- 坐标：**否**（时间量）。
- 备注：负值转 `Uint32` 后的行为**待验证**（`:1441` + `ONScripter.h:727`）。
- 证据：`ONScripter_command.cpp:1439-1444`；`ONScripter.h:727`；`ONScripter_command.cpp:1409-1432`。

### `mp3stop` / `bgmstop`
- 处理函数：`mp3stopCommand` — `ONScripter_command.cpp:1404`
- 注册名：`mp3stop`（`ONScripter_lut.cpp:162`）、`bgmstop`（`ONScripter_lut.cpp:369`）
- 语法：`mp3stop`（无参数）
- 参数：无（函数体不读取任何脚本 token）。
- 可选参数：无。
- 语义与分支：
  1. 已在淡出中（`timer_bgmfade_id` 有效且 `mp3fadeout_duration_internal > 0`）→ 直接返回（`:1406-1407`）。
  2. 正在播放且 `mp3fadeout_duration > 0` → 启动淡出定时器（20ms，`:1414`）：
     - 文件扩展名为 `OGG`/`ogg`：不等待，置 `IDLE_EVENT_MODE` 并 `waitEvent(0)`，同时清空 `music_file_name`（`:1419-1426`）。
     - 其它格式：置 `WAIT_TIMER_MODE` 并 `waitEvent(-1)`，**阻塞直到淡出结束**（`:1427-1431`）。
  3. 最后 `stopBGM(false)`（`:1434`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:1404-1437`。

### `mp3vol` / `bgmvol`
- 处理函数：`mp3volCommand` — `ONScripter_command.cpp:1396`
- 注册名：`mp3vol`（`ONScripter_lut.cpp:161`）、`bgmvol`（`ONScripter_lut.cpp:368`）
- 语法：`mp3vol <音量>`
- 参数：
  - P1 `volume`：BGM 音量百分比（`readInt()` → `music_volume`，`:1398`），按 `volume * MIX_MAX_VOLUME / 100` 交给 `Mix_VolumeMusic`（`:1399`）。逻辑取值 0–100；源码**不钳制**，越界值由 SDL_mixer 侧处理。
- 可选参数：无。
- 坐标：**否**（音量）。
- 证据：`ONScripter_command.cpp:1396-1402`。

### `mpegplay`
- 处理函数：`mpegplayCommand` — `ONScripter_command.cpp:1381`
- 注册名：`mpegplay`（`ONScripter_lut.cpp:160`）
- 语法：`mpegplay <文件名>, <可点击跳过>`
- 参数：
  - P1 `file`：视频文件名（`readStr()`，用 `saveStringBuffer()` 暂存，`:1383-1384`）。
  - P2 `click_flag`：是否允许点击跳过（`readInt()==1` → true，`:1386`）。**必填**（无逗号判断）。
- 可选参数：无。
- 语义：先 `stopBGM(false)`（`:1388`），再 `playMPEG(file, click)`；**播放失败则 `endCommand()` 直接结束程序**（`:1389`），随后 `repaintCommand()`（`:1391`）。
- 坐标：**否**（文件名 + 标志）。
- 备注：若漏写 P2，`readInt()` 会读到行尾，`parseInt` 返回 0（`ScriptHandler.cpp:1515-1518`），等价于 false。
- 证据：`ONScripter_command.cpp:1381-1394`。

### `msp2` / `msp`
- 处理函数：`mspCommand` — `ONScripter_command.cpp:1337`
- 注册名：`msp2`（`ONScripter_lut.cpp:158`）、`msp`（`ONScripter_lut.cpp:159`）
- 语法：
  - `msp <精灵号>, <dx>, <dy>[, <trans>]`
  - `msp2 <精灵号>, <dx>, <dy>, <scale_x>, <scale_y>, <rot>[, <trans>]`
- 参数：
  - P1 `no`：精灵编号（`readInt()`，`:1344`）；`msp` 用 `sprite_info[no]`，`msp2` 用 `sprite2_info[no]`（`:1346-1353`）。**无越界检查**，越界即越界访问。
  - P2 `dx`：x **增量**，`orig_pos.x += dx`（`:1355`）。整型像素（相对坐标）。
  - P3 `dy`：y **增量**，`orig_pos.y += dy`（`:1356`）。整型像素（相对坐标）。
  - 之后统一 `scalePosXY(screen_ratio1, screen_ratio2)`（`:1357`）——即增量会随后被屏幕缩放比放大。
  - 仅 `msp2`：P4 `scale_x`、P5 `scale_y`：缩放百分比**增量**（`scale_x += ...`，`:1359-1360`；100 = 100%，换算见 `AnimationInfo.cpp:920-923`）。
  - 仅 `msp2`：P6 `rot`：旋转角**增量**，单位为度（`:1361`；按 `cos(-π*rot/180)` 换算，`AnimationInfo.cpp:917-918`），随后 `calcAffineMatrix()`（`:1362`）。
  - 可选末尾 `trans`：仅在检测到逗号时读取（`:1369`）。若当前 `trans == -1`，取 `255 + 值`（支持用 -255…-1 表示 0…254），否则 `trans += 值`，最后钳制到 0–255（`:1369-1376`）。
- 可选参数：`trans`（`END_COMMA` 判断）。
- 坐标：**部分**。
  - P2 `dx`、P3 `dy`：**是**（像素位移，写入 `orig_pos`，`msp2` 另走 `bounding_rect` 脏矩形 `:1348`）。
  - P4 `scale_x`、P5 `scale_y`、P6 `rot`：否（缩放/旋转变换参数）。
  - P1、`trans`：否（编号/透明度）。
- 备注：`msp2` 的 `scale_x`/`scale_y` 为 0 时，旋转绘制路径 `blendOnSurface2` 会直接返回、不绘制（`AnimationInfo.cpp:596-600`），因此 `msp2` 一般需给出非 0 百分比。
- 证据：`ONScripter_command.cpp:1337-1379`；`AnimationInfo.cpp:596-600`、`:910-923`。

### `mul`
- 处理函数：`mulCommand` — `ScriptParser_command.cpp:576`
- 注册名：`mul`（`ONScripter_lut.cpp:157`）
- 语法：`mul <变量>, <乘数>`
- 参数：
  - P1：目标变量。写法为 `readInt()` 读取（`:578`）后 `pushVariable()`（`:579`）——当 P1 写成 `%N`/`?数组` 时，`readInt()` 内部的 `parseInt` 会顺带把 `current_variable` 设为该变量（`ScriptHandler.cpp:1473-1484`），因此 `pushVariable()` 拿到的是正确目标。
  - P2：乘数（`readInt()`，`:581`）。
  - 结果：`P1 = P1值 * P2`（`setInt(&pushed_variable, val1*val2)`，`:582`）。
- 可选参数：无。
- 坐标：**否**。
- 备注：P1 若写成裸数字而非变量引用，`current_variable.var_no` 不会被赋值，行为不确定（**待验证**）。
- 证据：`ScriptParser_command.cpp:576-585`；`ScriptHandler.cpp:1473-1484`。

---

## N

### `nega`
- 处理函数：`negaCommand` — `ONScripter_command.cpp:1309`
- 注册名：`nega`（`ONScripter_lut.cpp:154`）
- 语法：`nega <模式>`
- 参数：
  - P1 `mode`：负片模式（`readInt()` → `nega_mode`，`:1311`）。绘制端在 `nega_mode == 1` 与 `== 2` 时都调用 `makeNegaSurface`（`ONScripter_image.cpp:741/743`、`:777/779`），故有效值实测为 0/1/2；1 与 2 的差异**待验证**。
- 可选参数：无。
- 语义：设置后 `dirty_rect.fill(screen_width, screen_height)` 触发全屏重绘（`:1313`）。
- 坐标：**否**（模式编号；全屏矩形来自 `screen_width/height`，非脚本参数）。
- 证据：`ONScripter_command.cpp:1309-1316`；`ONScripter_image.cpp:741-743`、`:777-779`。

### `next`
- 处理函数：`nextCommand` — `ScriptParser_command.cpp:545`
- 注册名：`next`（`ONScripter_lut.cpp:150`）
- 语法：`next`（无参数）
- 参数：无。循环变量、步长、终值均来自 `for` 建立的 `NestInfo`（`:552-560`）。
- 可选参数：无。
- 语义：必须处于 `for` 循环内，否则 `errorAndExit("next: not in for loop")`（`:547-548`）；未置 `break_flag` 时先 `变量 += step`（`:551-554`），再按步长方向比较终值决定跳出或回跳（`:558-571`）。
- 坐标：**否**。
- 证据：`ScriptParser_command.cpp:545-574`。

### `nextcsel`
- 处理函数：`nextcselCommand` — `ONScripter_command.cpp:1318`
- 注册名：`nextcsel`（`ONScripter_lut.cpp:155`）
- 语法：`nextcsel <值>`（约定写法 `nextcsel 1`）
- 参数：
  - P1：整型占位参数，`readInt()` 读取后**值被丢弃**（`:1320`）；但该调用会把 `current_variable` 更新为 P1 所指变量（若 P1 写 `%N`，见 `ScriptHandler.cpp:1473-1478`），因此 P1 实际上充当「结果变量」。
  - 结果写入：若当前 `NestInfo` 为 `LABEL` 且其后脚本（跳过空白）以 `"csel"` 开头 → 写 1，否则写 0（`:1322-1332`）。
- 可选参数：无。
- 坐标：**否**。
- 备注：P1 的字面含义（占位/兼容参数）在源码中无解释，**待验证**。
- 证据：`ONScripter_command.cpp:1318-1335`；`ScriptHandler.cpp:1473-1478`。

### `sar` / `nsa` / `ns3` / `ns2`
- 处理函数：`nsaCommand` — `ScriptParser_command.cpp:527`
- 注册名：`sar`（`ONScripter_lut.cpp:114`）、`nsa`（`ONScripter_lut.cpp:148`）、`ns3`（`ONScripter_lut.cpp:152`）、`ns2`（`ONScripter_lut.cpp:153`）
- 语法：`sar` / `nsa` / `ns3` / `ns2`（四者均**无参数**，函数体不读取任何 token）
- 参数：无。行为差异完全来自命令名：
  - `ns2` → `nsa_offset = 1`（`:529-531`）
  - `ns3` → `nsa_offset = 2`（`:532-534`）
  - `nsa` / `sar` → 保持既有 `nsa_offset`
- 语义：`delete script_h.cBR` 后新建 `NsaReader(nsa_offset, archive_path, ARCHIVE_TYPE_NSA|ARCHIVE_TYPE_NS2, key_table)` 并 `open(nsa_path)`（`:536-540`）；打开失败仅打印错误、不中止（`:539`）。
- 坐标：**否**。
- 备注：`ONScripter_lut.cpp:151` 另有一条 `{"nsa", &ONScripter::arcCommand}`，位于 `:148` 之后，同字母组内先命中 `:148`，故该条**不可达**（与分册 01 的 `arc/nsa` 说明一致）。
- 证据：`ScriptParser_command.cpp:527-543`；`ONScripter_lut.cpp:114`、`:148`、`:151-153`。

### `nsadir`
- 处理函数：`nsadirCommand` — `ScriptParser_command.cpp:513`
- 注册名：`nsadir`（`ONScripter_lut.cpp:147`）
- 语法：`nsadir <目录名>`
- 参数：
  - P1 `path`：归档目录名（`readStr()`，`:518`），拼成 `RELATIVEPATH + path + DELIMITER` 存入 `nsa_path`（`:521-522`）。
- 可选参数：无。
- 语义：仅限 `define` 段，否则 `errorAndExit("nsadir: not in the define section")`（`:515-516`）；设置后供后续 `nsa`/`sar`/`ns2`/`ns3` 的 `open(nsa_path)` 使用。
- 坐标：**否**（路径）。
- 证据：`ScriptParser_command.cpp:513-525`；`BaseReader.h:47-59`（`DELIMITER`/`RELATIVEPATH` 定义）。

### `numalias`
- 处理函数：`numaliasCommand` — `ScriptParser_command.cpp:499`
- 注册名：`numalias`（`ONScripter_lut.cpp:146`）
- 语法：`numalias <别名>, <编号>`
- 参数：
  - P1：别名标签（`readLabel()`，`:504`），经 `saveStringBuffer()` 取出文本（`:505`）。
  - P2：对应数值（`readInt()`，`:507`），交给 `addNumAlias(别名, 值)`（`:508`）。
- 可选参数：无。
- 语义：仅限 `define` 段（`:501-502`）；登记后脚本中可把该名字当数字使用（`parseInt` 的别名解析见 `ScriptHandler.cpp:1520-1540`）。
- 坐标：**否**。
- 证据：`ScriptParser_command.cpp:499-511`。

---

## O

### `ofscpy` / `ofscopy`
- 处理函数：`ofscopyCommand` — `ONScripter_command.cpp:1297`
- 注册名：`ofscpy`（`ONScripter_lut.cpp:143`）、`ofscopy`（`ONScripter_lut.cpp:144`）
- 语法：`ofscpy` / `ofscopy`（无参数）
- 参数：无（函数体不读取任何 token）。
- 语义：按 `render_view_rect` 尺寸新建 32bit 表面，`SDL_RenderReadPixels` 抓取渲染视图，再 `resizeSurface` 缩放进 `accumulation_surface`（`:1299-1304`）。
- 坐标：**否**（尺寸取自内部 `render_view_rect`，无脚本参数）。
- 证据：`ONScripter_command.cpp:1297-1307`。

---

## P

### `pagetag`
- 处理函数：`pagetagCommand` — `ScriptParser_command.cpp:489`
- 注册名：`pagetag`（`ONScripter_lut.cpp:140`）
- 语法：`pagetag`（无参数）
- 参数：无。仅置 `pagetag_flag = true`（`:494`）。
- 可选参数：无。
- 语义：仅限 `define` 段，否则 `errorAndExit`（`:491-492`）。
- 坐标：**否**。
- 证据：`ScriptParser_command.cpp:489-497`。

### `playonce` / `play`
- 处理函数：`playCommand` — `ONScripter_command.cpp:1264`
- 注册名：`playonce`（`ONScripter_lut.cpp:138`）、`play`（`ONScripter_lut.cpp:139`）
- 语法：
  - `play <文件名>`：播放 MIDI，循环
  - `play *<音轨号>`：播放 CD 音轨，循环
  - `playonce …`：同上但不循环
- 参数：
  - P1：`readStr()`（`:1270`），一个字符串参数，按首字符区分两种形态：
    - 以 `*` 开头：`atoi(P1+1)` 作为 CD 音轨号，写入 `current_cd_track`，`playCDAudio()`；`cd_play_loop_flag = loop_flag`（`:1271-1283`）。
    - 其余：作为 MIDI 文件名，`midi_play_loop_flag = loop_flag`，`playSound(..., SOUND_MIDI, loop)`；失败仅告警（`:1284-1292`）。
  - `loop_flag`：`playonce` 为 false，其余为 true（`:1266-1268`）。
- 可选参数：无。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:1264-1295`。

### `playstop`
- 处理函数：`playstopCommand` — `ONScripter_command.cpp:1257`
- 注册名：`playstop`（`ONScripter_lut.cpp:137`）
- 语法：`playstop`（无参数）
- 参数：无。语义：`stopBGM(false)`（`:1259`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:1257-1262`。

### `pretextgosub`
- 处理函数：`pretextgosubCommand` — `ScriptParser_command.cpp:479`
- 注册名：`pretextgosub`（`ONScripter_lut.cpp:136`）
- 语法：`pretextgosub *<标签>`
- 参数：
  - P1：标签字符串（`readStr()`，`:484`），保存时**跳过首字符**（`+1`，即去掉 `*`）。
- 可选参数：无。
- 语义：仅限 `define` 段（`:481-482`）；标签在文本行首进入时被 gosub（`ONScripter_text.cpp:730-748`），且 `saveon_flag` 参与判定（`:457`、`:684`）。
- 坐标：**否**。
- 证据：`ScriptParser_command.cpp:479-487`。

### `print`
- 处理函数：`printCommand` — `ONScripter_command.cpp:1246`
- 注册名：`print`（`ONScripter_lut.cpp:135`）
- 语法：`print <效果号>[, <时长>[, <遮罩图>]]`
- 参数（由 `parseEffect(true)` → `readEffect` 解析，`ScriptParser.cpp:664-687`）：
  - P1 `effect`：效果编号（`readInt()`，`:668`）。逻辑范围 0–255（`:680-683`）；`0` 表示无操作（`setEffect` 直接返回 true，`ONScripter_effect.cpp:50`）。
  - P2 `duration`：效果时长，毫秒（`readInt()`，`:669-671`），可选。
  - P3 `mask`：遮罩图片文件名（`readStr()`，`:672-676`），可选；用于 15/18 号效果（`ONScripter_effect.cpp:62-67`）。
- 可选参数：P2、P3（`END_COMMA` 判断）。
- 语义：先 `leaveTextDisplayMode()`（`:1248`）；`parseEffect` 解析效果，`setEffect` 启动后循环 `doEffect` 直至结束（`:1250-1252`）。
- 坐标：**否**（编号/时间/文件名）。
- 备注：
  - 只给 P1、且 P1 ≥ 2 时，该编号必须已由 `define` 段的 `effect` 命令登记，否则 `parseEffect` 打印错误并 `exit(-1)`（`ScriptParser.cpp:696-705`；`effect` 的登记范围 2–255 见 `ScriptParser_command.cpp:1244-1248`）。
  - 只给 P1 且越界（<0 或 >255）时会被改为 0 并告警（`ScriptParser.cpp:680-683`）；给了 P2 时该范围检查被跳过（`num > 1` 直接返回，`:695`）。
  - 本命令**不接受文本参数**（文本显示由 `puttext` 等负责）。
- 证据：`ONScripter_command.cpp:1246-1255`；`ScriptParser.cpp:664-708`；`ONScripter_effect.cpp:48-67`。

### `prnum`
- 处理函数：`prnumCommand` — `ONScripter_command.cpp:1199`
- 注册名：`prnum`（`ONScripter_lut.cpp:134`）
- 语法：`prnum <编号>, <数值>, <x>, <y>, <字号x>, <字号y>, <颜色>`
- 参数：
  - P1 `no`：prnum 槽位编号（`readInt()`，`:1203`），有效范围 `0 … MAX_PARAM_NUM-1`（= 0–99，`ONScripter.h:61`、`:478`）。越界时仍会读完 P2–P7 后返回（`:1204-1212`）。
  - P2 `param`：要显示的数字（`readInt()` → `ai->param`，`:1224`）；渲染时按 3 列宽度转字符串（`getStringFromInteger(num_buf, param, 3)`，`:1236-1237`），超出 3 位时丢弃高位（`ScriptHandler.cpp:802-808`）。
  - P3 `x`：左上角 x（`readInt()` → `orig_pos.x`，`:1225`），整型像素，**绝对坐标**。
  - P4 `y`：左上角 y（`readInt()` → `orig_pos.y`，`:1226`），整型像素，**绝对坐标**。
  - P5 `font_size_x`：字号 x（`readInt()` → `font_size_xy[0]`，`:1228`），像素；同时复制到 `font_pitch[0]`（`:1230`）。
  - P6 `font_size_y`：字号 y（`readInt()` → `font_size_xy[1]`，`:1229`），像素；复制到 `font_pitch[1]`（`:1231`）。
  - P7 `color`：颜色字符串（`readStr()`，`:1233`），交 `readColor` 解析，通常为 `#RRGGBB`（`:1234`）。
- 可选参数：**全部必填**（越界分支也按 5 个 int + 1 个 str 读完）。
- 坐标：**部分**。
  - P3 `x`、P4 `y`：**是**（绝对像素坐标，且随后 `scalePosXY(screen_ratio1, screen_ratio2)` 缩放，`:1227`）。
  - P5 `font_size_x`、P6 `font_size_y`：**是**（按判定规则 `font_size_xy`/`font_pitch` 属坐标类；源码未见 `screen_ratio` 乘法，`:1228-1231`，是否需 ×2 **待验证**）。
  - P1、P2、P7：否（编号/数值/颜色）。
- 备注：重复设置同一槽位会先删除旧对象并把旧 `pos` 加入脏区（`:1214-1217`）；字号 0/负值的实际表现源码未校验，**待验证**。
- 证据：`ONScripter_command.cpp:1199-1244`；`ONScripter.h:61`、`:478`；`ScriptHandler.cpp:788-808`。

### `prnumclear`
- 处理函数：`prnumclearCommand` — `ONScripter_command.cpp:1187`
- 注册名：`prnumclear`（`ONScripter_lut.cpp:133`）
- 语法：`prnumclear`（无参数）
- 参数：无。语义：遍历 `prnum_info[0..MAX_PARAM_NUM-1]`，把存在项的 `pos` 加入脏区并删除（`:1189-1195`）。
- 坐标：**否**（清理操作，无参数）。
- 证据：`ONScripter_command.cpp:1187-1197`。

### `puttext`
- 处理函数：`puttextCommand` — `ONScripter_command.cpp:1170`
- 注册名：`puttext`（`ONScripter_lut.cpp:132`）
- 语法：`puttext <文本>`
- 参数：
  - P1：要插入文本缓冲的字符串（`readStr()`，`:1174`）。若读到的 token 带 1 字节字符标记（`END_1BYTE_CHAR`），`string_buffer_offset = 1` 以跳过行首的反引号 `` ` ``（`:1176-1178`）。
- 可选参数：无。
- 语义：`enterTextDisplayMode(false)` 后逐字符 `processText()` 处理，并暂存/恢复 `line_enter_status`（`:1172`、`:1180-1182`）。
- 坐标：**否**（无坐标参数；文本位置沿用 `sentence_font`）。
- 证据：`ONScripter_command.cpp:1170-1185`。

---

## Q

### `quakey` / `quakex` / `quake`
- 处理函数：`quakeCommand` — `ONScripter_command.cpp:1142`
- 注册名：`quakey`（`ONScripter_lut.cpp:128`）、`quakex`（`:129`）、`quake`（`:130`）
- 语法：`quake <振幅>, <时长>`
- 参数：
  - P1 `no`：振幅（`readInt()` → `tmp_effect.no`，`:1156`）。整型；0 时 `quakey`/`quakex` 会在 `effect_duration/4/effect->no` 处**除零**（`ONScripter_effect.cpp:337-338`、`:357-358`），故应 ≥ 1。
  - P2 `duration`：持续时间，毫秒（`readInt()` → `tmp_effect.duration`，`:1157`）；若 `duration < no*4` 则被抬到 `no*4`（`:1158`），因此给 0 也会得到 `no*4`。
- 可选参数：无。
- 语义：按命令名设 `quake_type`（`quakey`=0、`quakex`=1、`quake`=2，`:1146-1154`），效果号记为 `MAX_EFFECT_NUM + quake_type`（`:1159`，`MAX_EFFECT_NUM`=256 见 `ONScripter.h:62`），全屏脏区并复制当前画面后执行效果（`:1161-1165`）。
- 坐标：**部分**。
  - P1 `no`（振幅）：**是**。`quakex`/`quakey` 的位移为 `EFFECT_QUAKE_AMP * no * sin(...) * (duration-counter)/duration`，其中 `EFFECT_QUAKE_AMP = 12 * screen_ratio1 / screen_ratio2`（`ONScripter_effect.cpp:30`、`:339-340`、`:359-360`），写进 `dst_rect.x/y` 并按 `screen_width/height` 补黑边（`:341-353`、`:361-373`）；`quake`（双轴）为 `±2*no` 原始像素（`:377-378`）。三种形态都与像素/屏幕尺寸运算相关。
  - P2 `duration`：否（时间）。
- 证据：`ONScripter_command.cpp:1142-1168`；`ONScripter_effect.cpp:30`、`:336-381`；`ONScripter.h:62`。

---

## R

### `repaint`
- 处理函数：`repaintCommand` — `ONScripter_command.cpp:1134`
- 注册名：`repaint`（`ONScripter_lut.cpp:126`）
- 语法：`repaint`（无参数）
- 参数：无。语义：全屏脏区后 `flush(refreshMode())`（`:1136-1137`）。
- 坐标：**否**（无参数；`screen_width/height` 只是脏区填充）。
- 证据：`ONScripter_command.cpp:1134-1140`。

### `reset`
- 处理函数：`resetCommand` — `ONScripter_command.cpp:1113`
- 注册名：`reset`（`ONScripter_lut.cpp:125`）
- 语法：`reset`（无参数）
- 参数：无。
- 语义：临时把 `mp3fadeout_duration` 置 0 后 `resetSub()`，再恢复（`:1115-1118`）；页指针回到首页并清页、`flush(..., &sentence_font_info.pos)`（`:1120-1122`）；重置局部变量（`i < global_variable_border`，`:1124-1126`）；跳转标签 `"start"` 并 `storeSaveFile()` 自动存档（`:1128-1129`）。
- 坐标：**否**（无参数；`:1122` 的 `sentence_font_info.pos` 来自内部状态）。
- 证据：`ONScripter_command.cpp:1113-1132`。

### `resettimer`
- 处理函数：`resettimerCommand` — `ONScripter_command.cpp:1106`
- 注册名：`resettimer`（`ONScripter_lut.cpp:124`）
- 语法：`resettimer`（无参数）
- 参数：无。语义：`internal_timer = SDL_GetTicks()`（`:1108`）。
- 坐标：**否**（时间基准）。
- 证据：`ONScripter_command.cpp:1106-1111`。

### `return`
- 处理函数：`returnCommand` — `ScriptParser_command.cpp:449`
- 注册名：`return`（`ONScripter_lut.cpp:123`）
- 语法：`return [*标签]`
- 参数：
  - P1（可选）：`readStr()` 读到的标签串（`:457`）。若首字符不是 `*` → 回到 gosub 保存的位置（`:458-459`）；若以 `*` 开头 → 跳转到 `P1+1` 指定的标签（`:460-461`）。
- 可选参数：P1（缺省时读到行尾，首字符非 `*` 即普通返回）。
- 语义：必须在 gosub 上下文，否则 `errorAndExit("return: not in gosub")`（`:451-452`）；随后弹栈（`:465-467`）；若该层带 `textgosub_flag` 且点击状态为 `CLICK_NEWPAGE|CLICK_EOL`，重置 `line_enter_status`/`page_enter_status`（`:470-474`）。
- 坐标：**否**。
- 证据：`ScriptParser_command.cpp:449-477`。

### `rmenu`
- 处理函数：`rmenuCommand` — `ScriptParser_command.cpp:424`
- 注册名：`rmenu`（`ONScripter_lut.cpp:122`）
- 语法：`rmenu <显示文本1>, <系统调用号1>[, <显示文本2>, <系统调用号2> …]`
- 参数（成对出现，循环直到不再有逗号，`:430-444`）：
  - P(2k-1)：菜单显示文本（`readStr()`，`:433`）。
  - P(2k)：系统调用标签（`readLabel()`），`getSystemCallNo(...)` 转成调用号（`:438`）。
  - 每读一项，`rmenu_link_width` 取 `strlen(文本)/2 + 1` 的最大值（`:435-436`）——单位是**字符数**（半角计），不是像素。
- 可选参数：第 2 对及以后（由 `END_COMMA` 决定，`:443`）。
- 语义：先 `deleteRMenuLink()` 清空旧菜单再重建（`:426-431`）。
- 坐标：**否**（无像素坐标/尺寸参数；宽度为字符数）。
- 证据：`ScriptParser_command.cpp:424-447`。

### `rmode`
- 处理函数：`rmodeCommand` — `ONScripter_command.cpp:1098`
- 注册名：`rmode`（`ONScripter_lut.cpp:121`）
- 语法：`rmode <0|1>`
- 参数：
  - P1：`readInt()==1` → `rmode_flag = true`，否则 false（`:1100-1101`）。
- 可选参数：无。
- 坐标：**否**（标志）。
- 证据：`ONScripter_command.cpp:1098-1104`。

### `rnd2` / `rnd`
- 处理函数：`rndCommand` — `ONScripter_command.cpp:1074`
- 注册名：`rnd2`（`ONScripter_lut.cpp:119`）、`rnd`（`:120`）
- 语法：
  - `rnd <变量>, <上限>` → 结果 ∈ [0, 上限-1]
  - `rnd2 <变量>, <下限>, <上限>` → 结果 ∈ [下限, 上限]
- 参数：
  - P1：结果变量。`readInt()`（`:1079`/`:1086`）后 `pushVariable()`（`:1080`/`:1087`）——P1 写 `%N` 时 `readInt` 会把 `current_variable` 指向该变量（`ScriptHandler.cpp:1473-1478`）。
  - `rnd`：P2 = 上限，内部取 `upper = P2-1`、`lower = 0`（`:1089-1090`）。
  - `rnd2`：P2 = 下限 `lower`、P3 = 上限 `upper`（`:1082-1083`）。
  - 计算式：`lower + (int)((upper-lower+1)*rand()/(RAND_MAX+1.0))`（`:1093`）。
- 可选参数：无。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:1074-1096`；`ScriptHandler.cpp:1473-1478`。

### `roff`
- 处理函数：`roffCommand` — `ScriptParser_command.cpp:415`
- 注册名：`roff`（`ONScripter_lut.cpp:118`）
- 语法：`roff`（无参数）
- 参数：无。语义：仅限 `define` 段（`:417-418`），置 `rmode_flag = false`（`:419`）。
- 坐标：**否**。
- 证据：`ScriptParser_command.cpp:415-422`。

### `rubyoff`
- 处理函数：`rubyoffCommand` — `ScriptParser_command.cpp:408`
- 注册名：`rubyoff`（`ONScripter_lut.cpp:117`）
- 语法：`rubyoff`（无参数）
- 参数：无。语义：`sentence_font.rubyon_flag = false`（`:410`）；不要求 `define` 段。
- 坐标：**否**。
- 证据：`ScriptParser_command.cpp:408-413`。

### `rubyon`
- 处理函数：`rubyonCommand` — `ScriptParser_command.cpp:383`
- 注册名：`rubyon`（`ONScripter_lut.cpp:116`）
- 语法：`rubyon [<字号x>, <字号y>[, <字体名>]]`
- 参数：
  - P1 `font_size_x`：注音字号 x（`readInt()` → `ruby_struct.font_size_xy[0]`，`:394`）。
  - P2 `font_size_y`：注音字号 y（`readInt()` → `ruby_struct.font_size_xy[1]`，`:395`）。
  - P3（可选）：字体名（`readStr()` → `ruby_struct.font_name`，`:397-402`）。
  - 若命令后紧跟换行/`:`/`;`，则 P1/P2 置 -1（表示沿用默认字号）、字体名置 NULL（`:387-392`）。
- 可选参数：全部可选（`getNext()` 首字符判断 + `END_COMMA`）。
- 语义：置 `sentence_font.rubyon_flag = true`（`:385`）；后续文本中的注音按该字号/字体渲染（`ONScripter_text.cpp:633-638`），并被存档（`ONScripter_file2.cpp:355-359`、`:641-644`）。
- 坐标：**部分**。
  - P1/P2：**是**（按判定规则 `font_size_xy` 属坐标类）。源码仅原样存入 `ruby_struct.font_size_xy`，未见 `screen_ratio` 乘法（`:394-395`），是否需参与 ×2 换算**待验证**。
  - P3：否（字体名）。
- 证据：`ScriptParser_command.cpp:383-406`；`ONScripter_text.cpp:633-638`；`ONScripter_file2.cpp:355-359`。

---

## S

### `savedir`
- 处理函数：`savedirCommand` — `ScriptParser_command.cpp:349`
- 注册名：`savedir`（`ONScripter_lut.cpp:113`）
- 语法：`savedir <相对目录>`
- 参数：
  - P1 `path`：存档目录（`readStr()`，`:354`），拼成 `archive_path + path + DELIMITER`（`:358-359`）。
- 可选参数：无。
- 语义：仅限 `define` 段（`:351-352`）；**仅当 `save_dir` 尚未设置时**才生效（避免覆盖命令行 `--save-dir`，`:356`）；在 Linux/macOS/iOS/Android 下 `stat` 校验目录存在，不存在则打印 `doesn't exist` 并放弃设置（`:361-374`）；成功后 `script_h.setSaveDir(save_dir)` 并记录 `save_dir_envdata`（`:376-377`）。
- 坐标：**否**（路径）。
- 证据：`ScriptParser_command.cpp:349-381`。

### `savefileexist`
- 处理函数：`savefileexistCommand` — `ONScripter_command.cpp:1060`
- 注册名：`savefileexist`（`ONScripter_lut.cpp:112`）
- 语法：`savefileexist <结果变量>, <存档号>`
- 参数：
  - P1：结果变量。`readInt()`（`:1062`）后 `pushVariable()`（`:1063`）；P1 写 `%N` 时 `readInt` 会把 `current_variable` 指向它（`ScriptHandler.cpp:1473-1478`）。
  - P2 `no`：存档号（`readInt()`，`:1064`），交 `searchSaveFile(info, no)`（`:1067`）。
  - 结果：存在写 1，否则 0（`:1069`）。
- 可选参数：无。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:1060-1072`。

### `savegame2` / `savegame`
- 处理函数：`savegameCommand` — `ONScripter_command.cpp:1042`
- 注册名：`savegame2`（`ONScripter_lut.cpp:110`）、`savegame`（`:111`）
- 语法：
  - `savegame <存档号>`
  - `savegame2 <存档号>, <备注字符串>`
- 参数：
  - P1 `no`：存档号（`readInt()`，`:1048`）。
  - P2 `savestr`：仅 `savegame2` 读取的备注串（`readStr()`，`:1050-1052`），其余情况传 NULL。
- 可选参数：P2（仅 `savegame2`）。
- 语义：若 `saveon_flag && internal_saveon_flag` 先 `storeSaveFile()`（写自动存档），再 `writeSaveFile(no, savestr)`（`:1054-1055`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:1042-1058`。

### `savename`
- 处理函数：`savenameCommand` — `ScriptParser_command.cpp:335`
- 注册名：`savename`（`ONScripter_lut.cpp:109`）
- 语法：`savename <存档菜单名>, <读档菜单名>, <存档项名>`
- 参数（三个字符串，顺序固定）：
  - P1 → `save_menu_name`（`:337-338`）
  - P2 → `load_menu_name`（`:340-341`）
  - P3 → `save_item_name`（`:343-344`）
- 可选参数：无（三个 `readStr()` 均无条件读取）。
- 语义：仅设置存档/读档界面显示用文本；`P1` 用于存档菜单标题（`ONScripter_rmenu.cpp:412-414`）。默认值见 `ScriptParser.cpp:174`。
- 坐标：**否**。
- 证据：`ScriptParser_command.cpp:335-347`；`ONScripter_rmenu.cpp:409-414`。

### `savenumber`
- 处理函数：`savenumberCommand` — `ScriptParser_command.cpp:328`
- 注册名：`savenumber`（`ONScripter_lut.cpp:108`）
- 语法：`savenumber <存档位数>`
- 参数：
  - P1：存档数量（`readInt()` → `num_save_file`，`:330`），类型 `unsigned int`（`ScriptParser.h:343`）。默认 9（`ScriptParser.cpp:177`）。
- 可选参数：无。
- 语义：决定存档菜单条目数与存档文件名位数——`num_save_file >= 10` 时用 2 位编号，否则 1 位（`ONScripter_file.cpp:60`）；并影响菜单行数（`ONScripter_rmenu.cpp:300`、`:409`）。
- 坐标：**否**（编号/数量）。
- 证据：`ScriptParser_command.cpp:328-333`；`ONScripter_file.cpp:60`；`ONScripter_rmenu.cpp:300`。

### `saveoff`
- 处理函数：`saveoffCommand` — `ONScripter_command.cpp:1031`
- 注册名：`saveoff`（`ONScripter_lut.cpp:107`）
- 语法：`saveoff`（无参数）
- 参数：无。语义：`autosaveoff_flag` 为假时，若当前在自动存档状态则先 `storeSaveFile()`，再置 `saveon_flag = false`（`:1033-1037`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:1031-1040`。

### `saveon`
- 处理函数：`saveonCommand` — `ONScripter_command.cpp:1023`
- 注册名：`saveon`（`ONScripter_lut.cpp:106`）
- 语法：`saveon`（无参数）
- 参数：无。语义：`autosaveoff_flag` 为假时置 `saveon_flag = true`（`:1025-1026`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:1023-1029`。

### `savepoint`
- 处理函数：`savepointCommand` — `ONScripter_command.cpp:1016`
- 注册名：`savepoint`（`ONScripter_lut.cpp:105`）
- 语法：`savepoint`（无参数）
- 参数：无。语义：立即 `storeSaveFile()`（写自动存档，`:1018`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:1016-1021`。

### `savescreenshot2` / `savescreenshot`
- 处理函数：`savescreenshotCommand` — `ONScripter_command.cpp:968`
- 注册名：`savescreenshot2`（`ONScripter_lut.cpp:103`）、`savescreenshot`（`:104`）
- 语法：`savescreenshot <文件名>` / `savescreenshot2 <文件名>`
- 参数：
  - P1 `filename`：输出 BMP 文件名（`readStr()`，`:982`）；反斜杠统一替换为 `/`（`:987`），再按 `save_dir` / `archive_path` 组合出实际路径（`:989-999`）。
- 可选参数：无。
- 语义：按 `screenshot_w × screenshot_h` 分配表面并 `resizeSurface`（`:975-980`），随后 `SDL_SaveBMP_RW` 保存；失败仅打印错误（`:1009-1010`）。
- 坐标：**否**（无坐标参数；截图尺寸来自 `getscreenshot` 设置的 `screenshot_w/h`，`ONScripter_command.cpp:2342-2343`，默认 `screen_width/height` 见 `ONScripter.cpp:550-551`）。
- 备注：`delete_flag` 变量被赋值（`:970-973`）但函数体内**从未使用**，故两别名当前行为相同；其原意（是否删除临时表面）**待验证**。
- 证据：`ONScripter_command.cpp:968-1014`；`ONScripter.cpp:550-551`。

### `savetime`
- 处理函数：`savetimeCommand` — `ONScripter_command.cpp:942`
- 注册名：`savetime`（`ONScripter_lut.cpp:102`）
- 语法：`savetime <存档号>, <月变量>, <日变量>, <时变量>, <分变量>`
- 参数：
  - P1 `no`：存档号（`readInt()`，`:944`），交 `searchSaveFile`（`:947`）。
  - P2–P5：四个整型结果变量，依次写入 月/日/时/分：
    - P2：`readVariable()` 后 `setInt(current_variable, info.month)`（`:949`、`:957`）。
    - P3–P5：用 `readInt()` 读取（`:958`、`:960`、`:962`），对 `%N` 同样会设置 `current_variable`（`ScriptHandler.cpp:1473-1478`），随后分别写入 `day`/`hour`/`minute`（`:959`、`:961`、`:963`）。
- 可选参数：无。
- 语义：存档无效时把 P2–P5 全部写 0（`:950-955`）。
- 坐标：**否**（时间值）。
- 备注：P2 用 `readVariable()`、P3–P5 用 `readInt()`，写法不一致但结果等价（两者都会设置 `current_variable`）；仅为记录源码事实。
- 证据：`ONScripter_command.cpp:942-966`；`ScriptHandler.cpp:384-401`、`:722-758`、`:1473-1478`。

### `selnum` / `selgosub` / `select` / `csel`
- 处理函数：`selectCommand` — `ONScripter_command.cpp:777`
- 注册名：`selnum`（`ONScripter_lut.cpp:96`）、`selgosub`（`:97`）、`select`（`:101`）、`csel`（`:340`）
- 语法：
  - `select <文本1>, <标签1>[, <文本2>, <标签2> …]`（SELECT_GOTO_MODE）
  - `selgosub <文本1>, <标签1>[, <文本2>, <标签2> …]`（SELECT_GOSUB_MODE）
  - `selnum <结果变量>[, <文本1>[, <文本2> …]]`（SELECT_NUM_MODE）
  - `csel <文本1>, <标签1>[, …]`（SELECT_CSEL_MODE）
- 参数：
  - 模式由命令名决定：`selnum`→NUM、`selgosub`→GOSUB、`select`→GOTO、`csel`→CSEL（`:784-791`）。
  - `selnum` 专有 P1：结果变量（`readVariable()` + `pushVariable()`，`:793-796`）；选项从 P2 开始。
  - 其余三种没有变量参数，从 P1 起就是「文本, 标签」成对序列：文本 `readStr()`（`:818`），标签 `readStr()` 且跳过首字符（`getStringBuffer()+1`，`:830-831`）。
  - 逗号语义：非 `selnum` 模式下文本与标签之间必须有逗号，否则 `errorAndExit("select: missing comma.")`（`:819-821`）；行首出现重复逗号报 `"select: double comma."`（`:845-846`）。
- 可选参数：第 2 组及以后的「文本, 标签」；选择项读到行尾/行中结束（`:840-874`）。`selnum` 的 P2 必填（`comma_flag` 初值为 true，`:798`）。
- 语义：
  - `enterTextDisplayMode()`（`:779`）；`selnum` 时先把结果变量压栈。
  - `csel`：先按当前存档状态 `storeSaveFile()` 并置 `saveon_flag = false`（`:799-802`），读完选项后直接 `setCurrentLabel("customsel")` 返回（`:891-893`）——即交由脚本自定义选择界面。
  - 其余模式：把每个非空文本做成可选按钮（`getSelectableSentence`，`:877-889`），进入 `WAIT_TEXT_MODE|WAIT_BUTTON_MODE|WAIT_TIMER_MODE` 等待点击（`:903-908`）。
  - 结果分派：GOTO → 跳到所选标签；GOSUB → `gosubReal`；NUM → 把 `按钮号-1` 写入结果变量并继续执行（`:923-934`）。
  - 结束时 `deleteSelectLink()` 与 `newPage()`（`:935-937`）。
- 坐标：**否**（无任何数值坐标/尺寸参数；选项位置沿用 `sentence_font.xy`，`:805-807`、`:896-897`）。
- 备注：本组命令**不提供** x/y/宽高参数，若需自定义坐标布局应使用 `csel` + 自定义界面命令。
- 证据：`ONScripter_command.cpp:777-940`。

---

## 本册统计

- 处理函数：**44 个**
- 命令条目：**62 条**（含别名与重复注册；`nsa` 在 `ONScripter_lut.cpp:151` 的重复注册不可达）
- 含坐标参数的命令（判定为**部分**，即并非全部参数都是坐标）：
  1. `msp2` / `msp` —— P2 `dx`、P3 `dy` 为相对像素坐标（另 `scale`/`rot` 非坐标）
  2. `prnum` —— P3 `x`、P4 `y` 为绝对像素坐标，P5/P6 字号属 `font_size_xy` 坐标类
  3. `quakey` / `quakex` / `quake` —— P1 振幅参与像素位移与 `screen_width/height` 运算（P2 时长非坐标）
  4. `rubyon` —— P1/P2 为 `font_size_xy` 字号（P3 字体名非坐标）
- 判定为**是**（全部参数皆坐标）：无
- 判定为**否**：其余 40 个处理函数（含 `print`、`puttext`、`repaint`、`reset`、`resettimer`、`select` 系列、`save*` 系列、`*fade*`/`mp3vol` 等时间/音量/编号/标志/路径类）
- 未定位：**0**

#### 待验证清单（源码不足以确证，不含推测）
1. `mp3fadein` / `mp3fadeout` 传入负值后写入 `Uint32` 的实际行为（`ONScripter_command.cpp:1448`、`:1441` + `ONScripter.h:727-728`）。
2. `rubyon` 的 `font_size_xy` 是否需参与 ×2 换算（`ScriptParser_command.cpp:394-395` 无 `screen_ratio` 运算）。
3. `prnum` 字号为 0/负值时的渲染结果（`ONScripter_command.cpp:1228-1231` 未校验）。
4. `nextcsel` 的 P1 参数名义（仅被读取后丢弃，`ONScripter_command.cpp:1320`）。
5. `savescreenshot2` 的 `delete_flag` 设计意图（`ONScripter_command.cpp:970-973` 赋值后未使用）。
6. `nega` 的模式 1 与模式 2 差异（两者均调用 `makeNegaSurface`，`ONScripter_image.cpp:741/743`）。
