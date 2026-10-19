# 命令分册 04：处理函数 `isskipCommand` – `mp3Command`

> 本册覆盖 `func_lut` 中处理函数位于 `isskipCommand`…`mp3Command` 区间的全部命令（含别名），共 45 个处理函数、68 条命令。
> 行号均指快照 `src/onsyuri/`。参数按**源码读取顺序**列出，逗号分隔；`[ ]` 表示由 `script_h.getEndStatus() & END_COMMA` 判定的可选参数。
> 「坐标」列：**是** = 含需在 ×2 转换中处理的像素参数；**否** = 不含；**部分** = 仅部分参数是。
> 特别注意：`locate` 的参数是**字符单位**而非像素（`FontInfo::setXY` 把 x 乘 2 存为半角单位），**不在 ×2 白名单**。

---

## I

### `isskip`
- 处理函数：`isskipCommand` — `ONScripter_command.cpp:2043`
- 语法：`isskip %结果`
- 参数：P1 结果整型变量（`readInt()`，`:2045`）。
- 语义：`automode_flag` 为真写 2；否则 `skip_mode & SKIP_NORMAL` 为真写 1；否则写 0（`:2047-2053`）。
- 坐标：否。

### `itoa` / `itoa2`
- 处理函数：`itoaCommand` — `ScriptParser_command.cpp:866`
- 语法：`itoa $结果, <整数>` / `itoa2 $结果, <整数>`
- 参数：
  - P1 字符串变量（`readVariable()`，必须 `$` 类型，否则 `errorAndExit`）。
  - P2 整数（`readInt()`）。
- 语义：`itoa` 用 `sprintf("%d")`；`itoa2` 用 `script_h.getStringFromInteger(val, -1)`（按位数补零/格式化），写入 P1（`:878-885`）。
- 坐标：否。

## J

### `jumpb`
- 处理函数：`jumpbCommand` — `ONScripter_command.cpp:2014`
- 参数：无。
- 语义：跳到上一个 `~` 标记（`last_tilde.next_script`），并同步 `current_label_info/current_line`（`:2016-2018`）。
- 坐标：否。

### `jumpf`
- 处理函数：`jumpfCommand` — `ONScripter_command.cpp:2001`
- 参数：无。
- 语义：从当前读取位置向后找第一个 `~`，跳过它并把执行位置设到其后（`:2003-2008`）。
- 坐标：否。

## K

### `kidokumode`
- 处理函数：`kidokumodeCommand` — `ScriptParser_command.cpp:856`
- 语法：`kidokumode <0|1>`
- 参数：P1 `readInt()==1` → `kidokumode_flag`（`:858-861`）。
- 坐标：否。

### `kidokuskip`
- 处理函数：`kidokuskipCommand` — `ScriptParser_command.cpp:848`
- 参数：无。
- 语义：`kidokuskip_flag = true`，并 `script_h.loadKidokuData()`（`:850-851`）。
- 坐标：否。

### `kinsoku`
- 处理函数：`kinsokuCommand` — `ONScripter_command.cpp:1987`
- 语法：`kinsoku on` / `kinsoku off`
- 参数：P1 关键字（`compareString("on"/"off")` + `readLabel()` 消费），非 on/off 时不消费。
- 语义：置 `is_kinsoku`（`:1989-1995`）。
- 坐标：否。

## L

### `labellog`
- 处理函数：`labellogCommand` — `ScriptParser_command.cpp:837`
- 参数：无。仅限 `define` 段。
- 语义：`labellog_flag = true`，并 `readLog(LABEL_LOG)`（`:839-842`）。
- 坐标：否。

### `layermessage`
- 处理函数：`layermessageCommand` — `ONScripter_command.cpp:1967`
- 语法：`layermessage <图层号>, <消息串>`
- 参数：P1 图层号（`readInt()`）；P2 消息（`readStr()`）。
- 语义：把消息送给内建图层处理器 `Layer::message()`，返回值存入 `getret_str/getret_int`；未编译 `USE_BUILTIN_LAYER_EFFECTS` 时只打印提示（`:1969-1980`）。
- 坐标：否。

### `ld`
- 处理函数：`ldCommand` — `ONScripter_command.cpp:1906`
- 语法：`ld <l|c|r>, <文件名>`；`ld` 后可跟特效参数（`parseEffect`）。
- 参数：
  - P1 位置标签（`readLabel()` 首字符）：`l`→立绘槽 0、`c`→1、`r`→2；其它值不读文件名（`:1910-1917`）。
  - P2 文件名（`readStr()`，仅 P1 合法时读）。
  - 之后是可选特效参数（`parseEffect(true)`，`:1936`）。
- 语义：加载立绘并按 `underline_value` 与 `screen_width` **自动**计算位置：x = `screen_width*(no+1)/4 - 图宽/2`，y = `underline_value - 图高`（`:1929-1930`）。**没有** x/y 参数。
- 坐标：否（位置由引擎按 `screen_width/height` 计算；分辨率变化后自动跟随）。
- 证据：`:1906-1941`。

### `len`
- 处理函数：`lenCommand` — `ScriptParser_command.cpp:825`
- 语法：`len %结果, <字符串>`
- 参数：P1 整型变量（`readInt()` + `pushVariable()`）；P2 字符串（`readStr()`）。
- 语义：`strlen(P2)` 写入 P1（`:830`）。
- 坐标：否。

### `linepage` / `linepage2`
- 处理函数：`linepageCommand` — `ScriptParser_command.cpp:808`
- 语法：`linepage` / `linepage2 <clickstr_line>`
- 参数：`linepage2` 读 P1 整数写入 `clickstr_line`；`linepage` 无参数（`:813-818`）。
- 语义：置 `linepage_mode = 1` 或 `2`，并 `script_h.setLinepage(true)`。仅限 `define` 段。
- 坐标：否。

### `loadgame`
- 处理函数：`loadgameCommand` — `ONScripter_command.cpp:1865`
- 语法：`loadgame <存档号>`
- 参数：P1 存档槽号（`readInt()`）。
- 语义：临时关闭 mp3 fadeout 后 `loadSaveFile(no)`；成功后重置文本/按钮/跳过状态，触发 Lua `load` 回调与 `loadgosub` 标签（`:1867-1928`）。
- 坐标：否。

### `loadgosub`
- 处理函数：`loadgosubCommand` — `ScriptParser_command.cpp:798`
- 语法：`loadgosub *标签`
- 参数：P1 字符串（`readStr()+1` 跳过 `*`），存入 `loadgosub_label`。仅限 `define` 段。
- 坐标：否。

### `locate`
- 处理函数：`locateCommand` — `ONScripter_command.cpp:1856`
- 语法：`locate <x>, <y>`
- 参数：
  - P1 `x`：**全角字符数**（不是像素）。
  - P2 `y`：**行数**。
- 语义：`sentence_font.setXY(x,y)`（`:1858-1860`）；`FontInfo::setXY` 把 x/y 乘 2 存入 `xy`（半角单位），`x()` 再按 `xy*pitch/2 + top_xy` 换算成像素（`FontInfo.cpp:185`、`:199-203`）。
- 坐标：**否**（单位是字符/行；×2 转换**不得**修改）。若误当像素 ×2，文本会向右下跳一整倍字符宽度。
- 证据：`ONScripter_command.cpp:1856-1863`；`FontInfo.cpp:183-203`。

### `logsp` / `logsp2`
- 处理函数：`logspCommand` — `ONScripter_command.cpp:1788`
- 语法：
  - `logsp <精灵号>, <文本>, <x>, <y>[, <颜色>...]`
  - `logsp2 <精灵号>, <文本>, <x>, <y>, <字号x>, <字号y>, <字距x>, <字距y>[, <颜色>...]`
- 参数：
  - P1 精灵号；P2 文本；P3 x、P4 y（像素，→ `orig_pos`）。
  - `logsp2` 追加 P5/P6 字号（像素）、P7/P8 字距**附加值**（`pitch = 值 + 字号`）。
  - 其余逗号分隔项为颜色 `#RRGGBB`；无颜色时默认白（`:1823-1841`）。
- 坐标：**是**（P3/P4 像素；`logsp2` 的 P5–P8 字号/字距也属字号体系）。
- 证据：`:1788-1849`。

### `lookbackbutton`
- 处理函数：`lookbackbuttonCommand` — `ONScripter_command.cpp:1777`
- 语法：`lookbackbutton <图1>, <图2>, <图3>, <图4>`
- 参数：连续 4 个图像名（`readStr()`），分别写入 `lookback_info[0..3]` 并解析标签/加载（`:1779-1784`）。
- 坐标：否。

### `lookbackcolor`
- 处理函数：`lookbackcolorCommand` — `ScriptParser_command.cpp:790`
- 语法：`lookbackcolor <#RRGGBB>`
- 参数：P1 颜色字符串 → `lookback_color`。
- 坐标：否。

### `lookbackflush`
- 处理函数：`lookbackflushCommand` — `ONScripter_command.cpp:1764`
- 参数：无。
- 语义：清空回看页链并把 `start_page` 前移（`:1766-1771`）。
- 坐标：否。

### `lookbacksp`
- 处理函数：`lookbackspCommand` — `ScriptParser_command.cpp:772`
- 语法：`lookbacksp <精灵号1>, <精灵号2>`
- 参数：2 个整数写入 `lookback_sp[0..1]`；仅限 `define` 段。
- 坐标：否。

### `loopbgm`
- 处理函数：`loopbgmCommand` — `ONScripter_command.cpp:1749`
- 语法：`loopbgm <前奏文件>, <循环文件>`
- 参数：P1 前奏、P2 循环体，均 `readStr()`；P2 预加载到 `MIX_LOOPBGM_CHANNEL1`，P1 播放于 `MIX_LOOPBGM_CHANNEL0`（`:1751-1777`）。
- 坐标：否。

### `loopbgmstop`
- 处理函数：`loopbgmstopCommand` — `ONScripter_command.cpp:1732`
- 参数：无。语义：停止并释放两个循环 BGM 通道，清空 `loop_bgm_name[0]`。
- 坐标：否。

### `lsp` / `lsph`
- 处理函数：`lspCommand` — `ONScripter_command.cpp:1699`
- 语法：`lsp <精灵号>, <文件名>, <x>, <y>[, <透明度>]`
- 参数：
  - P1 精灵号（`sprite_info` 下标）。
  - P2 图像名（可带 `:a/:s/...` 标签）。
  - P3 x、P4 y（像素，→ `orig_pos`）。
  - P5 `[可选]` 透明度（`END_COMMA` 判断；缺省 -1）。
- 语义：`lsph` 只把 `visible` 置假（`:1703-1704`）；加载标签并 `setupAnimationInfo`。
- 坐标：**是**（P3/P4）。
- 证据：`:1699-1730`。

### `lsp2` / `lsp2add` / `lsp2sub` / `lsph2` / `lsph2add` / `lsph2sub`
- 处理函数：`lsp2Command` — `ONScripter_command.cpp:1650`
- 语法：`lsp2 <精灵号>, <文件名>, <x>, <y>, <scale_x>, <scale_y>, <rot>[, <透明度>]`
- 参数：
  - P1 精灵号（`sprite2_info`）；P2 文件名；P3 x、P4 y（像素）。
  - P5 `scale_x`、P6 `scale_y`（缩放倍率，千分比语义见 `calcAffineMatrix`）；P7 `rot`（角度）。
  - P8 `[可选]` 透明度。
- 语义：`lsph2*` 置不可见；`add/sub` 选 `BLEND_ADD/BLEND_SUB` 混合模式（`:405-414`）。
- 坐标：**是**（P3/P4；P5–P7 是变换参数，不是坐标）。
- 证据：`:1650-1695`。

### `luacall`
- 处理函数：`luacallCommand` — `ScriptParser_command.cpp:761`
- 语法：`luacall <函数名>`
- 参数：P1 标签/函数名（`readLabel()`）；`USE_LUA` 下 `lua_handler.addCallback(label)`（`:763-766`）。
- 坐标：否。

### `luasub`
- 处理函数：`luasubCommand` — `ScriptParser_command.cpp:746`
- 语法：`luasub <命令名>`
- 参数：P1 名称（`readLabel()`），注册为用户函数并标记 `lua_flag`（`:748-756`）。
- 坐标：否。

## M

### `maxkaisoupage`
- 处理函数：`maxkaisoupageCommand` — `ScriptParser_command.cpp:737`
- 语法：`maxkaisoupage <页数>`
- 参数：P1 整数，`max_page_list = P1 + 1`；仅限 `define` 段。
- 坐标：否（页数，不是像素）。

### `menu_automode`
- 处理函数：`menu_automodeCommand` — `ONScripter_command.cpp:1641`
- 参数：无。语义：`automode_flag = true` 并清 skip。
- 坐标：否。

### `menu_click_def` / `menu_click_page`
- 处理函数：`menu_click_defCommand` — `:1635`；`menu_click_pageCommand` — `:1629`
- 参数：无。语义：分别清/置 `SKIP_TO_EOP`。
- 坐标：否。

### `menu_full` / `menu_window`
- 处理函数：`menu_fullCommand` — `:1623`；`menu_windowCommand` — `:1617`
- 参数：无。语义：`setFullScreen(true/false)`（切换全屏/窗口）。
- 坐标：否。

### `menuselectcolor`
- 处理函数：`menuselectcolorCommand` — `ScriptParser_command.cpp:723`
- 语法：`menuselectcolor <选中色>, <未选中色>, <无素材色>`
- 参数：3 个 `#RRGGBB`，分别写入 `menu_font.on_color/off_color/nofile_color`（`:725-732`）。
- 坐标：否。

### `menuselectvoice`
- 处理函数：`menuselectvoiceCommand` — `ScriptParser_command.cpp:712`
- 语法：`menuselectvoice <文件名1>, …`（共 `MENUSELECTVOICE_NUM` 个）
- 参数：连续 `MENUSELECTVOICE_NUM` 个字符串；仅限 `define` 段。
- 坐标：否。

### `menusetwindow`
- 处理函数：`menusetwindowCommand` — `ScriptParser_command.cpp:690`
- 语法：`menusetwindow <字号x>, <字号y>, <字距x>, <字距y>, <bold>, <shadow>[, <颜色|图名>]`
- 参数：
  - P1/P2 菜单字号（像素）→ `menu_font.font_size_xy`。
  - P3/P4 字距**附加值**（像素）→ `pitch_xy = 值 + 字号`。
  - P5 粗体 0/1；P6 阴影 0/1。
  - P7 `[可选]` `#RRGGBB` 或图像名；空串时窗口色置 0x99（`:694-701`）。
- 坐标：**是**（P1–P4 字号/字距为像素）。
- 证据：`:690-596`。

### `mid`
- 处理函数：`midCommand` — `ScriptParser_command.cpp:662`
- 语法：`mid $结果, <源串>, <起始>, <长度>`
- 参数：P1 字符串变量（`readStr()` 后检查 `VAR_STR`）；P2 源串；P3 起始下标；P4 长度（`:664-669`）。
- 语义：越界时截断/置空（`:672-682`）。
- 坐标：否。

### `mod`
- 处理函数：`modCommand` — `ScriptParser_command.cpp:651`
- 语法：`mod <被除数>, <结果变量>, <除数>`
- 参数：P1 被除数（`readInt()`）；P2 结果变量（`pushVariable()`）；P3 除数；结果 `val1 % val2`（`:653-656`）。除数为 0 未检查（待验证）。
- 坐标：否。

### `mode_ext` / `automode`
- 处理函数：`mode_extCommand` — `ScriptParser_command.cpp:642`
- 参数：无。仅限 `define` 段；置 `mode_ext_flag = true`（`:644-645`）。
- 坐标：否。

### `mode_saya`
- 处理函数：`mode_sayaCommand` — `ScriptParser_command.cpp:633`
- 参数：无。仅限 `define` 段；置 `mode_saya_flag`。
- 坐标：否。

### `mode_wave_demo`
- 处理函数：`mode_wave_demoCommand` — `ScriptParser_command.cpp:624`
- 参数：无。仅限 `define` 段；置 `mode_wave_demo_flag`。
- 坐标：否。

### `monocro`
- 处理函数：`monocroCommand` — `ONScripter_command.cpp:1595`
- 语法：`monocro off` / `monocro <#RRGGBB>`
- 参数：P1 关键字或颜色（`compareString("off")` 分支只 `readLabel()`；否则 `readStr()` 走 `readColor`）。
- 语义：开/关单色滤镜并重建 LUT，标脏全屏（`:1597-1610`）。
- 坐标：否。

### `mov` / `mov3` / `mov4` / `mov5` / `mov6` / `mov7` / `mov8` / `mov9` / `mov10` / `movl`
- 处理函数：`movCommand` — `ScriptParser_command.cpp:587`
- 语法：
  - 整数/数组：`mov %目标, <值1>[, <值2>...]`（`movN` 最多 N 个，`movl` 无限）
  - 字符串：`mov $目标, <字符串>`
- 参数：
  - P1 目标变量（`readVariable()`）。
  - P2… 值列表：整型/数组走 `readInt()` 逐个 `setInt(..., i++)`；字符串走 `readStr()`。
- 语义：**赋值**，不是动画移动；`mov3`…`mov10` 的个数由命令名第 4 个字符决定（`:591-599`）。
- 坐标：否。
- 备注：这是最容易误判为「移动动画」的命令；真正的精灵移动是 `msp/lsp/amsp`。

### `movemousecursor`
- 处理函数：`movemousecursorCommand` — `ONScripter_command.cpp:1583`
- 语法：`movemousecursor <x>, <y>`
- 参数：P1/P2 逻辑像素坐标；先乘 `screen_ratio1/2`，再换算到设备坐标并加 `render_view_rect` 偏移（`:1585-1592`）。
- 坐标：**是**（P1/P2）。
- 证据：`:1583-1593`。

### `movie`
- 处理函数：`movieCommand` — `ONScripter_command.cpp:1536`
- 语法：`movie <文件名>[, pos, x, y, w, h][, click][, loop][, async]`
- 参数：
  - P1 文件名（`readStr()`）；`movie stop` 为特例（`compareString("stop")`）。
  - 之后逗号分隔的关键字：`pos` 读 4 个整数但**不支持**（只警告）；`click`/`loop` 置标志；`async` 不支持（`:753-777`）。
- 语义：`playMPEG(filename, click, loop)`；失败则 `endCommand()`。
- 坐标：否（`pos` 的 x/y/w/h 被读取后丢弃，不生效）。

### `mp3` / `mp3loop` / `mp3save` / `bgm` / `bgmonce`
- 处理函数：`mp3Command` — `ONScripter_command.cpp:1453`
- 语法：`mp3 <文件名>[,(<循环起点秒数>)]`
- 参数：
  - P1 文件名（`readStr()`）；可写成 `(12.5)file.mp3` 形式指定循环回跳秒数（`:810-829`）。
  - 无其它位置参数。
- 语义：
  - `bgm`/`mp3loop` → 循环 + 可存档；`mp3save` → 可存档不循环；`bgmonce`/`mp3` → 不循环不可存档（`:787-801`）。
  - 先 `mp3stopCommand()` + `stopBGM(false)`，再按 `mp3fadein_duration` 做淡入（`:803-846`）。
- 坐标：否。

---

## 本册统计

- 处理函数：**45 个**
- 命令条目：**68 条**（含别名：`itoa/itoa2`、`linepage/linepage2`、`lsp/lsph`、`lsp2/lsp2add/lsp2sub/lsph2/lsph2add/lsph2sub`、`logsp/logsp2`、`mov/mov3…mov10/movl`、`mp3/mp3loop/mp3save/bgm/bgmonce`、`movie`、`mode_ext/automode`）
- 含坐标参数：`logsp`/`logsp2`、`lsp`/`lsph`、`lsp2*`、`menusetwindow`、`movemousecursor`（5 组）
- 明确**不** ×2 的易混命令：`locate`（字符/行单位）、`ld`（位置由引擎自动计算）、`movie`（`pos` 参数不生效）、`maxkaisoupage`（页数）
- 未定位：**0**
- 待验证：`mod` 除数为 0；`movie` 的 `pos` 是否在未来版本启用；`mp3` 循环起点解析的边界（`)` 缺失）；`itoa2` 的 `getStringFromInteger(val,-1)` 具体格式。
