# 命令分册 03：处理函数 `getcursorCommand` – `ispageCommand`

> 本册覆盖 `func_lut`（`ONScripter_lut.cpp`）中处理函数位于 `getcursorCommand`…`ispageCommand` 区间的全部命令（含别名），共 **43 个处理函数 / 49 条命令**。
> 行号均指快照 `src/onsyuri/`。参数按**源码读取顺序**列出，逗号分隔；`[ ]` 表示可选（由 `script_h.getEndStatus() & END_COMMA` 判断）。
> 「坐标」列：**是** = 该命令含逻辑坐标/尺寸参数，需在 ×2 转换中处理；**否** = 不含；**部分** = 仅部分参数是坐标。
> 判定依据：参数是否直接写入 `orig_pos.x/y/w/h`、`pos`、`image_rect`、`top_xy`、`num_xy`、`font_size_xy`、`pitch_xy`、`max_width`、`dst_rect/src_rect` 等坐标字段，或与 `screen_width/height`、`screen_ratio1/2` 做像素运算。
> 通用约定：本册多数「get / is」类命令的**首个参数是变量引用**（`%` 整型或 `$` 字符串）。源码用 `script_h.readInt()` / `readStr()` / `readVariable()` 读取该变量，再用 `script_h.setInt( &script_h.current_variable, … )` 把结果写回同一个变量；因此语法上必须写成变量，写成立即数不会得到结果。
> 说明：`analysis/cards/cards-03.txt` 中 `gettagCommand`、`ifCommand` 的函数体摘录被截断（各 60 行），本册按源码补全。

---

## G

### `getcursor` / `bcursor`
- 处理函数：`getcursorCommand` — `ONScripter_command.cpp:2612`
- 注册：`ONScripter_lut.cpp:283`（`getcursor`）、`:380`（`bcursor`）
- 语法：`getcursor`（无参数）
- 参数：无。
- 可选参数：无。
- 语义：置 `getcursor_flag = true`（`:2614-2615`），把方向键从「滚轮 / 回看 / 按钮光标移动」改为返回按钮码：↑ = `-40`（`"UP"`）、→ = `-41`（`"RIGHT"`）、↓ = `-42`（`"DOWN"`）、← = `-43`（`"LEFT"`）（`ONScripter_event.cpp:951-966`）。未开启时方向键承担滚轮与回看（`ONScripter_event.cpp:902-935`）。
- 坐标：**否**。无参数；仅改变按键语义。
- 证据：`ONScripter_command.cpp:2612-2618`；事件分支 `ONScripter_event.cpp:951-966`；别名注册 `ONScripter_lut.cpp:380`。
- 备注：`force_button_shortcut_flag` 为真时（启动参数 `-force-button-shortcut`，`onscripter_main.cpp:445-447` → `ONScripter.cpp:488-491`）命令**静默无效**（`:2614`）。`bcursor` 与 `getcursor` 完全等价，仅注册分组不同。

### `getcursorpos`
- 处理函数：`getcursorposCommand` — `ONScripter_command.cpp:2593`
- 注册：`ONScripter_lut.cpp:282`
- 语法：`getcursorpos %x, %y`
- 参数：
  - P1：x 结果变量（`readInt()`，`:2603`），写入 `fi.x(false)`（`:2604`）。
  - P2：y 结果变量（`readInt()`，`:2606`），写入 `fi.y(false)`（`:2607`）。
- 可选参数：无。
- 语义：先把 `sentence_font` 复制到局部 `fi`；若 `fi.isEndOfLine()` 则模拟换行 `fi.newLine()`，并按 `indent_offset` 逐单位 `advanceCharInHankaku(2)` 缩进（`:2595-2601`）。返回值为「下一个待输出字符」的左上角。
- 坐标：**是**。P1/P2 都是屏幕逻辑坐标（px）：`FontInfo::x()` = `xy[0]*pitch_xy[0]/2 + top_xy[0] + line_offset_xy[0]`，`y()` 同理（`FontInfo.cpp:183-197`）；该坐标**未乘** `screen_ratio1/2`，渲染时才换算（`ONScripter_text.cpp:225-226`）。
- 证据：`ONScripter_command.cpp:2593-2610`；`FontInfo.cpp:183-197`；`ONScripter_text.cpp:225-226`。

### `getcursorpos2`
- 处理函数：`getcursorpos2Command` — `ONScripter_command.cpp:2582`
- 注册：`ONScripter_lut.cpp:281`
- 语法：`getcursorpos2 %x, %y`
- 参数：
  - P1：x 结果变量（`readInt()`，`:2584`）→ `sentence_font.old_xy[0]`（`:2585`）。
  - P2：y 结果变量（`readInt()`，`:2587`）→ `sentence_font.old_xy[1]`（`:2588`）。
- 可选参数：无。
- 语义：返回**最近一次绘制字符的起点**。`old_xy` 在绘制每个字符前写入（此时已完成换行 / 缩进处理，`ONScripter_text.cpp:204-216`）；另有 `ONScripter.cpp:835-836` 的写入点。
- 坐标：**是**。P1/P2 均为逻辑坐标（与 `getcursorpos` 同源，取自 `FontInfo::x/y(false)`）。
- 证据：`ONScripter_command.cpp:2582-2591`；`ONScripter_text.cpp:215-216`；`ONScripter.cpp:835-836`。

### `getenter`
- 处理函数：`getenterCommand` — `ONScripter_command.cpp:2574`
- 注册：`ONScripter_lut.cpp:280`
- 语法：`getenter`（无参数）
- 参数：无。
- 可选参数：无。
- 语义：置 `getenter_flag = true`（`:2576-2577`），使主键盘 / 小键盘 Enter 在等待模式下返回按钮码 `-19`（`ONScripter_event.cpp:944-947`）。未开启时 Enter 等同点击当前按钮，返回 `"RETURN"` 或 `"S%d"`（`ONScripter_event.cpp:838-880`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:2574-2580`；`ONScripter_event.cpp:838-880`、`:944-947`。
- 备注：与 `getcursor` 相同，`force_button_shortcut_flag` 为真时不置位、静默无效（`:2576`）。

### `getfunction`
- 处理函数：`getfunctionCommand` — `ONScripter_command.cpp:2567`
- 注册：`ONScripter_lut.cpp:279`
- 语法：`getfunction`（无参数）
- 参数：无。
- 可选参数：无。
- 语义：置 `getfunction_flag = true`（`:2569`），F1–F12 返回按钮码 `-21`…`-32`，并写入 `current_button_state.str` = `"F1"`…`"F12"`（`ONScripter_event.cpp:979-983`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:2567-2572`；`ONScripter_event.cpp:979-983`。

### `getinsert`
- 处理函数：`getinsertCommand` — `ONScripter_command.cpp:2560`
- 注册：`ONScripter_lut.cpp:278`
- 语法：`getinsert`（无参数）
- 参数：无。
- 可选参数：无。
- 语义：置 `getinsert_flag = true`（`:2562`），Insert 键返回按钮码 `-50`（`ONScripter_event.cpp:967-969`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:2560-2565`；`ONScripter_event.cpp:967-969`。

### `getlog` / `getlogtext`
- 处理函数：`getlogCommand` — `ONScripter_command.cpp:2503`
- 注册：`ONScripter_lut.cpp:277`（`getlog`）、`:276`（`getlogtext`）
- 语法：
  - `getlog $结果, <页号>`
  - `getlogtext $结果, <页号>`
- 参数：
  - P1：目标**字符串**变量（`readVariable()` + `pushVariable()`，`:2510-2511`）。
  - P2：页号（`readInt()`，`:2513`）。`0` = 当前页，`1` = 上一页，依此类推；从 `current_page` 沿 `previous` 回溯，最多到 `start_page`（`:2515-2519`）。
- 可选参数：无。
- 语义：页号超出历史（回溯到 `start_page` 仍 `page_no > 0`）时写空串（`:2521-2522`）。`getlog` 原样复制 `page->text`（长度 `text_count`，含换行 0x0a）；`getlogtext` 去掉 0x0a，并在 `coding2utf16->force_utf8` 时按 UTF-8 前导字节复制，否则按双字节 / 单字节复制（`:2524-2552`）。命令名由 `script_h.isName("getlogtext")` 区分（`:2507-2508`）。
- 坐标：**否**。P1 是变量引用；P2 是页号（计数），不涉及像素。
- 证据：`ONScripter_command.cpp:2503-2558`；页结构 `ONScripter.h`（`Page`）与 `ONScripter_text.cpp:735-744`。
- 备注（待验证）：`getlogtext` 的 UTF-8 分支内层循环写作 `for (int j=0; i < leading; j++)`（`:2533`），条件使用外层计数 `i` 且循环内不改变 `i`，疑似死循环 / 越界写；`force_utf8` 为真时的实际表现待验证。

### `getmclick`
- 处理函数：`getmclickCommand` — `ONScripter_command.cpp:2468`
- 注册：`ONScripter_lut.cpp:275`
- 语法：`getmclick`（无参数）
- 参数：无。
- 可选参数：无。
- 语义：置 `getmclick_flag = true`（`:2470`），鼠标中键返回按钮码 `-70`，字符串 `"MCLICK"`（`ONScripter_event.cpp:443-446`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:2468-2473`；`ONScripter_event.cpp:443-446`。

### `getmouseover`
- 处理函数：`getmouseoverCommand` — `ONScripter_command.cpp:2493`
- 注册：`ONScripter_lut.cpp:274`
- 语法：`getmouseover <起始按钮号>, <结束按钮号>`
- 参数：
  - P1：`getmouseover_lower`（`readInt()`，`:2497`）——按钮编号下界（含）。
  - P2：`getmouseover_upper`（`readInt()`，`:2498`）——按钮编号上界（含）。
- 可选参数：无。
- 语义：在 `WAIT_BUTTON_MODE` 中，鼠标移动到编号落在 `[lower, upper]` 区间内的按钮上时，立即把该按钮号作为点击结果返回（`ONScripter_event.cpp:359-366`），即「悬停即触发」。
- 坐标：**否**。P1/P2 是按钮编号（`btn` / `spbtn` 注册的 `no`），不是屏幕坐标；该命令不读取任何像素参数。
- 证据：`ONScripter_command.cpp:2493-2501`；`ONScripter_event.cpp:354-370`；字段定义 `ONScripter.h:400-402`。

### `getmousepos`
- 处理函数：`getmouseposCommand` — `ONScripter_command.cpp:2482`
- 注册：`ONScripter_lut.cpp:273`
- 语法：`getmousepos %x, %y`
- 参数：
  - P1：x 结果变量（`readInt()`，`:2484`）→ `current_button_state.x * screen_ratio2 / screen_ratio1`（`:2485`）。
  - P2：y 结果变量（`readInt()`，`:2487`）→ 同式（`:2488`）。
- 可选参数：无。
- 语义：把事件层记录的鼠标位置换算回脚本逻辑坐标。`current_button_state.x/y` 在 `mouseMoveEvent`（`ONScripter_event.cpp:356-357`）、`mousePressEvent`（`:387-388`）、`mouseWheelEvent`（`:470-471`）中写入，值是窗口像素坐标。
- 坐标：**是**。P1/P2 都是坐标，且显式做 `screen_ratio` 换算；本构建 `screen_ratio1 = screen_ratio2 = 1`（`ScriptParser.cpp:51-52`），因此当前为恒等映射，但移植 / 改窗口缩放时必须按坐标处理。
- 证据：`ONScripter_command.cpp:2482-2491`；`ONScripter_event.cpp:356-357`、`:387-388`、`:470-471`；`ScriptParser.cpp:51-52`。

### `getmp3vol` / `getbgmvol`
- 处理函数：`getmp3volCommand` — `ONScripter_command.cpp:2475`
- 注册：`ONScripter_lut.cpp:272`（`getmp3vol`）、`:287`（`getbgmvol`）
- 语法：`getmp3vol %音量` / `getbgmvol %音量`
- 参数：
  - P1：结果整型变量（`readInt()`，`:2477`）→ `music_volume`（`:2478`）。
- 可选参数：无。
- 语义：返回当前 BGM 音量，取值 0–100，默认 `DEFAULT_VOLUME = 100`（`ONScripter.h:64`，初始化 `ONScripter.cpp:1484`）；播放时按 `music_volume * MIX_MAX_VOLUME / 100` 应用（`ONScripter_sound.cpp:189`、`:224`）。
- 坐标：**否**。P1 是音量，不是坐标。
- 证据：`ONScripter_command.cpp:2475-2480`；`ONScripter_sound.cpp:189`、`:224`；别名注册 `ONScripter_lut.cpp:287`。

### `getpage`
- 处理函数：`getpageCommand` — `ONScripter_command.cpp:2380`
- 注册：`ONScripter_lut.cpp:271`
- 语法：`getpage`（无参数）
- 参数：无。
- 可选参数：无。
- 语义：同时置 `getpageup_flag` 与 `getpagedown_flag`（`:2382-2383`），使 PageUp 返回 `-12`（`"PAGEUP"`）、PageDown 返回 `-13`（`"PAGEDOWN"`）（`ONScripter_event.cpp:936-943`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:2380-2386`；`ONScripter_event.cpp:936-943`。

### `getpageup`
- 处理函数：`getpageupCommand` — `ONScripter_command.cpp:2373`
- 注册：`ONScripter_lut.cpp:270`
- 语法：`getpageup`（无参数）
- 参数：无。
- 可选参数：无。
- 语义：只置 `getpageup_flag`（`:2375`），PageUp 返回 `-12`；PageDown 维持默认行为（`ONScripter_event.cpp:936-939`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:2373-2378`；`ONScripter_event.cpp:936-943`。

### `getparam` / `getparam2`
- 处理函数：`getparamCommand` — `ScriptParser_command.cpp:1074`
- 注册：`ONScripter_lut.cpp:269`（`getparam`）、`:268`（`getparam2`）
- 语法：
  - `getparam <变量1>[, <变量2>[, …]]`
  - `getparam2 <变量1>[, <变量2>[, …]]`
- 参数：**变长**，按源码顺序 P1…Pn 均为「接收变量」；每个变量按自身类型取值（`:1083-1102`）：
  - P_k 为指针变量（`VAR_PTR`）：从调用方实参再读一个变量，取其变量编号写入（`:1091-1093`）。
  - P_k 为整型 / 数组（`VAR_INT` / `VAR_ARRAY`）：`readInt()` 写入（`:1095-1097`）。
  - P_k 为字符串（`VAR_STR`）：`readStr()` 后 `setStr()` 写入（`:1099-1101`）。
- 可选参数：无固定数量；参数表长度可变，由 `END_COMMA` 决定循环次数（`:1108-1109`）。
- 语义：必须位于 `gosub` 建立的子程序内（`nest_mode == NestInfo::LABEL`，`:1076-1077`）。通过 `pushCurrent(last_nest_info->next_script)` 切到**调用方**的实参位置读取（`:1089`），每读一个就把 `next_script` 前移（`:1106`），从而实现实参到形参的绑定。`getparam2` 在绑定完成后，把剩余列出的变量清零 / 置空（`:1111-1127`）。
- 坐标：**否**。全部参数是变量引用，不含像素量。
- 证据：`ScriptParser_command.cpp:1074-1130`；`gosubReal` `:1045-1055`；`return` `:449-477`。
- 备注（待验证）：调用方实参少于形参时，多余形参保持原值；实参多于形参时，多出的实参仍留在 `next_script` 处（`:1106`），后续执行行为待验证。

### `getreg`
- 处理函数：`getregCommand` — `ONScripter_command.cpp:2405`
- 注册：`ONScripter_lut.cpp:267`
- 语法：`getreg $结果, <节名>, <键名>`
- 参数：
  - P1：目标**字符串**变量（`readVariable()`，`:2407`）；非字符串直接 `errorAndExit("getreg: no string variable.")`（`:2409-2410`）。
  - P2：节名（`readStr()` → `path[256]`，`:2413-2415`），对应 registry 文件中的 `[节名]`。
  - P3：键名（`readStr()` → `key[256]`，`:2416-2417`），对应节内的 `键名=值`。
- 可选参数：无。
- 语义：打开 `registry_file`（默认 `"registry.txt"`，`ONScripter.cpp:43`、`:365`；可用启动参数 `-registry` 改，`onscripter_main.cpp:435-439` → `ONScripter.cpp:422`）。逐行扫描，遇到 `[` 开头行比较节名（`:2430-2433`），命中后在节内把每行当脚本片段 `pushCurrent` 并 `readStr()` 取键名比较（`:2436-2443`），再匹配 `=`（`:2445-2448`），最后读值写入 P1（`:2451-2452`）。文件打不开只打印错误并返回（`:2422-2425`）；键找不到只打印 `"The key is not found."`（`:2462`），P1 不变。
- 坐标：**否**。P1 变量、P2/P3 均为字符串标识，不含像素量。
- 证据：`ONScripter_command.cpp:2405-2466`；`ONScripter.cpp:43`、`:365`、`:422`；`onscripter_main.cpp:435-439`。
- 备注（待验证）：`path` / `key` 为 256 字节栈缓冲且用 `strcpy`（`:2414-2417`），超长参数可能越界；键名比较长度取两者较大值（`:2438-2440`），语义上等价于全等匹配。

### `getret`
- 处理函数：`getretCommand` — `ONScripter_command.cpp:2388`
- 注册：`ONScripter_lut.cpp:266`
- 语法：`getret <变量>`
- 参数：
  - P1：接收变量，类型决定分支：`VAR_INT` / `VAR_ARRAY` → `getret_int`（`:2392-2394`）；`VAR_STR` → `getret_str`（`:2396-2398`）；其它类型 `errorAndExit("getret: no variable.")`（`:2400`）。
- 可选参数：无。
- 语义：返回最近一次「消息处理」的结果：`layermessage` 的 `handler->message()` 返回值（`:1978`）或 `exec_dll` 的返回值（`:2794-2804`）；`lrclick` 之后为 0 / 1（`:3244`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:2388-2403`；写入点 `:1978`、`:2794-2804`、`:3244`；字段 `ONScripter.h:361-362`。

### `getsavestr`
- 处理函数：`getsavestrCommand` — `ONScripter_command.cpp:2356`
- 注册：`ONScripter_lut.cpp:265`
- 语法：`getsavestr $结果, <存档号>`
- 参数：
  - P1：目标**字符串**变量（`readVariable()`，`:2358`）；非字符串 `errorAndExit("getsavestr: no string variable.")`（`:2359-2360`）。
  - P2：存档号（`readInt()`，`:2364`），用于拼 `save<号>.dat`。
- 可选参数：无。
- 语义：`readSaveStrFromFile(no)`（`ONScripter_file.cpp:180-204`）读取 `save%d.dat`，从文件尾部倒数定位最后一个 `"…"*` 字段并取出引号内字符串；失败返回 `NULL`，`setStr` 置空（`:2367`）。
- 坐标：**否**。P1 变量、P2 存档编号，均非坐标。
- 证据：`ONScripter_command.cpp:2356-2371`；`ONScripter_file.cpp:180-204`。

### `getscreenshot`
- 处理函数：`getscreenshotCommand` — `ONScripter_command.cpp:2333`
- 注册：`ONScripter_lut.cpp:264`
- 语法：`getscreenshot <宽>, <高>`
- 参数：
  - P1：截图输出宽（`readInt()`，`:2335`，单位 px）；若 `disable_rescale_flag` 则先做 `* screen_ratio1 / screen_ratio2`（`:2336`）；为 0 时改为 1（`:2339`）。
  - P2：截图输出高（`readInt()`，`:2337`）；同样换算（`:2338`）、0→1（`:2340`）。
- 可选参数：无。
- 语义：结果写入 `screenshot_w` / `screenshot_h`（`:2342-2343`），并把当前累积画面 `accumulation_surface` 拷到 `screenshot_surface`（按 `screen_width × screen_height` 分配，`:2346-2351`）。后续 `savescreenshot` 按该尺寸 `resizeSurface` 输出（`:975-980`）。初始值即 `screen_width` / `screen_height`（`ONScripter.cpp:550-551`）。
- 坐标：**是**。P1/P2 都是像素尺寸，且直接参与 `screen_ratio` 换算与 `screen_width/height` 的目标面分配。
- 证据：`ONScripter_command.cpp:2333-2354`；消费点 `:968-980`；初值 `ONScripter.cpp:550-551`；开关 `ONScripter.cpp:498-501`、`onscripter_main.cpp:451-453`。

### `getsevol`
- 处理函数：`getsevolCommand` — `ONScripter_command.cpp:2326`
- 注册：`ONScripter_lut.cpp:263`
- 语法：`getsevol %音量`
- 参数：
  - P1：结果整型变量（`readInt()`，`:2328`）→ `se_volume`（`:2329`）。
- 可选参数：无。
- 语义：返回 SE 音量，0–100，默认 100（`ONScripter.h:64`）；播放时按 `se_volume * MIX_MAX_VOLUME / 100` 应用（`ONScripter_sound.cpp:190`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:2326-2331`；`ONScripter_sound.cpp:188-190`。

### `getspmode`
- 处理函数：`getspmodeCommand` — `ONScripter_command.cpp:2315`
- 注册：`ONScripter_lut.cpp:262`
- 语法：`getspmode %结果, <精灵号>`
- 参数：
  - P1：结果整型变量（`readVariable()` + `pushVariable()`，`:2317-2318`）。
  - P2：精灵号（`readInt()`，`:2320`），索引 `sprite_info[]`。
- 可选参数：无。
- 语义：写入 `sprite_info[no].visible ? 1 : 0`（`:2321`），即精灵是否可见。
- 坐标：**否**。输出是可见标志（0/1），不是坐标。
- 证据：`ONScripter_command.cpp:2315-2324`。
- 备注（待验证）：未校验 `no` 范围即下标访问（`:2321`），越界为未定义行为。

### `getsppos`
- 处理函数：`getspposCommand` — `ONScripter_command.cpp:2302`
- 注册：`ONScripter_lut.cpp:261`
- 语法：`getsppos <精灵号>, %x, %y`
- 参数：
  - P1：精灵号（`readInt()`，`:2304`）。
  - P2：x 结果变量（`readVariable()`，`:2306`）→ `sprite_info[no].orig_pos.x`（`:2307`）。
  - P3：y 结果变量（`readVariable()`，`:2309`）→ `sprite_info[no].orig_pos.y`（`:2310`）。
- 可选参数：无。
- 语义：返回精灵的**逻辑**原始位置；绘制时按 `pos = orig_pos * screen_ratio1 / screen_ratio2` 换算（`AnimationInfo.h:119-122`）。
- 坐标：**是**。P2/P3 为逻辑坐标（px）；P1 是精灵编号（非坐标）。
- 证据：`ONScripter_command.cpp:2302-2313`；`AnimationInfo.h:119-122`。
- 备注（待验证）：未校验 `no` 范围。

### `getspsize`
- 处理函数：`getspsizeCommand` — `ONScripter_command.cpp:2286`
- 注册：`ONScripter_lut.cpp:260`
- 语法：`getspsize <精灵号>, %w, %h[, %cells]`
- 参数：
  - P1：精灵号（`readInt()`，`:2288`）。
  - P2：宽结果变量（`readVariable()`，`:2290`）→ `orig_pos.w`（`:2291`）。
  - P3：高结果变量（`readVariable()`，`:2292`）→ `orig_pos.h`（`:2293`）。
  - P4（可选）：单元格数结果变量（`:2294-2297`，仅当 `END_COMMA` 时读取）→ `num_of_cells`。
- 可选参数：P4。
- 语义：`orig_pos.w` 是整条位图的宽度（含所有 cell），`orig_pos.h` 是单格高度（`AnimationInfo.cpp:1075-1079`）；单格宽 = `orig_pos.w / num_of_cells`（同 `LUAHandler.cpp:748` 的取法）。
- 坐标：**部分**。P2/P3 是像素尺寸（逻辑坐标）；P4 是单元格计数，不是坐标。
- 证据：`ONScripter_command.cpp:2286-2300`；`AnimationInfo.cpp:1075-1079`；`LUAHandler.cpp:748-750`。
- 备注（待验证）：未校验 `no` 范围。

### `gettab`
- 处理函数：`gettabCommand` — `ONScripter_command.cpp:2279`
- 注册：`ONScripter_lut.cpp:259`
- 语法：`gettab`（无参数）
- 参数：无。
- 可选参数：无。
- 语义：置 `gettab_flag = true`（`:2281`），Tab 键返回按钮码 `-20`（`ONScripter_event.cpp:948-950`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:2279-2284`；`ONScripter_event.cpp:948-950`。

### `gettag`
- 处理函数：`gettagCommand` — `ONScripter_command.cpp:2215`
- 注册：`ONScripter_lut.cpp:258`
- 语法：`gettag <变量1>[, <变量2>[, …]]`
- 参数：**变长**，按源码顺序 P1…Pn 均为接收变量；逐项从 `pretext_buf` 里切一段（`:2230-2268`）：
  - P_k 为整型 / 数组：`script_h.parseInt(&buf)` 解析成整数（`:2235-2240`）；`buf` 为空时写 0。
  - P_k 为字符串：取到 `/`、`]`、`】` 或串尾之前的片段（`:2242-2256`）；`buf` 为空时置空串。
- 可选参数：无固定数量，参数表长度可变（`END_COMMA`，`:2268`）。
- 语义：必须在子程序内（`LABEL` 嵌套，`:2217-2218`，错误信息即提示 `pretextgosub`）。`pretext_buf` 由文本行首 `【…】` 触发 `pretextgosub` 时指向该行（`ONScripter_text.cpp:747`）。解析前去掉 `[` 或全角 `【` 前缀（`:2220-2227`），每项以 `/` 分隔（`:2262-2266`），结束后跳过尾部 `]` / `】`（`:2270-2274`）。
- 坐标：**否**。全部参数是变量引用，取的是标签文本片段。
- 证据：`ONScripter_command.cpp:2215-2277`；`ONScripter_text.cpp:730-752`；`pretextgosubCommand` `ScriptParser_command.cpp:479-487`。
- 备注（待验证）：若 `pretext_buf` 为 `NULL`（例如在普通 `gosub` 里调用），`:2220-2222` 会直接解引用空指针；源码未做保护。

### `gettaglog`
- 处理函数：`gettaglogCommand` — `ONScripter_command.cpp:2194`
- 注册：`ONScripter_lut.cpp:257`
- 语法：`gettaglog $结果, <页号>`
- 参数：
  - P1：目标**字符串**变量（`readVariable()` + `pushVariable()`，`:2196-2197`）。
  - P2：页号（`readInt()`，`:2199`）；`0` = 当前页，逐页回溯（`:2201-2205`）。
- 可选参数：无。
- 语义：返回该页保存的标签文本 `page->tag`（`:2207-2208`），无标签则置空（`:2209-2210`）。tag 在 `pretextgosub` 触发时由行首 `【…】` 内容写入 `current_page->tag`（`ONScripter_text.cpp:735-744`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:2194-2213`；`ONScripter_text.cpp:735-744`。
- 备注：与 `getlog` 不同，这里**没有** `page_no > 0` 的溢出判断，页号超过历史时返回 `start_page` 的 tag（`:2201-2208`）。

### `gettext`
- 处理函数：`gettextCommand` — `ONScripter_command.cpp:2175`
- 注册：`ONScripter_lut.cpp:256`
- 语法：`gettext $结果`
- 参数：
  - P1：目标字符串变量（`readStr()` 后取 `current_variable.var_no`，`:2177-2178`）。
- 可选参数：无。
- 语义：复制当前页文本 `current_page->text`，剔除所有换行 `0x0a`（`:2180-2188`），结果写入 P1。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:2175-2192`。
- 备注（待验证）：未校验 P1 是否为字符串变量（对比 `getsavestr` / `getreg` 的显式检查）。

### `gettimer` / `getbtntimer`
- 处理函数：`gettimerCommand` — `ONScripter_command.cpp:2153`
- 注册：`ONScripter_lut.cpp:255`（`gettimer`）、`:286`（`getbtntimer`）
- 语法：`gettimer %毫秒` / `getbtntimer %毫秒`
- 参数：
  - P1：结果整型变量（`readInt()`，`:2163`）。
- 可选参数：无。
- 语义：
  - `gettimer`：写 `SDL_GetTicks() - internal_timer`（`:2166`），即自 `resettimer` 起的毫秒数；`internal_timer` 由 `resettimer`（`:1106-1111`）与 reset 流程（`ONScripter.cpp:651`）设定。
  - `getbtntimer`：写 `btnwait_time`（`:2169`），即上一次 `btnwait` 的等待耗时（毫秒，`:3480`；复位 `ONScripter.cpp:695`）。
- 坐标：**否**。P1 是时间量。
- 证据：`ONScripter_command.cpp:2153-2173`；`:1106-1111`；`:3480`；别名注册 `ONScripter_lut.cpp:286`。

### `getversion`
- 处理函数：`getversionCommand` — `ONScripter_command.cpp:2145`
- 注册：`ONScripter_lut.cpp:254`
- 语法：`getversion %结果`
- 参数：
  - P1：结果整型变量（`readInt()`，`:2147`）→ `NSC_VERSION`（`:2148`）。
- 可选参数：无。
- 语义：返回 NScripter 兼容版本号；本快照 `NSC_VERSION = 296`（`version.h:4`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:2145-2151`；`version.h:4`。

### `getvoicevol`
- 处理函数：`getvoicevolCommand` — `ONScripter_command.cpp:2138`
- 注册：`ONScripter_lut.cpp:253`
- 语法：`getvoicevol %音量`
- 参数：
  - P1：结果整型变量（`readInt()`，`:2140`）→ `voice_volume`（`:2141`）。
- 可选参数：无。
- 语义：返回语音音量，0–100，默认 100；应用 `voice_volume * MIX_MAX_VOLUME / 100`（`ONScripter_sound.cpp:188`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:2138-2143`；`ONScripter_sound.cpp:188`。

### `getzxc`
- 处理函数：`getzxcCommand` — `ONScripter_command.cpp:2131`
- 注册：`ONScripter_lut.cpp:252`
- 语法：`getzxc`（无参数）
- 参数：无。
- 可选参数：无。
- 语义：置 `getzxc_flag = true`（`:2133`），使 Z / X / C 键分别返回按钮码 `-51` / `-52` / `-53`（`ONScripter_event.cpp:970-978`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:2131-2136`；`ONScripter_event.cpp:970-978`。

### `globalon`
- 处理函数：`globalonCommand` — `ScriptParser_command.cpp:1065`
- 注册：`ONScripter_lut.cpp:251`
- 语法：`globalon`（无参数）
- 参数：无。
- 可选参数：无。
- 语义：仅允许在 `define` 段使用（`:1067-1068`）；置 `globalon_flag = true`（`:1069`），使 `saveGlovalData()` 把全局变量区（`global_variable_border` … `variable_range`）写入 `gloval.sav`（`ScriptParser.cpp:327-338`）。
- 坐标：**否**。
- 证据：`ScriptParser_command.cpp:1065-1072`；`ScriptParser.cpp:327-338`；字段 `ScriptParser.h:244`。

### `gosub`
- 处理函数：`gosubCommand` — `ScriptParser_command.cpp:1057`
- 注册：`ONScripter_lut.cpp:250`
- 语法：`gosub *标签`
- 参数：
  - P1：标签字符串（`readStr()`，`:1059`）；源码做 `buf+1` 跳过前导 `*`（`:1060`）。
- 可选参数：无。
- 语义：`gosubReal(label, script_h.getNext())`（`:1060`）建立一层 `NestInfo`（默认 `nest_mode = LABEL`，`ScriptParser.h:206-210`），记录返回地址 `next_script`，再 `setCurrentLabel()` 跳转（`:1045-1055`）；由 `return` 返回（`:449-477`）。
- 坐标：**否**。
- 证据：`ScriptParser_command.cpp:1038-1063`；`gosubReal` `:1045-1055`；`NestInfo` `ScriptParser.h:197-211`。

### `goto`
- 处理函数：`gotoCommand` — `ScriptParser_command.cpp:1038`
- 注册：`ONScripter_lut.cpp:249`
- 语法：`goto *标签`
- 参数：
  - P1：标签字符串（`readStr()`，`:1040`），源码 `+1` 跳过 `*`（`:1040`）。
- 可选参数：无。
- 语义：`setCurrentLabel(buf+1)` 直接跳转，不保存返回点（与 `gosub` 的区别）。
- 坐标：**否**。
- 证据：`ScriptParser_command.cpp:1038-1043`。

### `humanorder`
- 处理函数：`humanorderCommand` — `ONScripter_command.cpp:2107`
- 注册：`ONScripter_lut.cpp:247`
- 语法：`humanorder <三字符>`
- 参数：
  - P1：3 字符字符串（`readStr()`，`:2111`）；逐位映射 `l`→0、`c`→1、`r`→2、其它→`-1`，写入 `human_order[0..2]`（`:2113-2118`）。
- 可选参数：无。
- 语义：决定 3 个立绘槽位（左 / 中 / 右）的绘制顺序；默认 `"rcl"` ≈ `{2,1,0}`（`ONScripter.cpp:759`），绘制时按 `human_order[2-i]` 取槽位（`ONScripter_image.cpp:735-736`）。随后把可见立绘矩形加入脏区并解析特效（`:2120-2126`）。
- 坐标：**否**。P1 是排序字符串；内部虽访问 `tachi_info[i].pos`（`:2122`），但没有坐标参数，也不改变坐标。
- 证据：`ONScripter_command.cpp:2107-2129`；`ONScripter_image.cpp:723-736`；默认值 `ONScripter.cpp:759`。
- 备注（待验证）：直接读取 `buf[0..2]` 而不校验字符串长度（`:2114-2117`），短串可能读到终止符之后。

### `humanz`
- 处理函数：`humanzCommand` — `ScriptParser_command.cpp:1031`
- 注册：`ONScripter_lut.cpp:246`
- 语法：`humanz <层号>`
- 参数：
  - P1：`z_order`（`readInt()`，`:1033`），立绘绘制层上限。
- 可选参数：无。
- 语义：设置立绘绘制的起始层号，默认 499（`ScriptParser.cpp:157`）；绘制循环从 `z_order` 向下进行（`ONScripter_image.cpp:723-763`）。
- 坐标：**否**。P1 是层号 / 编号，不是像素坐标。
- 证据：`ScriptParser_command.cpp:1031-1036`；`ONScripter_image.cpp:723-763`；默认值 `ScriptParser.cpp:157`。

### `if` / `notif`
- 处理函数：`ifCommand` — `ScriptParser_command.cpp:912`
- 注册：`ONScripter_lut.cpp:244`（`if`）、`:149`（`notif`）
- 语法：
  - `if <左值> <运算符> <右值>`
  - `if fchk "文件名"`
  - `if lchk *标签`
  - 组合：`if <条件> & <条件> …`（与）、`if <条件> | <条件> …`（或），两者不可混用（`:1004-1005`、`:1017-1018`）
- 参数：无逗号分隔的固定参数表；按表达式语法就地从脚本流读取（`:923-998`）：
  - 整型 / 数组左值：`readInt()` 取值，再按紧随的运算符比较右值 `readInt()`（`:937-966`）。
  - 字符串左值：`readStr()` 取左串，`readStr()` 取右串，用 `strcmp` 比较（`:968-997`）。
  - 运算符集合：`>=` `<=` `==` `!=` `<>` `<` `>` `=`（`:944-953`、`:975-984`；判定 `:959-966`、`:989-996`）。
  - `fchk`：`readLabel()` + `readStr()`，查文件日志（`:924-929`）。
  - `lchk`：`readLabel()` + `readStr()`，查标签日志，取 `buf+1` 跳过 `*`（`:930-935`）。
- 可选参数：无。
- 语义：条件为真继续执行本行后续命令；为假返回 `RET_SKIP_LINE` 跳过本行（`:1012-1014`）。`notif` 对每个条件取反（`:921`、`:1000`）。`&` 逐个求值、遇假即跳过；`|` 用 `condition_flag` 累积或结果，全部为假才跳过（`:1000-1023`）。
- 坐标：**否**。所有参数是数值 / 字符串 / 文件名 / 标签，不含像素量。
- 证据：`ScriptParser_command.cpp:912-1029`；`RET_SKIP_LINE` 定义 `ScriptParser.h:225-230`。
- 备注：`&` 与 `|` 混用会直接 `errorAndExit`（`:1005`、`:1018`）。

### `inc`
- 处理函数：`incCommand` — `ScriptParser_command.cpp:904`
- 注册：`ONScripter_lut.cpp:243`
- 语法：`inc %变量`
- 参数：
  - P1：目标整型变量（`readInt()` 取出当前值，`:906`），写回 `val + 1`（`:907`）。
- 可选参数：无。
- 语义：自增 1，等价于 `add %var, 1`。
- 坐标：**否**。
- 证据：`ScriptParser_command.cpp:904-910`。

### `indent`
- 处理函数：`indentCommand` — `ONScripter_command.cpp:2100`
- 注册：`ONScripter_lut.cpp:242`
- 语法：`indent <全角字符数>`
- 参数：
  - P1：`indent_offset`（`readInt()`，`:2102`），单位是**全角字符数**：排版时每单位执行一次 `advanceCharInHankaku(2)`（`:2599-2600`；`ONScripter_text.cpp:206`、`:330`）。
- 可选参数：无。
- 语义：设置换行后首行缩进的字符数；初值 0（`ONScripter.cpp:776`，另见 `:713`、`:746`、`:1882` 的复位）。
- 坐标：**否**。P1 是字符计数而非像素；它间接影响文本 x 位置，但不是坐标参数，做 ×2 转换时**不应**缩放该值。
- 证据：`ONScripter_command.cpp:2100-2105`；`ONScripter_text.cpp:206`、`:330`；复位点 `ONScripter.cpp:776`。

### `input`
- 处理函数：`inputCommand` — `ONScripter_command.cpp:2074`
- 注册：`ONScripter_lut.cpp:241`
- 语法：`input $变量, <说明>, <默认值>, <最大长度>, <宽字符标志>[, <窗口宽>, <窗口高>, <文本框宽>, <文本框高>]`
- 参数：
  - P1：目标**字符串**变量（`readStr()`，`:2076`）；非字符串 `errorAndExit("input: no string variable.")`（`:2078-2079`）。
  - P2：说明文字（`readStr()`，`:2082`）——**读后丢弃**。
  - P3：默认值（`readStr()`，`:2083`），写入 P1（`:2084`）。
  - P4：最大长度（`readInt()`，`:2088`）——读后丢弃。
  - P5：宽字符标志（`readInt()`，`:2089`）——读后丢弃。
  - P6（可选）：窗口宽（`readInt()`，`:2091`）——读后丢弃。
  - P7（可选）：窗口高（`readInt()`，`:2092`）——读后丢弃。
  - P8（可选）：文本框宽（`readInt()`，`:2093`）——读后丢弃。
  - P9（可选）：文本框高（`readInt()`，`:2094`）——读后丢弃。
- 可选参数：P6–P9，仅当检测到逗号时读取（`:2090`）。
- 语义：本实现是**桩**：只把默认值写入变量，其余参数读完即弃（`:2082-2095`）；未见交互式输入实现（事件层的 `variable_edit_mode` 属于 `-edit` 调试编辑器，见 `ONScripter_event.cpp:516-640`）。
- 坐标：**部分**。P1–P5 不是坐标（变量、文本、长度、标志）；P6–P9 在语法上是窗口 / 文本框的像素宽高，但源码读完即丢弃，不写入任何坐标字段，也不参与像素运算。
- 证据：`ONScripter_command.cpp:2074-2098`。
- 备注（待验证）：P6–P9 实际无任何效果；若按语义移植到其它引擎，这 4 个参数需按坐标处理。

### `intlimit`
- 处理函数：`intlimitCommand` — `ScriptParser_command.cpp:890`
- 注册：`ONScripter_lut.cpp:240`
- 语法：`intlimit %变量, <下限>, <上限>`
- 参数：
  - P1：变量号（`readInt()`，`:895`）。
  - P2：下限（`readInt()`，`:898`）→ `num_limit_lower`。
  - P3：上限（`readInt()`，`:899`）→ `num_limit_upper`。
- 可选参数：无。
- 语义：仅限 `define` 段（`:892-893`）；置 `num_limit_flag` 并设上下限（`:897-899`），之后写入该变量时由 `setNumVariable` 钳制到 `[lower, upper]`（`ScriptHandler.cpp:781-784`）。
- 坐标：**否**。
- 证据：`ScriptParser_command.cpp:890-902`；`ScriptHandler.cpp:778-786`；字段 `ScriptHandler.h:220-232`。

### `isdown`
- 处理函数：`isdownCommand` — `ONScripter_command.cpp:2057`
- 注册：`ONScripter_lut.cpp:239`
- 语法：`isdown %结果`
- 参数：
  - P1：结果整型变量（`readInt()`，`:2059`）。
- 可选参数：无。
- 语义：`current_button_state.down_flag` 为真写 1，否则 0（`:2066-2069`）。iOS / Android / WinRT 下多指触摸（`num_fingers > 1`）时强制按「未按下」处理（`:2061-2064`）。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:2057-2072`；位置记录 `ONScripter_event.cpp:387-389`。

### `isfull`
- 处理函数：`isfullCommand` — `ONScripter_command.cpp:2035`
- 注册：`ONScripter_lut.cpp:238`
- 语法：`isfull %结果`
- 参数：
  - P1：结果整型变量（`readInt()`，`:2037`）→ `fullscreen_mode ? 1 : 0`（`:2038`）。
- 可选参数：无。
- 语义：查询当前是否全屏。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:2035-2041`。

### `ispage`
- 处理函数：`ispageCommand` — `ONScripter_command.cpp:2023`
- 注册：`ONScripter_lut.cpp:237`
- 语法：`ispage %结果`
- 参数：
  - P1：结果整型变量（`readInt()`，`:2025`）。
- 可选参数：无。
- 语义：若 `textgosub_clickstr_state == CLICK_NEWPAGE` 写 1，否则 0（`:2027-2030`）。`CLICK_NEWPAGE = 2`（`ScriptParser.h:231-235`）；该状态在文本换页触发 `textgosub` 时置位（`ONScripter_text.cpp:604`），供 `texec`（`:295-307`）与 `ispage` 判断本次进入文本子程序的原因是否为「换页」。
- 坐标：**否**。
- 证据：`ONScripter_command.cpp:2023-2033`；`ONScripter_text.cpp:601-610`；`ScriptParser.h:231-235`。
- 备注：同一状态还被 `texec` 与 `return` 使用（`ONScripter_command.cpp:297-302`、`ScriptParser_command.cpp:470-474`）；在已检索的源码中未发现 `textgosub_clickstr_state` 的其它写入点，其复位时机待验证。

---

## 本册统计

> 说明：本册 `get*`/`is*` 类命令的「坐标」指其**返回值**处于坐标系中，它们**没有坐标输入参数**，转换器不得修改（见附录 B.2）。`input` 的 P6–P9 虽在语法上是像素尺寸，但实现读后丢弃，不构成有效坐标参数。

- 处理函数：**43 个**
- 命令条目：**49 条**（含别名与重复注册）
- 其中含坐标参数（7 条）：
  - 纯坐标 / 尺寸（**是**）：`getcursorpos`、`getcursorpos2`、`getmousepos`、`getsppos`、`getscreenshot`
  - 部分坐标（**部分**）：`getspsize`（P2/P3 宽高为坐标，P4 单元格数不是）、`input`（P6–P9 语法上是窗口 / 文本框像素尺寸，但实现读后丢弃）
- 未定位：**0**
- 待验证：见各节备注（`getlog` 的 UTF-8 循环、`gettag` 的空 `pretext_buf`、`getreg` 的 256 字节缓冲、`humanorder` 的短串、`getparam` 的实参个数不匹配、`input` 的桩实现、`ispage` 的状态复位）。
