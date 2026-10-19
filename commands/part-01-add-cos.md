# 命令分册 01：处理函数 `addCommand` – `cosCommand`

> 本册覆盖 `func_lut` 中处理函数位于 `addCommand`…`cosCommand` 区间的全部命令（含别名）。
> 行号均指快照 `src/onsyuri/`。参数按**源码读取顺序**列出，逗号分隔；`[ ]` 表示可选（由 `script_h.getEndStatus() & END_COMMA` 判断）。
> 「坐标」列：**是** = 该命令含逻辑坐标/尺寸参数，需在 ×2 转换中处理；**否** = 不含；**部分** = 仅部分参数是坐标。
> 判定依据：参数是否直接写入 `orig_pos.x/y/w/h`、`top_xy`、`num_xy`、`font_size_xy`、`pitch_xy`、`image_rect`、`max_width`、`dst_rect/src_rect` 等坐标字段，或与 `screen_width/height` 做像素运算。

---

## A

### `add`
- 处理函数：`addCommand` — `ScriptParser_command.cpp:1493`
- 语法：`add <变量>, <值>`
- 参数：
  - P1：目标变量（`readVariable()`，支持 `%` 整型/数组、`$` 字符串）。
  - P2：加数。整型/数组变量走 `readInt()` 做数值加法；字符串变量走 `readStr()` 做**字符串追加**（`strcat`）。
  - 只读一个 P2；类型由 P1 决定。
- 坐标：否。备注：字符串模式下是连接而非加法（`:1503-1528`）。

### `addkinsoku`
- 处理函数：`addkinsokuCommand` — `ScriptParser_command.cpp:1480`
- 语法：`addkinsoku <起始字符串>, <结束字符串>`
- 参数：P1/P2 均为字符串（`readStr()`），P1 用 `saveStringBuffer()` 暂存后作为起始串，P2 作为结束串，交给 `setKinsoku(start,end,true)`。
- 坐标：否。备注：仅允许在 `define` 段使用（`:1482-1483`）。

### `allsp2hide`
- 处理函数：`allsp2hideCommand` — `ONScripter_command.cpp:3960`
- 参数：无。
- 语义：置 `all_sprite2_hide_flag = true`，并把所有可见 `sprite2_info` 的 `bounding_rect` 加入脏矩形。
- 坐标：否（内部涉及矩形，但无脚本参数）。

### `allsp2resume`
- 处理函数：`allsp2resumeCommand` — `ONScripter_command.cpp:3929`
- 参数：无。语义：`all_sprite2_hide_flag = false`，恢复 sprite2 显示。
- 坐标：否。

### `allsphide`
- 处理函数：`allsphideCommand` — `ONScripter_command.cpp:3972`
- 参数：无。语义：`all_sprite_hide_flag = true`，隐藏立绘（`tachi_info[0..2]`）与全部 `sprite_info`。
- 坐标：否。

### `allspresume`
- 处理函数：`allspresumeCommand` — `ONScripter_command.cpp:3941`
- 参数：无。语义：`all_sprite_hide_flag = false`，恢复显示。
- 坐标：否。

### `amsp` / `amsp2`
- 处理函数：`amspCommand` — `ONScripter_command.cpp:3888`
- 语法：
  - `amsp <精灵号>, <x>, <y>[, <trans>]`
  - `amsp2 <精灵号>, <x>, <y>, <scale_x>, <scale_y>, <rot>[, <trans>]`
- 参数：
  - P1：精灵编号（`readInt()`；`amsp` 用 `sprite_info`，`amsp2` 用 `sprite2_info`）。
  - P2：目标 x（`orig_pos.x`）。
  - P3：目标 y（`orig_pos.y`）。
  - `amsp2` 追加：P4=`scale_x`（缩放倍率，`ai->scale_x`）、P5=`scale_y`、P6=`rot`（旋转角，度），随后 `calcAffineMatrix()`。
  - 可选末尾：`trans`（0–255，超出被钳制）。
- 坐标：**是**（P2/P3；`amsp2` 的 scale/rot 不是坐标，但属于变换参数）。
- 证据：`ONScripter_command.cpp:119-148`；`amsp2` 分支 `:133-139`。

### `arc` / `nsa`
- 处理函数：`arcCommand` — `ScriptParser_command.cpp:1451`
- 语法：`arc <归档名>[|<其它>]`（`nsa` 同义）
- 参数：P1 字符串（`readStr()`），按 `|` 截断，只取第一段作为归档文件名。
- 语义：若当前 reader 是 `direct`，切换为 `SarReader` 并 `open()`；若已是 `sar`，直接 `open()` 下一个归档。失败仅警告不中止。
- 坐标：否。
- 备注：`{"nsa", &ONScripter::arcCommand}`（`ONScripter_lut.cpp:151`）**不可达**，因为同字母组内 `{"nsa", &ONScripter::nsaCommand}`（`:148`）先命中；见 6.4 说明。

### `atoi`
- 处理函数：`atoiCommand` — `ScriptParser_command.cpp:1439`
- 语法：`atoi <结果变量>, <字符串>`
- 参数：P1 目标整型变量（`readInt()` 取值后 `pushVariable()` 定位——注意：这里用 `readInt` 读的是「变量编号」）；P2 字符串（`readStr()`）。
- 语义：`atoi(P2)` 写入 P1。
- 坐标：否。

### `autoclick`
- 处理函数：`autoclickCommand` — `ONScripter_command.cpp:3881`
- 语法：`autoclick <毫秒>`
- 参数：P1 自动点击间隔（ms），写入 `autoclick_time`。
- 坐标：否（时间）。

### `automode_time`
- 处理函数：`automode_timeCommand` — `ONScripter_command.cpp:3874`
- 语法：`automode_time <毫秒>`
- 参数：P1 自动模式等待时间（ms），写入 `automode_time`；负值表示按字数计算（见 `btnwait` 中 `-automode_time * num_chars_in_sentence`）。
- 坐标：否。

### `autosaveoff`
- 处理函数：`autosaveoffCommand` — `ScriptParser_command.cpp:1429`
- 参数：无。语义：`autosaveoff_flag = true`；仅限 `define` 段。
- 坐标：否。

### `avi`
- 处理函数：`aviCommand` — `ONScripter_command.cpp:3858`
- 语法：`avi <文件名>[, <click_flag>]`
- 参数：P1 视频文件名（`readStr()`）；P2 是否可点击跳过（`readInt()==1`，缺省 0/false）。
- 语义：先 `stopBGM`，再 `playAVI(file, click)`；播放失败则 `endCommand()`。
- 坐标：否。

## B

### `bar`
- 处理函数：`barCommand` — `ONScripter_command.cpp:3816`
- 语法：`bar <编号>, <当前值>, <x>, <y>, <最大宽度>, <高度>, <最大值>, <颜色>`
- 参数：
  - P1 `no`：进度条编号（`bar_info[no]`）。
  - P2 `param`：当前值（`ai->param`）。
  - P3 `x`、P4 `y`：左上角（`orig_pos.x/y`）。
  - P5 `max_width`：最大宽度（像素）。
  - P6 `height`：高度（像素）。
  - P7 `max_param`：最大值；宽度按 `max_width*param/max_param` 计算（`:265-267`）。
  - P8 颜色字符串：`#RRGGBB`（`readColor`）。
- 坐标：**是**（P3/P4/P5/P6）。

### `barclear`
- 处理函数：`barclearCommand` — `ONScripter_command.cpp:3804`
- 参数：无。语义：删除全部 `bar_info[]` 并加入脏矩形。
- 坐标：否。

### `bdown`
- 处理函数：`bdownCommand` — `ONScripter_command.cpp:3797`
- 参数：无。语义：`btndown_flag = true`（按钮按下状态）。
- 坐标：否。

### `bg`
- 处理函数：`bgCommand` — `ONScripter_command.cpp:3764`
- 语法：`bg <文件名>`，或 `bg white` / `bg black`
- 参数：P1 文件名（`readStr()`）；特例 `white`/`black` 用 `compareString`+`readLabel` 消费，不当作文件。
- 语义：清空立绘与旧背景，设置 `bg_info.file_name`，`createBackground()`，再解析可选特效（`parseEffect`）。
- 坐标：否（背景图自动铺满 `screen_width/height`，见 `:3788`）。

### `bgcopy` / `bgcpy`
- 处理函数：`bgcopyCommand` — `ONScripter_command.cpp:3750`
- 参数：无（内部调用 `ofscopyCommand()`）。
- 语义：把当前累积画面复制为背景，文件名标记 `*bgcpy`。
- 坐标：否。

### `blt`
- 处理函数：`bltCommand` — `ONScripter_command.cpp:3665`
- 语法：`blt <dx>,<dy>,<dw>,<dh>,<sx>,<sy>,<sw>,<sh>`
- 参数（全部为像素，且都被 `* screen_ratio1 / screen_ratio2` 处理，本分支恒等）：
  - P1 `dx`、P2 `dy`：目标左上角。
  - P3 `dw`、P4 `dh`：目标宽高。
  - P5 `sx`、P6 `sy`：源图左上角。
  - P7 `sw`、P8 `sh`：源图宽高。
  - 任一边为 0 则直接返回（`:359`）。
- 坐标：**是**（8 个参数全是）。其中 P5–P8 是**源图坐标**，是否 ×2 取决于素材是否重制 → 附录 B 标为「人工复核」；P1–P4 是屏幕坐标，明确 ×2。

### `br`
- 处理函数：`brCommand` — `ONScripter_command.cpp:3641`
- 参数：无。语义：文本换行（`sentence_font.newLine()`，页内追加 `\n`）。
- 坐标：否。

### `break`
- 处理函数：`breakCommand` — `ScriptParser_command.cpp:1409`
- 语法：`break [*标签]`
- 参数：可选标签字符串（`script_h.getNext()[0]=='*'` 判断，`readStr()+1` 跳过 `*`）。
- 语义：必须在 `for` 循环内；带标签时跳出循环并跳转；否则置 `break_flag`。
- 坐标：否。

### `bsp`
- 处理函数：`bspCommand` — `ONScripter_command.cpp:3612`
- 语法：`bsp <精灵号>[, <exbtn_ctl0>[, <exbtn_ctl1>[, <exbtn_ctl2>]]]`
- 参数：P1 精灵号；P2–P4 可选，最多 3 个扩展按钮控制串（`readStr()`，仅当检测到逗号）。
- 语义：把精灵注册为 `SPRITE_BUTTON`，命中矩形取 `sprite_info[no].pos`。
- 坐标：否（矩形来自精灵自身位置）。

### `btn`
- 处理函数：`btnCommand` — `ONScripter_command.cpp:3572`
- 语法：`btn <编号>, <x>, <y>, <w>, <h>, <src_x>, <src_y>`
- 参数：
  - P1 按钮编号（`button->no`）。
  - P2 `x`、P3 `y`、P4 `w`、P5 `h`：屏幕命中矩形（`image_rect`）。
  - P6 `src_x`、P7 `src_y`：从 `btndef` 位图取图的左上角；宽高沿用 P4/P5。
- 坐标：**是**（P2–P7 全为像素）。注意 P4/P5 同时用作**源图裁剪宽高**（`:490-491`），P6/P7 是源图坐标；它们是否 ×2 取决于素材是否重制 → 附录 B 标为「人工复核」。P2/P3 是屏幕坐标，明确 ×2。

### `btndef` / `bclear`
- 处理函数：`btndefCommand` — `ONScripter_command.cpp:3539`
- 语法：`btndef <文件名>` / `btndef clear` / `bclear`
- 参数：P1 字符串；`clear` 走 `compareString` 分支（`readLabel` 消费）；`bclear` 无参数。
- 语义：设置按钮底图（`btndef_info`），或清空底图/按钮；同时重置 `btntime_value = -1`、`transbtn_flag=false`、删除全部按钮。
- 坐标：否。

### `btndown`
- 处理函数：`btndownCommand` — `ONScripter_command.cpp:3532`
- 语法：`btndown <0|1>`
- 参数：P1 `readInt()==1` → `btndown_flag`。
- 坐标：否。

### `btntime` / `btntime2` / `btime`
- 处理函数：`btntimeCommand` — `ONScripter_command.cpp:3512`
- 语法：`btntime <毫秒>`；`btime <毫秒>[,1]`
- 参数：P1 等待时间（ms）；`btime` 可再跟一个整型，为 1 时启用 `btntime2_flag`（等待语音结束）。
- 坐标：否（时间）。

### `btnwait` / `btnwait2` / `textbtnwait` / `selectbtnwait` / `bexec`
- 处理函数：`btnwaitCommand` — `ONScripter_command.cpp:3355`
- 语法：
  - `btnwait <超时毫秒>`
  - `btnwait2 <超时毫秒>`（先 `leaveTextDisplayMode`）
  - `textbtnwait <超时毫秒>`
  - `selectbtnwait <超时毫秒>`
  - `bexec <字符串变量>[, <超时毫秒>]`
- 参数：
  - 普通形式 P1：超时（ms），`-1` 表示无限；`btnwait` 会 `del_flag` 并退出文本显示模式。
  - `bexec`：P1 目标字符串变量（`readStr()` + `pushVariable()`），P2 可选超时（`readInt()`）。
- 语义：显示全部按钮（按类型 `SPRITE_BUTTON`/`TMP_SPRITE_BUTTON`/普通按钮），进入 `WAIT_BUTTON_MODE` 等待点击/超时，结果写入 `current_button_state`。
- 坐标：否（等待与按钮状态）。

### `caption`
- 处理函数：`captionCommand` — `ONScripter_command.cpp:3337`
- 语法：`caption <标题字符串>`
- 参数：P1 字符串，经 `DirectReader::convertCodingToUTF8` 转 UTF-8 后作为窗口标题与图标名。
- 坐标：否。

### `cell`
- 处理函数：`cellCommand` — `ONScripter_command.cpp:3326`
- 语法：`cell <精灵号>, <cell号>`
- 参数：P1 精灵号；P2 单元格编号；设置后把该精灵矩形加入脏区。
- 坐标：否。

### `checkkey`
- 处理函数：`checkkeyCommand` — `ONScripter_command.cpp:3312`
- 语法：`checkkey <结果变量>, <按键字符串>`
- 参数：P1 整型结果变量；P2 待比较字符串；相等写 1，否则 0。
- 坐标：否。

### `checkpage`
- 处理函数：`checkpageCommand` — `ONScripter_command.cpp:3287`
- 语法：`checkpage <结果变量>, <页号>`
- 参数：P1 整型变量；P2 页号（相对当前页向前回溯的页数）；可回溯到则写 1，否则 0。
- 坐标：否。

### `chvol`
- 处理函数：`chvolCommand` — `ONScripter_command.cpp:3275`
- 语法：`chvol <通道>, <音量>`
- 参数：P1 通道（0..`ONS_MIX_CHANNELS-1`，越界钳制）；P2 音量百分比（0–100），作用到 `Mix_Volume`。
- 坐标：否。

### `cl`
- 处理函数：`clCommand` — `ONScripter_command.cpp:3249`
- 语法：`cl <l|c|r|a>`
- 参数：P1 标签（`readLabel()` 取首字符）：`l` 左立绘、`c` 中、`r` 右、`a` 全部。
- 坐标：否。

### `click` / `lrclick`
- 处理函数：`clickCommand` — `ONScripter_command.cpp:3232`
- 参数：无。语义：进入 `WAIT_TIMER_MODE|WAIT_INPUT_MODE` 等待点击；`lrclick` 额外等待右键。
- 坐标：否。

### `clickstr`
- 处理函数：`clickstrCommand` — `ScriptParser_command.cpp:1397`
- 语法：`clickstr <等待字符集>, <行号>`
- 参数：P1 字符串（`setClickstr`）；P2 整数（`clickstr_line`）。
- 坐标：否。

### `clickvoice`
- 处理函数：`clickvoiceCommand` — `ScriptParser_command.cpp:1386`
- 语法：`clickvoice <文件名1>, …`（共 `CLICKVOICE_NUM` 个）
- 参数：连续 `CLICKVOICE_NUM` 个字符串，写入 `clickvoice_file_name[]`；仅限 `define` 段。
- 坐标：否。

### `cmp`
- 处理函数：`cmpCommand` — `ScriptParser_command.cpp:1368`
- 语法：`cmp <结果变量>, <字符串A>, <字符串B>`
- 参数：P1 整型结果变量；P2/P3 字符串；结果 = `strcmp` 归一化为 -1/0/1。
- 坐标：否。

### `cos`
- 处理函数：`cosCommand` — `ScriptParser_command.cpp:1357`
- 语法：`cos <结果变量>, <角度>`
- 参数：P1 整型结果变量；P2 角度（度）；结果 = `cos(π*角度/180)*1000` 取整。
- 坐标：否（三角函数值，非坐标）。

---

## 本册统计

- 处理函数：38 个
- 命令条目：49 条（含别名与重复注册）
- 其中含坐标参数：`amsp/amsp2`、`bar`、`blt`、`btn`（共 5 组）
- 未定位：0
