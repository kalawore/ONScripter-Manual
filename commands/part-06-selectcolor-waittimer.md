# 命令分册 06：处理函数 `selectcolorCommand` – `waittimerCommand`

> 本册覆盖 `func_lut` 中处理函数位于 `selectcolorCommand`…`waittimerCommand` 区间的全部命令（含别名），共 **51 个处理函数 / 60 条命令名**。命令按名称字母序排列，同一处理函数的别名合并为一个标题。
> 行号均指快照 `src/onsyuri/`；未特别注明文件时，`ONScripter_command.cpp` 指 `src/onsyuri/ONScripter_command.cpp`，`ScriptParser_command.cpp` 指 `src/onsyuri/ScriptParser_command.cpp`。
> 参数按**源码读取顺序**列出，逗号分隔；`[ ]` 表示可选（由 `script_h.getEndStatus() & END_COMMA` 或源码显式判断）。未标 `[ ]` 的参数在源码中**无条件读取**，缺少即属脚本错误。
> 「坐标」：**是** = 含逻辑坐标/尺寸参数；**否** = 不含；**部分** = 仅部分参数是坐标。判定依据：参数是否写入 `orig_pos.x/y/w/h`、`pos`、`image_rect`、`top_xy`、`num_xy`、`font_size_xy`、`pitch_xy`、`max_width`、`dst_rect/src_rect` 等坐标字段，或与 `screen_width/height` 做像素运算；时间/速度/音量/透明度/颜色/编号/标志均判为否。
> 数值参数统一由 `readInt()` 读取，因而支持 `%变量`、数组元素与算式（`ScriptHandler.cpp:384-400`、`:1550-1575`）；颜色参数统一由 `readColor()` 读取，**必须**为 `#RRGGBB`（`ScriptParser.cpp:289-294`）。标签参数由 `readLabel()` 读取，可带前导 `*`，名称中的 ASCII 大写会转小写（`ScriptHandler.cpp:318-355`）。
> 本快照 `screen_ratio1 = screen_ratio2 = 1`（`ONScripter.cpp:203-204`、`ScriptParser.cpp:51-52`），源码中所有 `* screen_ratio1 / screen_ratio2` 实际为恒等；本文仍按「脚本像素」描述。若按 640×480 脚本 → 1280×960 画面换算，像素类参数需 ×2，字符数/编号/时间不换算。
> 说明：`func_lut` 全表（`ONScripter_lut.cpp:27-403`）中**不存在** `textwindow` 命令，故本册不设该节，也不虚构其参数。

---

## S

### `selectcolor`
- 处理函数：`selectcolorCommand` — `ScriptParser_command.cpp:317`
- 语法：`selectcolor <选中色>, <普通色>`
- 参数：
  - P1 `on_color`：选中/高亮项颜色，`#RRGGBB`，写入 `sentence_font.on_color`。
  - P2 `off_color`：普通项颜色，`#RRGGBB`，写入 `sentence_font.off_color`。
- 可选参数：无（两色均无条件读取）。
- 坐标：**否**（P1/P2 均为颜色值；无坐标/尺寸参数）。
- 语义：设置 `select` 系列选项文本的两态颜色——内部为 `color_list[0]=off_color`、`color_list[1]=on_color`（`ONScripter.cpp:1350-1355`）；文件不存在时另用 `nofile_color`。本命令不受 `define` 段限制。
- 证据：`ScriptParser_command.cpp:317-326`（P1 `:319-320`，P2 `:322-323`）；`ONScripter.cpp:1350-1355`。

### `selectvoice`
- 处理函数：`selectvoiceCommand` — `ScriptParser_command.cpp:306`
- 语法：`selectvoice <打开音>, <悬停音>, <选择音>`
- 参数：
  - P1 `selectvoice_file_name[SELECTVOICE_OPEN]`：选项列表打开时的音效文件名。
  - P2 `selectvoice_file_name[SELECTVOICE_OVER]`：鼠标移入选项时的音效文件名。
  - P3 `selectvoice_file_name[SELECTVOICE_SELECT]`：确认选择时的音效文件名。
- 可选参数：无；循环固定读取 `SELECTVOICE_NUM = 3` 个字符串（`ScriptParser.h:420-425`）。
- 坐标：**否**（P1–P3 均为文件名）。
- 备注：仅允许在 `define` 段使用（`:308-309`）。
- 证据：`ScriptParser_command.cpp:306-315`（循环 `:311-312`）；`ScriptParser.h:420-425`。

### `setcursor` / `abssetcursor`
- 处理函数：`setcursorCommand` — `ONScripter_command.cpp:755`
- 语法：
  - `setcursor <编号>, <图片名>, <x>, <y>`
  - `abssetcursor <编号>, <图片名>, <x>, <y>`
- 参数：
  - P1 `no`：光标编号。`0` = 点击等待光标（CLICK_WAIT），`1` = 换页光标（CLICK_NEWPAGE）；`cursor_info` 数组仅 2 项（`ONScripter.h:474`），源码**未做范围检查**，传入 >1 会越界写入。
  - P2 `file`：光标图片文件名（`readStr()` 后 `saveStringBuffer()` 暂存）。空串不会回退到默认图，而是按空文件名加载失败并移除光标；默认光标只能由内部 `loadCursor(0,NULL,…)` 设置（`ONScripter_animation.cpp:479-500`）。
  - P3 `x`：像素 X。`abssetcursor` 时为屏幕绝对坐标；`setcursor` 时为**相对当前文本位置**的偏移（绘制时 `+= sentence_font.x()`）。
  - P4 `y`：像素 Y，语义同 P3。
- 可选参数：无。
- 坐标：**是**（P3/P4 为像素坐标；P1 为编号、P2 为文件名，均非坐标）。相对/绝对由命令名决定（`:759-764`）。
- 证据：`ONScripter_command.cpp:755-775`（P1 `:766`，P2 `:767-768`，P3/P4 `:769-770`，调用 `:772`）；`ONScripter_animation.cpp:442-445`（相对偏移）、`:459-470`（0/1 编号与相对偏移）、`:475-505`（loadCursor）；`ONScripter.h:474`。

### `setkinsoku`
- 处理函数：`setkinsokuCommand` — `ScriptParser_command.cpp:293`
- 语法：`setkinsoku <行首禁则字符集>, <行尾禁则字符集>`
- 参数：
  - P1 `start_chrs`：行首禁则字符集合（字符串，逐字符解析，双字节字符按 2 字节处理）。
  - P2 `end_chrs`：行尾禁则字符集合（同上）。
- 可选参数：无。
- 坐标：**否**（P1/P2 为字符集）。
- 语义：以 `add=false` 调用 `setKinsoku()`，即**替换**两套禁则表（先释放旧表）；追加式设置请用 `addkinsoku`。仅允许 `define` 段（`:295-296`）。
- 证据：`ScriptParser_command.cpp:293-304`（P1/P2 `:298-301`）；`ScriptParser.cpp:759-845`（`add=false` 分支 `:789-794`、`:821-826`）。

### `setlayer`
- 处理函数：`setlayerCommand` — `ScriptParser_command.cpp:252`
- 语法：`setlayer <层号>, <间隔毫秒>, <DLL 名>`
- 参数：
  - P1 `no`：层编号，`layer_info[]` 共 `MAX_LAYER_NUM = 32` 项（`builtin_layer.h:29`），源码**未做范围检查**（`:266`），应取 0–31。
  - P2 `interval`：更新间隔，写入 `LayerInfo::interval`（`Uint32`，毫秒；动画推进时 `next_time += interval`）。
  - P3 `dll`：效果名/路径字符串。仅识别 `oldmovie.dll`（旧电影噪点）、`snow.dll`（雪）、`hana.dll`（花瓣）；先用 `strrchr(dll,'\\')` 取反斜杠后的基名再比较，**大小写敏感**；含正斜杠路径的整串不会被剥离，因而匹配失败。
- 可选参数：无。
- 坐标：**否**（P1 编号、P2 时间、P3 文件名；层内部使用 `screen_width/height` 不来自脚本参数）。
- 语义：替换该层已有 handler（先 `delete` 再新建）；未识别的 DLL 打印 `not implemented` 后忽略。本构建在 `CMakeLists.txt:520-524` 定义了 `USE_BUILTIN_LAYER_EFFECTS`，故该分支生效；未定义该宏的构建只打印错误并返回。仅允许 `define` 段（`:254-255`）。
- 证据：`ScriptParser_command.cpp:252-291`（参数 `:257-259`，无支持分支 `:261-264`，三种效果 `:271-284`）；`builtin_layer.h:29`、`:47-64`；`CMakeLists.txt:520-524`；`AnimationInfo.cpp:228-238`（interval 推进）。

### `setwindow`
- 处理函数：`setwindowCommand` — `ONScripter_command.cpp:741`（公共参数体 `setwindowCore()` `:660`）
- 语法：
  - 颜色窗口：`setwindow <top_x>,<top_y>,<num_x>,<num_y>,<font_x>,<font_y>,<pitch_x>,<pitch_y>,<wait>,<bold>,<shadow>,#RRGGBB,<x>,<y>,<right>,<bottom>`
  - 图片窗口：`setwindow <top_x>,<top_y>,<num_x>,<num_y>,<font_x>,<font_y>,<pitch_x>,<pitch_y>,<wait>,<bold>,<shadow>,<图名>,<x>,<y>`
- 参数：以下顺序即 `setwindowCore()` 读取顺序。
  - P1 `top_x`：文本区左上角 X（像素）→ `sentence_font.top_xy[0]`。
  - P2 `top_y`：文本区左上角 Y（像素）→ `top_xy[1]`。
  - P3 `num_x`：每行可容纳的全角**字符数** → `num_xy[0]`；不 ×2（半角按半个字符计，`FontInfo.cpp:233-237`）。
  - P4 `num_y`：文本**行数** → `num_xy[1]`；不 ×2。
  - P5 `font_x`：字号宽（像素）→ `font_size_xy[0]`。
  - P6 `font_y`：字号高（像素）→ `font_size_xy[1]`。
  - P7 `pitch_x`：字间距**增量**（像素）；`pitch_xy[0] = P7 + font_size_xy[0]`，可为负。
  - P8 `pitch_y`：行间距**增量**（像素）；`pitch_xy[1] = P8 + font_size_xy[1]`。
  - P9 `wait_time`：每字符等待毫秒 → `sentence_font.wait_time`；`-1` 用默认速度档、`0` 不等、`>0` 为毫秒。
  - P10 `bold`：非 0 为粗体。
  - P11 `shadow`：非 0 为阴影。
  - P12 `window`：以首字符是否为 `#` 二选一：
    - 以 `#` 开头：透明窗口颜色 `#RRGGBB`，并 `sentence_font_info.remove()` 移除窗口图；
    - 否则：窗口图文件名（可带 `:` 开头的图片标签，交给 `parseTaggedString()`）。
  - 颜色分支（P12 为 `#…`）P13 `x`、P14 `y`：窗口矩形左上角（像素，`orig_pos.x/y`）；P15 `right`、P16 `bottom`：窗口矩形右下角（像素，**含端点**，宽高按 `right-x+1`、`bottom-y+1` 计算）。
  - 图片分支 P13 `x`、P14 `y`：窗口图左上角（像素）；宽高取图片自身尺寸。
- 可选参数：无。P13 起按 P12 分支固定读取 4 个（颜色）或 2 个（图片）整数，源码无条件读取，必须给足。
- 坐标：**是**（P1/P2、P5–P8、P13–P16 均为像素；P3/P4 为字符数；P9 为时间；P10/P11 为标志；P12 为字符串）。
- 转换提示：`num_xy` 是字符数（列/行），**不可 ×2**；`top_xy`、`font_size_xy`、`pitch_xy`（含 P7/P8 增量）及窗口矩形为像素，640×480 → 1280×960 时需 ×2。注意 `setwindowCore()` 只对窗口图调用 `scalePosXY/scalePosWH`，`top_xy/num_xy/font_size/pitch` 是直接赋值（`:664-671`）。
- 语义：设置文本窗口字体与区域后，`lookbackflushCommand()` 清空历史页（`:745`），重置缩进/回车状态与显示模式，并以 `sentence_font_info.pos` 全屏 `flush`。`ttf_font[0..1]` 先被置 NULL（`:662-663`），使字号变更后重新打开字体；末尾记录 `sentence_font.old_xy`（`:704-705`）。
- 证据：`ONScripter_command.cpp:741-753`；`setwindowCore()` `:660-706`（P1/P2 `:664-665`，P3/P4 `:666-667`，P5/P6 `:668-669`，P7/P8 `:670-671`，P9 `:672`，P10/P11 `:673-674`，P12 `:676`，颜色分支 `:680-691`，图片分支 `:692-702`，old_xy `:704-705`）；`FontInfo.h:49-55`；`FontInfo.cpp:183-197`、`:233-237`；`ONScripter.cpp:1231`（页缓冲按 `num_xy` 计算）。

### `setwindow2`
- 处理函数：`setwindow2Command` — `ONScripter_command.cpp:722`
- 语法：
  - `setwindow2 #RRGGBB`（透明窗口色）
  - `setwindow2 <窗口图名>`
- 参数：
  - P1 `buf`：唯一参数。`buf[0]=='#'` 时作为窗口颜色（透明窗口）并移除窗口图；否则作为窗口图文件名（可带 `:` 标签，经 `parseTaggedString()`/`setupAnimationInfo()`）。
- 可选参数：无。
- 坐标：**否**（P1 为颜色或图片名，不含独立坐标参数；图片分支位置/尺寸未由本命令指定）。
- 语义：只更换窗口外观，不改字体、文本区、页内容；末尾 `repaintCommand()` 全屏重绘（`:736`，`repaintCommand` 见 `:1134-1140`）。
- 证据：`ONScripter_command.cpp:722-739`（颜色分支 `:725-729`，图片分支 `:730-735`，重绘 `:736`）。

### `setwindow3`
- 处理函数：`setwindow3Command` — `ONScripter_command.cpp:708`
- 语法：与 `setwindow` 完全相同（共用 `setwindowCore()`）：
  - 颜色窗口：`setwindow3 <top_x>,<top_y>,<num_x>,<num_y>,<font_x>,<font_y>,<pitch_x>,<pitch_y>,<wait>,<bold>,<shadow>,#RRGGBB,<x>,<y>,<right>,<bottom>`（共 16 个参数）
  - 图片窗口：`setwindow3 <top_x>,<top_y>,<num_x>,<num_y>,<font_x>,<font_y>,<pitch_x>,<pitch_y>,<wait>,<bold>,<shadow>,<图名>,<x>,<y>`（共 14 个参数）
- 参数：P1–P16 的语义、顺序与取值范围与 `setwindow` 完全相同，逐参数说明见上节。
- 可选参数：无（同 `setwindow`）。
- 坐标：**是**（同 `setwindow`：P1/P2、P5–P8、P13–P16 像素；P3/P4 字符数；P9 时间；P10/P11 标志；P12 字符串）。
- 语义：设置窗口后调用 `clearCurrentPage()`（`:712`）清空当前页，并复位缩进/回车状态、`display_mode`，最后 flush。与 `setwindow` 的差别是**不执行** `lookbackflushCommand()`——不轮转/清空历史回看页。
- 证据：`ONScripter_command.cpp:708-720`（`setwindowCore()` `:710`，`clearCurrentPage()` `:712`，flush `:717`）；`ONScripter.cpp:1227-1249`（clearCurrentPage）；对照 `:745`（setwindow 的 lookbackflush）。

### `sevol`
- 处理函数：`sevolCommand` — `ONScripter_command.cpp:647`
- 语法：`sevol <音量>`
- 参数：
  - P1 `se_volume`：音效音量百分比，期望 0–100；源码不做钳制，直接 `se_volume * MIX_MAX_VOLUME / 100` 传给 `Mix_Volume`。
- 可选参数：无。
- 坐标：**否**（音量）。
- 语义：保存 `se_volume`，并对通道 1…`ONS_MIX_CHANNELS-1` 及两个 loop BGM 通道中已加载的样本立即应用新音量；**不包含**通道 0（人声，见 `voicevol`）。
- 证据：`ONScripter_command.cpp:647-658`（参数 `:649`，应用 `:651-655`）。

### `shadedistance`
- 处理函数：`shadedistanceCommand` — `ScriptParser_command.cpp:238`
- 语法：`shadedistance <x 偏移>, <y 偏移>`
- 参数：
  - P1 `shade_distance[0]`：文字阴影水平偏移（像素），先乘 `screen_ratio1/screen_ratio2`；结果为 0 时强制为 1。
  - P2 `shade_distance[1]`：文字阴影垂直偏移（像素），同上。
- 可选参数：无。
- 坐标：**否**（是像素**偏移量**，不写入 `orig_pos` 等坐标字段，也不与 `screen_width/height` 运算；但它是布局像素量，见备注）。
- 备注：默认值 1/1（`ScriptParser.cpp:187-188`）；用于文字阴影绘制（`ONScripter_text.cpp:145-146`、`:242`、`:372`）。做分辨率换算时建议按像素量同比处理，是否计入坐标清单**待验证**（本文未计入）。
- 证据：`ScriptParser_command.cpp:238-246`（P1/P2 `:240-243`）；`ONScripter_text.cpp:145-146`。

### `sin`
- 处理函数：`sinCommand` — `ScriptParser_command.cpp:227`
- 语法：`sin <结果变量>, <角度>`
- 参数：
  - P1：目标整型变量/数组元素。用 `readInt()` 读入并把其变量描述 `pushVariable()` 暂存（不是 `readVariable()`，故只支持整型/数组，字符串变量会触发 `setInt: no variables.`）。
  - P2 `val`：角度（度，整数）。
- 可选参数：无。
- 坐标：**否**（三角函数值）。
- 语义：`P1 = (int)(sin(π·P2/180)·1000)`，结果范围约 -1000…1000。
- 证据：`ScriptParser_command.cpp:227-236`（P1/P2 `:229-233`）；`ScriptHandler.cpp:773-776`（pushVariable）、`:760-771`（setInt 类型检查）。

### `skip`
- 处理函数：`skipCommand` — `ScriptParser_command.cpp:211`
- 语法：`skip <行数>`
- 参数：
  - P1 `val`：相对当前行的行偏移（可负）。`0` 会被改成 `1`。
- 可选参数：无。
- 坐标：**否**（行号偏移）。
- 语义：目标绝对行 = `current_label_info.start_line + current_line + P1`；由 `getAddressByLine()` 解析地址后重设 `current_label_info/current_line/current_script`。目标早于第一个标签时 `getLabelByLine()` 回退到首标签，再按 `line - start_line` 前进（负差值不前进）。
- 证据：`ScriptParser_command.cpp:211-225`（`val==0` `:214`，目标行 `:216`，跳转 `:218-222`）；`ScriptHandler.cpp:522-534`、`:546-554`。

### `skipoff`
- 处理函数：`skipoffCommand` — `ONScripter_command.cpp:640`
- 语法：`skipoff`（无参数）
- 参数：无。
- 可选参数：无。
- 坐标：**否**（无参数）。
- 语义：`skip_mode &= ~SKIP_NORMAL`，仅关闭普通跳过位，不清除其它跳过标志。
- 证据：`ONScripter_command.cpp:640-645`（`:642`）。

### `spi` / `soundpressplgin`
- 处理函数：`soundpressplginCommand` — `ScriptParser_command.cpp:186`
- 语法：`soundpressplgin <nbzplgin.dll|扩展名>`（`spi` 同义）
- 参数：
  - P1：插件声明字符串。前 12 字节（大小写不敏感）必须为 `nbzplgin.dll`，否则打印 `plugin ... is not available, ignored.` 并返回；之后在串中找 `|`，`|` 后的文本作为压缩扩展名注册为 NBZ 压缩（`BaseReader::NBZ_COMPRESSION`）。无 `|` 时直接返回、不注册。
- 可选参数：无（`|` 之后的部分由 P1 内部携带）。
- 坐标：**否**（插件名/扩展名）。
- 备注：仅允许 `define` 段（`:188-189`）。支持的插件只有 `nbzplgin.dll`。
- 证据：`ScriptParser_command.cpp:186-209`（长度/大小写处理 `:191-197`，`|` 解析 `:202-206`）。

### `sp_rgb_gradation`
- 处理函数：`sp_rgb_gradationCommand` — `ONScripter_command.cpp:434`
- 语法：`sp_rgb_gradation <编号>, <上R>,<上G>,<上B>, <下R>,<下G>,<下B>, <键R>,<键G>,<键B>, <alpha>`
- 参数：
  - P1 `no`：`-1` 表示窗口字体图 `sentence_font_info`；否则为 `sprite_info[no]`（源码未校验范围）。
  - P2–P4 `upper_r/g/b`：渐变起始行颜色（0–255）。
  - P5–P7 `lower_r/g/b`：渐变结束行颜色（0–255）。
  - P8–P10 `key_r/g/b`：被替换的键色（0–255），按目标表面像素格式换算成掩码。
  - P11 `alpha`：写入键色像素的透明度（0–255）。
- 可选参数：无。
- 坐标：**否**（编号/颜色/透明度；内部仅用表面宽高扫描，无脚本坐标）。
- 语义：在精灵表面中扫描键色，记录首次/末次出现键色的**行**；把这两行之间（含）每行中所有键色像素改写为按行插值的 upper→lower 颜色，并写入 alpha。表面为空（未加载图）时直接返回；仅 1 行命中时用 upper 色。本命令不改变位置/尺寸。
- 证据：`ONScripter_command.cpp:434-525`（参数 `:436-446`，空表面 `:451-452`，键色扫描 `:461-480`，渐变替换 `:482-517`，alpha `:494`/`:509`）。

### `spbtn` / `existspbtn` / `cellcheckspbtn`
- 处理函数：`spbtnCommand` — `ONScripter_command.cpp:604`
- 语法：
  - `spbtn <精灵号>, <按钮号>`
  - `existspbtn <精灵号>, <按钮号>`
  - `cellcheckspbtn <精灵号>, <按钮号>`
- 参数：
  - P1 `sprite_no`：精灵编号，有效范围 0–999（`MAX_SPRITE_NUM`）；还要求该精灵已加载图片（`image_surface != NULL`）。
  - P2 `no`：按钮编号，必须 ≥1（`no < 1` 直接忽略）。
- 可选参数：无。
- 坐标：**否**（P1/P2 为编号；命中矩形取自精灵自身 `pos`）。
- 语义：新建 `ButtonLink` 并插入按钮链，类型 `SPRITE_BUTTON`，`sprite_no/no` 如上；`image_rect = select_rect = sprite_info[sprite_no].pos`。`cellcheckspbtn` 额外要求 `num_of_cells >= 2`（`:619-621`）；普通形式要求 `num_of_cells != 0`（`:622-624`）。**注意**：本快照函数体只特判 `cellcheckspbtn`，`existspbtn` 没有独立分支，行为与 `spbtn` 相同（不检查按钮是否已存在）——与名称语义的差异**待验证**。
- 证据：`ONScripter_command.cpp:604-638`（标志 `:606-609`，参数与校验 `:611-624`，矩形 `:633-635`）；`ONScripter_lut.cpp:83`、`:295`、`:350`；`ONScripter.h:58`（`MAX_SPRITE_NUM 1000`）。

### `spclclk`
- 处理函数：`spclclkCommand` — `ONScripter_command.cpp:597`
- 语法：`spclclk`（无参数）
- 参数：无。
- 可选参数：无。
- 坐标：**否**。
- 语义：若未启用 `force_button_shortcut_flag`，置 `spclclk_flag = true`。启用后，在 `WAIT_BUTTON_MODE` 下按空格等同「点击当前悬停按钮」（返回 `current_over_button`；无悬停时返回按钮 0 / `RETURN`），而不是产生 SPACE 事件（`ONScripter_event.cpp:838-880`；对照 `:899-901`）。该标志在初始化时清零（`ONScripter.cpp:1573`）。
- 证据：`ONScripter_command.cpp:597-602`（`:599-600`）；`ONScripter_event.cpp:838-880`、`:899-901`；`ScriptParser.cpp:61`、`ONScripter.cpp:488-491`、`:1573`。

### `split` / `splitstring`
- 处理函数：`splitCommand` — `ONScripter_command.cpp:550`
- 语法：`split <源字符串>, <分隔符>, <变量1>[, <变量2>, …]`
- 参数：
  - P1：源字符串，`readStr()` 后 `saveStringBuffer()` 暂存（扫描指针基于该副本）。
  - P2：分隔符字符串，**只取首字符**；空串时分隔符为 `\0`，所有 token 为空。
  - P3…：接收 token 的变量，逗号分隔、数量不限；每个 token 按变量类型写入：整型/数组用 `atoi(token)`，字符串变量用 `setStr(token)`。
- 可选参数：P3 起为可变参数；循环条件是「上一步之后仍有逗号」，因此至少给 1 个变量才有实际赋值，token 用尽后剩余变量得到空串。
- 坐标：**否**（字符串/变量）。
- 语义：按分隔符逐个切分源串；扫描按编码判断字符宽度（UTF-8 用 `UTF8_N_BYTE`，否则用 `IS_TWO_BYTE`），token 长度 <256 用栈缓冲，否则堆分配。超过变量数的 token 被忽略。
- 证据：`ONScripter_command.cpp:550-595`（P1/P2 `:552-555`，切分 `:558-577`，赋值 `:579-586`，指针推进 `:590-591`）。

### `spreload`
- 处理函数：`spreloadCommand` — `ONScripter_command.cpp:534`
- 语法：`spreload <编号>`
- 参数：
  - P1 `no`：`-1` 表示窗口字体图 `sentence_font_info`，否则 `sprite_info[no]`（源码未校验范围）。
- 可选参数：无。
- 坐标：**否**（P1 为编号；矩形来自图片标签解析结果）。
- 语义：对指定精灵执行 `parseTaggedString()` + `setupAnimationInfo()`，即预解析/预加载图片（不改变可见性），若可见则把 `ai->pos` 加入脏矩形。
- 证据：`ONScripter_command.cpp:534-548`（参数 `:536-539`，解析 `:541-542`，脏矩形 `:544-545`）。

### `spstr`
- 处理函数：`spstrCommand` — `ONScripter_command.cpp:527`
- 语法：`spstr <exbtn 控制串>`
- 参数：
  - P1：exbtn 控制字符串，交给 `decodeExbtnControl()` 解析，用于按扩展按钮状态批量设置精灵显示。
- 可选参数：无。
- 坐标：**否**（控制串）。
- 证据：`ONScripter_command.cpp:527-532`（`:529`）。

### `stop`
- 处理函数：`stopCommand` — `ONScripter_command.cpp:426`
- 语法：`stop`（无参数）
- 参数：无。
- 可选参数：无。
- 坐标：**否**。
- 语义：依次执行 `mp3stopCommand()` 与 `wavestopCommand()`，停止 BGM 与波形（音效）播放。
- 证据：`ONScripter_command.cpp:426-432`（`:428-429`）。

### `stralias`
- 处理函数：`straliasCommand` — `ScriptParser_command.cpp:172`
- 语法：`stralias <别名>, <字符串>`
- 参数：
  - P1：别名（`readLabel()`），可带前导 `*`，ASCII 大写转小写。
  - P2：别名展开后的字符串（`readStr()`）。
- 可选参数：无。
- 坐标：**否**（名称/字符串）。
- 语义：`addStrAlias(P1,P2)` 注册字符串别名；仅允许 `define` 段（`:174-175`）。
- 证据：`ScriptParser_command.cpp:172-184`（P1 `:177-178`，P2 `:179`，注册 `:181`）；`ScriptHandler.cpp:318-355`。

### `strsp` / `strsph`
- 处理函数：`strspCommand` — `ONScripter_command.cpp:366`
- 语法：
  - `strsp <精灵号>, <文本>, <x>, <y>, <num_x>, <num_y>, <font_x>, <font_y>, <pitch_x>, <pitch_y>, <bold>, <shadow>[, <颜色1>, <颜色2>, …]`
  - `strsph <精灵号>, <文本>, <x>, <y>, <num_x>, <num_y>, <font_x>, <font_y>, <pitch_x>, <pitch_y>, <bold>, <shadow>[, <颜色…>]`
- 参数：
  - P1 `sprite_no`：目标精灵编号（源码未校验范围，`sprite_info[]` 共 1000 项）。
  - P2 `text`：显示文本，写入 `ai->file_name`（`TRANS_STRING` 模式用该字段保存文本）。
  - P3 `x`、P4 `y`：文本左上角（像素）→ `orig_pos.x/y`，随后 `scalePosXY()`。
  - P5 `num_x`：每行字符数 → `fi.num_xy[0]`（**字符数，不 ×2**）。
  - P6 `num_y`：行数 → `fi.num_xy[1]`（字符数，不 ×2）。
  - P7 `font_x`、P8 `font_y`：字号（像素）→ `fi.font_size_xy`。
  - P9 `pitch_x`：字间距**增量**（像素）→ `fi.pitch_xy[0] = P9 + font_x`。
  - P10 `pitch_y`：行间距**增量**（像素）→ `fi.pitch_xy[1] = P10 + font_y`。
  - P11 `bold`：非 0 粗体。
  - P12 `shadow`：非 0 阴影。
  - P13…：可选的每 cell 颜色，逐个 `#RRGGBB`；数量 = 尾部逗号参数个数，构成 `color_list`。缺省（0 个）时 `num_of_cells=1`、颜色为白色。
- 可选参数：P13 起颜色串，数量可变；每个都必须是 `#RRGGBB`，否则 `readColor()` 直接报错退出。
- 坐标：**是**（P3/P4 像素、P5/P6 字符数、P7–P10 像素；P1 编号、P2 文本、P11/P12 标志、P13+ 颜色）。
- 语义：`strsp` 使精灵可见（`visible=true`），`strsph` 使其隐藏（`visible=false`，用于仅排版/测量）。执行前 `leaveTextDisplayMode()`，`removeTag()` 清空旧 cell/颜色，设置 `TRANS_STRING`、`trans=-1`、单行/紧致区域关闭，按 `sentence_font.rubyon_flag` 决定 ruby 可绘，最后 `setupAnimationInfo(ai,&fi)`。
- 证据：`ONScripter_command.cpp:366-424`（`strsph` 判定 `:370-371`，P1 `:373-375`，P2 `:381`，P3/P4 `:382-384`，P5–P12 `:386-395`，颜色 `:397-412`，模式与 setup `:414-420`）；`AnimationInfo.cpp:191-219`（removeTag 清零 `num_of_cells`）。

### `sub`
- 处理函数：`subCommand` — `ScriptParser_command.cpp:161`
- 语法：`sub <结果变量>, <减数>`
- 参数：
  - P1：目标整型变量/数组元素，`readInt()` 读取并 `pushVariable()` 暂存（不是 `readVariable()`）。
  - P2 `val2`：减数（整数表达式）。
- 可选参数：无。
- 坐标：**否**（变量/数值）。
- 语义：`P1 = P1 - P2`。
- 证据：`ScriptParser_command.cpp:161-170`（`:163-167`）；`ScriptHandler.cpp:773-776`。

### `systemcall`
- 处理函数：`systemcallCommand` — `ONScripter_command.cpp:357`
- 语法：`systemcall <系统调用名>`
- 参数：
  - P1：系统调用名（`readLabel()`，可带前导 `*`，ASCII 大写转小写）。支持：`skip`、`reset`、`save`、`load`、`lookback`、`windowerase`、`rmenu`、`automode`、`end`。
- 可选参数：无。
- 坐标：**否**（名称）。
- 语义：`getSystemCallNo()` 映射为枚举；未识别的名称打印 `Unsupported system call ...` 并返回 -1，`executeSystemCall()` 在 `default` 分支仅 `leaveSystemCall()`，即**无动作**。
- 证据：`ONScripter_command.cpp:357-364`（`:359`）；`ScriptParser.cpp:310-325`；`ONScripter_rmenu.cpp:139-176`（`default` `:171-173`）。

## T

### `tablegoto`
- 处理函数：`tablegotoCommand` — `ONScripter_command.cpp:341`
- 语法：`tablegoto <索引>, <*标签0>, <*标签1>, …`
- 参数：
  - P1 `no`：0 起始的索引。
  - P2…：候选标签串，逗号分隔；每个应为 `*label` 形式（源码固定 `buf+1` 跳过首字符后调用 `setCurrentLabel`）。
- 可选参数：P2 起为可变参数；数量不限，匹配到即 `break`。
- 坐标：**否**（索引/标签）。
- 语义：读入 P1 后按顺序读标签，`count++ == no` 时跳转到该标签；索引越界（含负值）则不跳转、继续执行下一条命令。
- 证据：`ONScripter_command.cpp:341-355`（`:343-351`）；`ScriptParser.cpp:635-640`（setCurrentLabel）。

### `tal`
- 处理函数：`talCommand` — `ONScripter_command.cpp:316`
- 语法：`tal <l|c|r>, <trans>, <特效号>[, <时长>[, <动画文件>]]`（P1 非 l/c/r 时退化为 `tal <其它>, <特效号>[,…]`）
- 参数：
  - P1 `loc`：位置标签首字符，`l`=左立绘（0）、`c`=中（1）、`r`=右（2）；其它值不更新任何立绘，但仍执行后面的特效。
  - P2 `trans`：立绘透明度（0–255，写入 `tachi_info[no].trans`；源码不钳制）；**仅当 P1 有效时读取**。
  - P3 `effect`：特效号（`readEffect()` 无条件读取，因而必填）。
  - P4 `duration`：特效时长（ms），可选。
  - P5 `anim`：特效动画文件名，可选。
- 可选参数：P4、P5 可选；P2 仅在 P1 不是 l/c/r 时可省略（此时紧随 P1 的数值被当作特效号）。
- 坐标：**否**（位置标签/透明度/特效参数；立绘坐标由 `ld` 等命令决定）。
- 语义：先 `leaveTextDisplayMode()`，更新指定立绘的 `trans` 并加入脏矩形；随后 `parseEffect(true)`：异步特效（`setEffect` 返回真）直接返回，否则循环 `doEffect()` 直至结束。特效号不存在时 `parseEffect` 打印错误并 `exit(-1)`。
- 证据：`ONScripter_command.cpp:316-339`（P1/P2 `:320-332`，特效 `:334-336`）；`ScriptParser.cpp:664-708`（readEffect/parseEffect）。

### `tan`
- 处理函数：`tanCommand` — `ScriptParser_command.cpp:150`
- 语法：`tan <结果变量>, <角度>`
- 参数：
  - P1：目标整型变量/数组元素（`readInt()` + `pushVariable()`）。
  - P2 `val`：角度（度）。
- 可选参数：无。
- 坐标：**否**（三角函数值）。
- 语义：`P1 = (int)(tan(π·P2/180)·1000)`。
- 证据：`ScriptParser_command.cpp:150-159`（`:152-156`）。

### `tateyoko`
- 处理函数：`tateyokoCommand` — `ONScripter_command.cpp:309`
- 语法：`tateyoko <0|1>`
- 参数：
  - P1：排版方向。`0` = 横排（`YOKO_MODE`）；非 0（通常 `1`）= 竖排（`TATE_MODE`）。
- 可选参数：无。
- 坐标：**否**（模式）。
- 语义：`sentence_font.setTateyokoMode(P1)`，内部同时 `clear()` 重置当前字符坐标（横排回到行首、竖排回到列顶）。
- 证据：`ONScripter_command.cpp:309-314`（`:311`）；`FontInfo.cpp:164-168`、`:205-212`。

### `texec`
- 处理函数：`texecCommand` — `ONScripter_command.cpp:295`
- 语法：`texec`（无参数）
- 参数：无。
- 可选参数：无。
- 坐标：**否**。
- 语义：根据 `textgosub_clickstr_state` 决定：`CLICK_NEWPAGE` → `newPage()`；`CLICK_WAIT|CLICK_EOL` → `processEOT()` 并清 `page_enter_status`；随后置 `saveon_flag = true`（允许存档）。
- 证据：`ONScripter_command.cpp:295-307`（`:297-304`）。

### `textclear`
- 处理函数：`textclearCommand` — `ONScripter_command.cpp:289`
- 语法：`textclear`（无参数）
- 参数：无。
- 可选参数：无。
- 坐标：**否**。
- 语义：`newPage()`，清空当前文本页并新建页。
- 证据：`ONScripter_command.cpp:289-293`（`:291`）。

### `textcolor`
- 处理函数：`textcolorCommand` — `ONScripter_command.cpp:282`
- 语法：`textcolor <#RRGGBB>`
- 参数：
  - P1：文本颜色，`#RRGGBB`，写入 `sentence_font.color`。
- 可选参数：无。
- 坐标：**否**（颜色）。
- 证据：`ONScripter_command.cpp:282-287`（`:284`）。

### `textgosub`
- 处理函数：`textgosubCommand` — `ScriptParser_command.cpp:140`
- 语法：`textgosub <*标签>`
- 参数：
  - P1：点击等待时转去的标签。源码 `readStr()+1` **固定跳过首字符**，因此必须写成 `*label`；若省略 `*`，首字符会被丢弃。
- 可选参数：无。
- 坐标：**否**（标签）。
- 备注：仅允许 `define` 段（`:142-143`）。
- 证据：`ScriptParser_command.cpp:140-148`（`:145`）。

### `texthide`
- 处理函数：`texthideCommand` — `ONScripter_command.cpp:273`
- 语法：`texthide`（无参数）
- 参数：无。
- 可选参数：无。
- 坐标：**否**。
- 语义：全屏填脏矩形，`refresh_shadow_text_mode = REFRESH_NORMAL_MODE|REFRESH_SHADOW_MODE`（**不含** `REFRESH_TEXT_MODE`），立即 `flush()`，从而重绘时不再画文本层（隐藏文本）。
- 证据：`ONScripter_command.cpp:273-280`（`:275-277`）；`ONScripter.h:603-606`；`ONScripter_image.cpp:752-754`。

### `textoff`
- 处理函数：`textoffCommand` — `ONScripter_command.cpp:261`
- 语法：`textoff`（无参数）
- 参数：无。
- 可选参数：无。
- 坐标：**否**。
- 语义：若 `windowchip_sprite_no >= 0` 则隐藏该窗口 chip 精灵；`leaveTextDisplayMode(true)` 退出文本显示模式；`text_on_flag = false`。
- 证据：`ONScripter_command.cpp:261-271`（`:263-268`）。

### `texton`
- 处理函数：`textonCommand` — `ONScripter_command.cpp:249`
- 语法：`texton`（无参数）
- 参数：无。
- 可选参数：无。
- 坐标：**否**。
- 语义：若存在窗口 chip 精灵则显示；`enterTextDisplayMode()`；`text_on_flag = true`。
- 证据：`ONScripter_command.cpp:249-259`（`:251-256`）。

### `textshow`
- 处理函数：`textshowCommand` — `ONScripter_command.cpp:240`
- 语法：`textshow`（无参数）
- 参数：无。
- 可选参数：无。
- 坐标：**否**。
- 语义：全屏填脏矩形，`refresh_shadow_text_mode = REFRESH_NORMAL_MODE|REFRESH_SHADOW_MODE|REFRESH_TEXT_MODE`（**含**文本层），立即 `flush()`，恢复文本显示。
- 证据：`ONScripter_command.cpp:240-247`（`:242-244`）。

### `textspeed`
- 处理函数：`textspeedCommand` — `ONScripter_command.cpp:233`
- 语法：`textspeed <毫秒>`
- 参数：
  - P1：每字符等待时间（ms），写入 `sentence_font.wait_time`。`-1` = 使用默认速度档（由 `text_speed_no` 选 40/20/10 ms）；`0` = 不等；`>0` = 每字符等待的毫秒数。
- 可选参数：无。
- 坐标：**否**（时间）。
- 证据：`ONScripter_command.cpp:233-238`（`:235`）；`ONScripter_text.cpp:878-887`、`:1083-1086`；`ScriptParser.cpp:39-41`、`:190-192`；`ONScripter.cpp:826`（默认 20）。

### `textspeeddefault`
- 处理函数：`textspeeddefaultCommand` — `ONScripter_command.cpp:227`
- 语法：`textspeeddefault`（无参数）
- 参数：无。
- 可选参数：无。
- 坐标：**否**。
- 语义：`sentence_font.wait_time = -1`，恢复为按默认速度档（40/20/10 ms）显示。
- 证据：`ONScripter_command.cpp:227-231`（`:229`）。

### `time`
- 处理函数：`timeCommand` — `ScriptParser_command.cpp:123`
- 语法：`time <时变量>, <分变量>, <秒变量>`
- 参数：
  - P1：接收「时」（0–23）的整型/数组变量（`readVariable()`）。
  - P2：接收「分」（0–59）。
  - P3：接收「秒」（0–59）。
- 可选参数：无。
- 坐标：**否**（变量）。
- 备注：使用 `localtime()`，取本地时间；三个参数必须是整型/数组变量（字符串变量会在 `setInt` 处报错）。
- 证据：`ScriptParser_command.cpp:123-138`（`:125-135`）。

### `transbtn` / `btrans`
- 处理函数：`transbtnCommand` — `ONScripter_command.cpp:220`
- 语法：`transbtn` / `btrans`（无参数）
- 参数：无。
- 可选参数：无。
- 坐标：**否**。
- 语义：置 `transbtn_flag = true`，按钮改为「透明按钮」命中方式：鼠标位置落在按钮矩形内时，按像素 alpha 选中最不透明的按钮（跳过全透明像素）；`btndef`/`bclear` 会把它复位为 false。
- 证据：`ONScripter_command.cpp:220-225`（`:222`）；`ONScripter.cpp:930-948`（透明度命中）；`ONScripter_command.cpp:3539-3564`（btndef 复位）。

### `transmode`
- 处理函数：`transmodeCommand` — `ScriptParser_command.cpp:109`
- 语法：`transmode <leftup|copy|alpha|righttup>`
- 参数：
  - P1：过渡模式名，大小写不敏感（`compareString` 仅比较前缀）：`leftup`→`TRANS_TOPLEFT`、`copy`→`TRANS_COPY`、`alpha`→`TRANS_ALPHA`、`righttup`→`TRANS_TOPRIGHT`。未匹配时保持原 `trans_mode`；随后 `readLabel()` 消费该 token。
- 可选参数：无。
- 坐标：**否**（模式）。
- 备注：仅允许 `define` 段（`:111-112`）。该值作为后续图片标签解析的默认过渡模式（`ONScripter_animation.cpp:272`）。
- 证据：`ScriptParser_command.cpp:109-121`（`:114-118`）；`ScriptHandler.cpp:568-578`（前缀、大小写不敏感）。

### `trap` / `lr_trap`
- 处理函数：`trapCommand` — `ONScripter_command.cpp:179`
- 语法：
  - `trap off` / `trap stop` / `trap resume`
  - `trap <*标签>`；`lr_trap <*标签>`
- 参数：
  - P1：关键字或标签串。`off` 清空 `trap_mode`；`stop` 追加 `TRAP_STOP`；`resume` 若此前已点击则立即 `trapHandler()`；其它字符串必须是 `*label`（`buf[0]=='*'`），去掉 `*` 后存入 `trap_dist`，否则打印 `is not supported` 并忽略。
- 可选参数：无。
- 坐标：**否**（模式/标签）。
- 语义：先按命令名设定点击类型——`lr_trap` 同时响应左右键，`trap` 只响应左键；再处理 P1。`compareString` 为大小写不敏感**前缀**匹配。源码另有 `r_trap`（仅右键）分支，但 `func_lut` 中没有注册 `r_trap`，该分支不可达。
- 证据：`ONScripter_command.cpp:179-218`（模式 `:183-191`，off/stop/resume `:193-207`，标签 `:209-215`）；`ONScripter_lut.cpp:50`、`:206`。

## U

### `underline`
- 处理函数：`underlineCommand` — `ScriptParser_command.cpp:102`
- 语法：`underline <基线 Y>`
- 参数：
  - P1：立绘底边对齐用的屏幕 Y 基线（像素），写入 `underline_value`；默认值为 `screen_height`。
- 可选参数：无。
- 坐标：**是**（P1 是屏幕 Y 坐标；`ld` 用它计算立绘 `orig_pos.y = underline_value - 图片高`，见 `ONScripter_command.cpp:1929-1930`）。
- 证据：`ScriptParser_command.cpp:102-107`（`:104`）；`ONScripter.cpp:288`（默认值）；`ONScripter_command.cpp:1927-1933`（ld 中的使用）。

### `useescspc`
- 处理函数：`useescspcCommand` — `ScriptParser_command.cpp:91`
- 语法：`useescspc`（无参数）
- 参数：无。
- 可选参数：无。
- 坐标：**否**。
- 语义：若未启用 `force_button_shortcut_flag`，置 `useescspc_flag = true`，使 ESC/空格等按键作为文本推进/点击处理。仅允许 `define` 段（`:93-94`）。
- 证据：`ScriptParser_command.cpp:91-100`（`:96-97`）；`ONScripter_event.cpp:842`、`:899`。

### `usewheel`
- 处理函数：`usewheelCommand` — `ScriptParser_command.cpp:81`
- 语法：`usewheel`（无参数）
- 参数：无。
- 可选参数：无。
- 坐标：**否**。
- 语义：`usewheel_flag = true`，允许鼠标滚轮推进文本。仅允许 `define` 段（`:83-84`）。
- 证据：`ScriptParser_command.cpp:81-89`（`:86`）。

## V

### `versionstr`
- 处理函数：`versionstrCommand` — `ScriptParser_command.cpp:67`
- 语法：`versionstr <第一行>, <第二行>`
- 参数：
  - P1：版本信息第一行（`readStr()` 后 `saveStringBuffer()` 暂存）。
  - P2：版本信息第二行。
- 可选参数：无。
- 坐标：**否**（字符串）。
- 语义：释放旧 `version_str`，重建为 `"P1\nP2\n"`（两行均强制换行结尾）。
- 证据：`ScriptParser_command.cpp:67-79`（`:71-76`）。

### `voicevol`
- 处理函数：`voicevolCommand` — `ONScripter_command.cpp:161`
- 语法：`voicevol <音量>`
- 参数：
  - P1 `voice_volume`：人声音量百分比，期望 0–100；源码不钳制，直接换算给通道 0 的 `Mix_Volume`。
- 可选参数：无。
- 坐标：**否**（音量）。
- 语义：保存 `voice_volume`；若通道 0 已有加载样本，立即应用新音量。
- 证据：`ONScripter_command.cpp:161-167`（`:163-164`）。

### `vsp` / `vsp2`
- 处理函数：`vspCommand` — `ONScripter_command.cpp:139`
- 语法：
  - `vsp <精灵号>, <0|1>`
  - `vsp2 <精灵2号>, <0|1>`
- 参数：
  - P1 `no`：精灵编号（`vsp` 用 `sprite_info`，`vsp2` 用 `sprite2_info`；源码未校验范围）。
  - P2 `v`：可见标志，`1` = 显示，其它 = 隐藏。
- 可选参数：无。
- 坐标：**否**（编号/标志；脏矩形取自精灵自身 `pos`/`bounding_rect`）。
- 语义：先 `leaveTextDisplayMode()`；设置 `visible` 后把对应矩形加入脏矩形（`vsp` 用 `pos`，`vsp2` 用 `bounding_rect`）。
- 证据：`ONScripter_command.cpp:139-159`（`vsp2` 判定 `:144`，参数 `:146-147`，分支 `:149-156`）。

## W

### `wait`
- 处理函数：`waitCommand` — `ONScripter_command.cpp:131`
- 语法：`wait <毫秒>`
- 参数：
  - P1：等待毫秒数（从当前时刻起算），进入 `WAIT_TIMER_MODE` 后 `waitEvent(P1)`。
- 可选参数：无。
- 坐标：**否**（时间）。
- 证据：`ONScripter_command.cpp:131-137`（`:133-134`）。

### `waittimer`
- 处理函数：`waittimerCommand` — `ONScripter_command.cpp:120`
- 语法：`waittimer <毫秒>`
- 参数：
  - P1：相对 `internal_timer` 的目标时刻偏移（ms）。实际等待量 = `P1 + internal_timer - SDL_GetTicks()`，小于 0 时按 0 处理。
- 可选参数：无。
- 坐标：**否**（时间）。
- 语义：`internal_timer` 由 `resettimer`（及初始化）设置，**不是**每次事件刷新，因此 `waittimer` 是「等到时间轴上的 `internal_timer + P1` 时刻」的绝对式定时，而非「从现在起等 P1 毫秒」；截止时刻已过则立即返回。随后进入 `WAIT_TIMER_MODE` 并 `waitEvent(count)`。
- 证据：`ONScripter_command.cpp:120-129`（`:122-126`）；`ONScripter_command.cpp:1106-1111`（resettimer 设 `internal_timer`）；`ONScripter.cpp:651`（初始化）。

---

## 本册统计

- 处理函数：51 个
- 命令条目：60 条（含 9 个别名：`abssetcursor`、`spi`、`existspbtn`、`cellcheckspbtn`、`splitstring`、`strsph`、`btrans`、`lr_trap`、`vsp2`）
- 含坐标命令（5 组 / 7 个命令名）：
  - `setcursor` / `abssetcursor` — P3/P4 像素（相对或绝对）
  - `setwindow` — P1/P2、P5–P8、P13–P16 像素；P3/P4 字符数
  - `setwindow3` — 同 `setwindow`
  - `strsp` / `strsph` — P3/P4、P7–P10 像素；P5/P6 字符数
  - `underline` — P1 屏幕 Y 基线
- 未定位：0
- 备注：
  - `textwindow` 不在 `func_lut` 中，未收录、未虚构。
  - `shadedistance` 为像素偏移量（按 `screen_ratio` 缩放），不写入坐标字段，未计入坐标清单；换算策略待确认。
  - `existspbtn` 在本快照与 `spbtn` 同路径（函数体仅特判 `cellcheckspbtn`），差异待验证。
  - `trap` 的 `r_trap` 分支未注册，不可达。
