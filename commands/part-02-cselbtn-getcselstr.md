# 命令分册 02：处理函数 `cselbtnCommand` – `getcselstrCommand`

> 本册覆盖 `func_lut` 中处理函数位于 `cselbtnCommand`…`getcselstrCommand` 区间的全部命令（含别名），共 43 个处理函数、52 条命令。
> 行号均指快照 `src/onsyuri/`。参数按**源码读取顺序**列出，逗号分隔；`[ ]` 表示可选（由 `script_h.getEndStatus() & END_COMMA` 判断）。本册只有 `effect`/`windoweffect` 经 `readEffect()` 具备真正的可选参数（`ScriptParser.cpp:669`、`:672`）；其余命令参数个数固定，或由命令名/关键字分支决定（见 `dwave*`、`for` 各节）。
> 「坐标」列：**是** = 该命令含逻辑坐标/尺寸参数，需在 ×2 转换中处理；**否** = 不含；**部分** = 仅部分参数是坐标。
> 判定依据：参数是否直接写入 `orig_pos.x/y/w/h`、`pos`、`image_rect`、`top_xy`、`num_xy`、`font_size_xy`、`pitch_xy`、`max_width`、`dst_rect/src_rect` 等坐标字段，或与 `screen_width/height` 做像素运算。
> 本册坐标按 NScripter 的 640×480 逻辑坐标书写，经 `screen_ratio1/screen_ratio2` 换算为实际像素（本快照中两者初始化为 1：`ONScripter.cpp:203-204`；换算公式见 `AnimationInfo.h:119-122`）。

---

## B

### `bdef`
- 处理函数：`exbtnCommand` — `ONScripter_command.cpp:2819`（与 `exbtn`/`exbtn_d`/`cellcheckexbtn` 共用；`ONScripter_lut.cpp:298`、`:297`、`:351`、`:379`）
- 语法：`bdef <控制串>`
- 参数：
  - P1：扩展按钮控制串（`readStr()`，`ONScripter_command.cpp:2858`），写入共享结构 `exbtn_d_button_link.exbtn_ctl[1]`（`:2863`）。
- 可选参数：无。`bdef`/`exbtn_d` 走 `:2824-2825` 分支，不读精灵号与按钮号，只读这 1 个字符串。
- 语义：先清空共享按钮 `exbtn_d_button_link` 的 `exbtn_ctl[0..2]`（`:2826-2832`），再写入新的 `ctl[1]`；此后凡自身 `ctl[1]` 为空的 exbtn 按钮都改用这一默认控制串（`ONScripter.cpp:989-990`、`:1006-1011`）。写入后仍把 `button_type = SPRITE_BUTTON`、`sprite_no = -1`、`no = 0` 记到该共享结构（`:2860-2862`）。
- 坐标：否（参数是控制串）。P1 内可含 `C`/`P`/`S`/`M` 子命令，其中 `M<sp>,<x>,<y>` 的 x/y 会写入 `orig_pos` 并重算 `pos`（`ONScripter.cpp:1418-1433`），但坐标藏在字符串里，不是独立参数。
- 备注：与 `exbtn_d` 完全同义（同一 LUT 目标、同一分支），是 NScripter “默认按钮定义”在本快照中的实现。

## C

### `cellcheckexbtn`
- 处理函数：`exbtnCommand` — `ONScripter_command.cpp:2819`（`ONScripter_lut.cpp:351`）
- 语法：`cellcheckexbtn <精灵号>, <按钮编号>, <控制串>`
- 参数：
  - P1：作为按钮外观的精灵号（`readInt()`，`:2840`）。
  - P2：按钮编号（`readInt()`，`:2841`），需 ≥1（`:2843`）。
  - P3：控制串（`readStr()`，`:2858`），写入 `exbtn_ctl[1]`（`:2863`）。
- 可选参数：无。
- 语义：与 `exbtn` 相同，差别只在成立条件要求该精灵 `num_of_cells >= 2`（`:2847`），即按 cell 做命中判定；不满足（含 P1 越界、无 `image_surface`、`num_of_cells < 2`）时读掉 P3 后静默返回（`:2848-2850`）。成立时新建按钮、插入按钮链并置 `is_exbtn_enabled = true`（`:2852-2855`），命中矩形取 `sprite_info[P1].pos`（`:2865-2868`）。
- 坐标：否（矩形来自精灵自身状态，脚本侧无像素参数）。

### `cselbtn`
- 处理函数：`cselbtnCommand` — `ONScripter_command.cpp:3200`（`ONScripter_lut.cpp:339`）
- 语法：`cselbtn <选项序号>, <按钮编号>, <x>, <y>`
- 参数：
  - P1 `csel_no`：`csel` 选项表下标，**0 起**（`readInt()`，`:3202`；匹配循环 `:3210-3215`）。
  - P2 `button_no`：按钮编号（`readInt()`，`:3203`），写入 `ButtonLink::no`（`:3223`），即 `btnwait` 返回给脚本的按钮号。
  - P3 `x`：选项文本左上角 x（`readInt()` → `csel_info.top_xy[0]`，`:3207`）。
  - P4 `y`：选项文本左上角 y（`readInt()` → `csel_info.top_xy[1]`，`:3208`）。
- 可选参数：无。四个参数在查表之前就被无条件读完（`:3202-3208`），选项不存在时也不会少读。
- 语义：以 `sentence_font` 的副本建立临时 `FontInfo`（`:3205-3206`），行宽按选项文本长度设为 `strlen(text)/2+1` 个半角（`:3219`，`FontInfo::setLineArea` 见 `FontInfo.cpp:227-231`），清空坐标后由 `getSelectableSentence()` 把文本渲染成按钮（`:3221`；`ONScripter.cpp:1334-1374`），插入按钮链并把 P2/P1 分别记入 `no`/`sprite_no`（`:3222-3224`）。
- 坐标：**是**（P3/P4）。`top_xy` 经 `FontInfo::x()/y()` 变成 `orig_pos`（`FontInfo.cpp:183-197`，`clear()` 已把 `xy` 归零，`FontInfo.cpp:205-212`），再由 `scalePosXY()` 换算成 `pos`（`ONScripter.cpp:1358-1360`）。
- 证据：`:3205-3227`。
- 备注：
  - 选项表由 `csel`（`selectCommand` 的 `SELECT_CSEL_MODE`）建立，`csel` 只存文本/标签、**不**自动生成按钮（`ONScripter_command.cpp:790-791`、`:877-888`），所以每个选项都要单独 `cselbtn` 摆放。
  - 选项不存在或文本为空时，读完 4 个参数后直接 `return`（`:3216-3217`）。
  - 副作用：把渲染用的 `csel_info.ttf_font[0..1]` 回写到全局 `sentence_font`（`:3226-3227`）。

### `cselgoto`
- 处理函数：`cselgotoCommand` — `ONScripter_command.cpp:3180`（`ONScripter_lut.cpp:338`）
- 语法：`cselgoto <选项序号>`
- 参数：
  - P1：`csel` 选项表下标，**0 起**（`readInt()`，`:3182`；匹配循环 `:3184-3189`）。
- 可选参数：无。
- 语义：按下标取出 `SelectLink`，取不到直接 `errorAndExit("cselgoto: no select link")`（`:3190`）；取到则 `setCurrentLabel(link->label)` 跳转，随后 `deleteSelectLink()` 清空选项表并 `newPage()`（`:3192-3195`）。
- 坐标：否。
- 备注：`label` 在 `csel` 解析时已去掉前导 `*`（`ONScripter_command.cpp:830-831`，`getStringBuffer()+1`），因此跳转目标是标签名本身。

### `csp`
- 处理函数：`cspCommand` — `ONScripter_command.cpp:3131`（`ONScripter_lut.cpp:337`）
- 语法：`csp <精灵号>`
- 参数：
  - P1：`sprite_info` 精灵号（`readInt()`，`:3138`）；`-1` = 全部（`:3150`），`0..999` = 单个（`:3166`）。
- 可选参数：无。
- 语义：先 `leaveTextDisplayMode()`（`:3133`）；对每个目标，把可见精灵的 `pos` 加入脏区，若已有 `image_name` 则把 `orig_pos` 置 `-1000` 并重算 `pos`（`:3151-3162`），非 `csp2` 还会移除该号精灵按钮（`:3163`、`:3173`），最后 `si[i].remove()` 释放图像（`:3164`、`:3174`）。
- 坐标：否（内部改写 `orig_pos`，但脚本侧无坐标参数）。
- 备注：精灵总数 `MAX_SPRITE_NUM = 1000`（`ONScripter.h:58`）。

### `csp2`
- 处理函数：`cspCommand` — `ONScripter_command.cpp:3131`（`ONScripter_lut.cpp:336`）
- 语法：`csp2 <精灵号>`
- 参数：
  - P1：`sprite2_info` 精灵号（`readInt()`，`:3138`）；`-1` = 全部（`:3150`）。
- 可选参数：无。
- 语义：与 `csp` 相同流程，但作用对象是 `sprite2_info`、总数 `MAX_SPRITE2_NUM = 256`（`:3142`，`ONScripter.h:59`）；脏区用 `bounding_rect` 而非 `pos`（`:3153-3154`、`:3168-3169`），且不移除按钮（`:3163`、`:3173` 的 `!csp2_flag` 分支）。
- 坐标：否。
- 备注：单编号分支的边界判断仍写 `no < MAX_SPRITE_NUM`（`:3166`），而 `csp2` 的数组只有 `MAX_SPRITE2_NUM = 256` 项（`:3142`），故 `csp2 256..999` 会越界访问 `sprite2_info`——实际运行表现**待验证**。

## D

### `date`
- 处理函数：`dateCommand` — `ScriptParser_command.cpp:1341`
- 语法：`date %年, %月, %日`
- 参数：
  - P1：年（两位）写入的整型变量（`readInt()` 定位，`:1346-1347`）。
  - P2：月写入的整型变量（`:1349-1350`）。
  - P3：日写入的整型变量（`:1352-1353`）。
- 可选参数：无。
- 语义：取本地时间 `localtime()`（`:1343-1344`）；P1 = `tm_year % 100`、P2 = `tm_mon + 1`、P3 = `tm_mday`（`:1347`、`:1350`、`:1353`）。
- 坐标：否。
- 备注：`readInt()` 解析到 `%N` 时会把 `current_variable` 定位到该变量（`ScriptHandler.cpp:1473-1478`），`setInt()` 再写回（`:760-770`），所以必须写变量引用；`?数组[...]` 亦可。若写裸数字，该数字会被当作变量号写入（同一机制，见 `dec` 节）。

### `dec`
- 处理函数：`decCommand` — `ScriptParser_command.cpp:1333`
- 语法：`dec %变量`
- 参数：
  - P1：被自减的整型变量（`readInt()` 取当前值，`:1335`），结果 `val-1` 写回该变量（`:1336`）。
- 可选参数：无。
- 语义：原地减 1。
- 坐标：否。
- 备注：`readInt()` 里 `parseInt()` 遇到 `%N` 会置 `current_variable = VAR_INT`（`ScriptHandler.cpp:1473-1478`），`setInt()` 因此写回同一变量（`:760-770`）。若 P1 写成裸数字，`parseInt()` 置 `VAR_INT|VAR_CONST` 且把该数字当变量号（`ScriptHandler.cpp:1541-1542`），`setInt()` 不拒绝 `VAR_CONST`，会写到同号变量——属代码事实，正常用法应写 `%N`/`?N[...]`。

### `defaultfont`
- 处理函数：`defaultfontCommand` — `ScriptParser_command.cpp:1323`
- 语法：`defaultfont <字体名>`
- 参数：
  - P1：默认字体名（`readStr()`，`:1328`），写入 `default_env_font`。
- 可选参数：无。
- 语义：仅允许在 `define` 段（`:1325-1326`），否则 `errorAndExit("defaultfont: not in the define section")`。
- 坐标：否。
- 备注：默认值 `DEFAULT_ENV_FONT = "simhei"`（`ONScripter.cpp:45`、`:1483-1484`）。

### `defaultspeed`
- 处理函数：`defaultspeedCommand` — `ScriptParser_command.cpp:1313`
- 语法：`defaultspeed <低速>, <中速>, <高速>`
- 参数：
  - P1：低速档（`default_text_speed[0]`，`:1318` 循环第 1 次）。
  - P2：中速档（`[1]`）。
  - P3：高速档（`[2]`）。
- 可选参数：无（固定 3 次 `readInt()`，`:1318`）。
- 语义：单位为**毫秒/字符**，在 `sentence_font.wait_time == -1` 时按 `text_speed_no` 取用并 `waitEvent(wait_time)`（`ONScripter_text.cpp:878-886`）；仅 `define` 段（`:1315-1316`）。
- 坐标：否（时间）。
- 备注：默认 40/20/10（`ScriptParser.cpp:39-41`）。

### `definereset`
- 处理函数：`defineresetCommand` — `ONScripter_command.cpp:3109`
- 语法：`definereset`
- 参数：无。
- 可选参数：无。
- 语义：`saveGlovalData()` 写回全局变量（`:3111`）→ `script_h.reset()`、`ScriptParser::reset()`、`reset()`（`:3113-3115`）→ `setCurrentLabel("define")`（`:3117`）→ 若 `gloval.sav` 存在则 `readVariables()` 恢复变量（`:3119-3120`）；Lua 重新 `init`（`:3122-3124`）；最后 `current_mode = DEFINE_MODE`（`:3126`）。
- 坐标：否。

### `defmp3vol`
- 处理函数：`defmp3volCommand` — `ScriptParser_command.cpp:1306`
- 语法：`defmp3vol <音量>`
- 参数：
  - P1：MP3/BGM 初始音量（`readInt()`，`:1308`），写入 `music_volume`。
- 可选参数：无。
- 语义：直接赋给全局 `music_volume`；播放时按百分比换算 `Mix_VolumeMusic(music_volume * MIX_MAX_VOLUME / 100)`（`ONScripter_command.cpp:1399`、`ONScripter_sound.cpp:109`）。
- 坐标：否（音量）。
- 备注：取值范围按百分比约定 0–100（`DEFAULT_VOLUME = 100`，`ONScripter.h:64`；读档缺省 `ONScripter.cpp:1484`），命令内无钳制。代码**没有** define 段检查，但惯例放在 `define` 段作初始值；运行中改音量应用 `mp3vol`。

### `defsevol`
- 处理函数：`defsevolCommand` — `ScriptParser_command.cpp:1299`
- 语法：`defsevol <音量>`
- 参数：
  - P1：音效初始音量（`readInt()`，`:1301`），写入 `se_volume`。
- 可选参数：无。
- 语义：播放音效时按 `se_volume * MIX_MAX_VOLUME / 100` 设通道音量（`ONScripter_command.cpp:649-655`，`ONScripter_sound.cpp:190`）。
- 坐标：否（音量）。
- 备注：同 `defmp3vol`，0–100 百分比、无钳制、无 define 段检查。

### `defsub`
- 处理函数：`defsubCommand` — `ScriptParser_command.cpp:1285`
- 语法：`defsub <命令名>`
- 参数：
  - P1：自定义命令名（`readLabel()`，`:1287`；`readLabel` 会把字母统一小写，`ScriptHandler.cpp:318-355`）。
- 可选参数：无。
- 语义：仅当首字符为 `a`–`z` 时，把 P1 追加进 `user_func_hash[首字母]` 桶（`:1289-1293`）；注册后脚本里出现同名命令时，按同名标签执行 `gosubReal()`（`ONScripter.cpp:1126-1145`）。
- 坐标：否。
- 备注：
  - 用户函数检查发生在内建 `func_lut` 之前（`ONScripter.cpp:1126-1151`），因此可覆盖内建命令名。
  - 与 `luasub` 结构相同，后者额外置 `lua_flag`（`ScriptParser_command.cpp:746-759`）。
  - 首字符非 `a`–`z` 时静默忽略（`:1289`），不会报错。

### `defvoicevol`
- 处理函数：`defvoicevolCommand` — `ScriptParser_command.cpp:1278`
- 语法：`defvoicevol <音量>`
- 参数：
  - P1：语音初始音量（`readInt()`，`:1280`），写入 `voice_volume`。
- 可选参数：无。
- 语义：voice 通道（通道 0）按 `voice_volume * MIX_MAX_VOLUME / 100` 设音量（`ONScripter_command.cpp:163-164`，`ONScripter_sound.cpp:188`）。
- 坐标：否（音量）。
- 备注：同 `defmp3vol`，0–100 百分比、无钳制、无 define 段检查。

### `delay`
- 处理函数：`delayCommand` — `ONScripter_command.cpp:3096`
- 语法：`delay <毫秒>`
- 参数：
  - P1：等待时长，毫秒（`readInt()`，`:3098`）。
- 可选参数：无。
- 语义：先判断跳过：`skip_mode & SKIP_NORMAL` 或按住 Ctrl 时直接返回（`:3100-3101`）；否则 `event_mode = WAIT_TIMER_MODE | WAIT_INPUT_MODE`，`waitEvent(val)`（`:3103-3104`）。`waitEvent` 对正数按 `SDL_GetTicks() + count` 作绝对超时（`ONScripter_event.cpp:295-297`）。
- 坐标：否（时间）。
- 备注：`val == 0` 时不等待（`count > 0` 才加时限，`ONScripter_event.cpp:297`）；负值的实际行为（无超时定时器）**待验证**。

### `deletescreenshot`
- 处理函数：`deletescreenshotCommand` — `ONScripter_command.cpp:3087`
- 语法：`deletescreenshot`
- 参数：无。
- 可选参数：无。
- 语义：若 `screenshot_surface` 存在则 `SDL_FreeSurface()` 并置 `NULL`（`:3089-3092`）。
- 坐标：否。
- 备注：该缓存由 `getscreenshot` 分配（按 `screenshot_w/h` 抓取累积画面，`:2333-2351`）或由 `savescreenshot` 首次使用时分配（`:975-976`）。

### `dim`
- 处理函数：`dimCommand` — `ScriptParser_command.cpp:1268`
- 语法：`dim ?<数组号>[<维1>][<维2>]…`
- 参数：
  - P1：整条数组声明，交给 `script_h.declareDim()`（`:1273`）；`declareDim()` 直接 `parseArray()` 解析 `?号[维]…`（`ScriptHandler.cpp:1712`、`:1656-1676`），维度可写表达式。
- 可选参数：无（`declareDim()` 自行扫到非 `[` 为止，`ScriptHandler.cpp:1665-1672`）。
- 语义：仅 `define` 段可用（`:1270-1271`）；声明后每维实际大小 = 指定值 + 1（`ScriptHandler.cpp:1717`），即 `dim ?0[10]` 合法下标为 0..10；数据清零（`:1720-1721`）。
- 坐标：否。
- 备注：一条 `dim` 只声明一个数组，多个数组需多行；`parseArray` 对 `[` 个数无上限检查（`ScriptHandler.cpp:1665-1673`）。

## E

### `effect`
- 处理函数：`effectCommand` — `ScriptParser_command.cpp:1232`（与 `windoweffect` 共用；`ONScripter_lut.cpp:305`）
- 语法：`effect <定义号>, <效果类型>[, <时长>[, <图像名>]]`
- 参数：
  - P1：效果**定义号**（`readInt()`，`:1244`），须 2–255，否则 `errorAndExit("effect: effect number out of range")`（`:1245-1246`）。
  - P2：效果**类型**号（`readEffect()` 内 `readInt()`，`ScriptParser.cpp:668`）。
  - P3：`[可选]` 时长（毫秒），仅当 P2 后跟逗号（`END_COMMA`）时读取（`ScriptParser.cpp:669-671`）。
  - P4：`[可选]` 图像名（`readStr()`），再跟一个逗号时读取（`ScriptParser.cpp:672-676`）；不跟则 `effect->anim.remove()`（`:677-678`）。
- 可选参数：**有**（P3/P4，由 `readEffect()` 的两级 `END_COMMA` 判定，`ScriptParser.cpp:669`、`:672`）。
- 语义：仅 `define` 段可用（`:1240-1241`）；新建 `EffectLink` 挂到 `last_effect_link` 链（`:1243-1249`），供 `bg` 等命令按 `no` 查表（`ScriptParser.cpp:689-708`）。
- 坐标：否。
- 备注：P2 的 0–255 范围检查只在**没有**逗号时执行，越界会警告并归 0（`ScriptParser.cpp:680-683`）；带 P3/P4 时不做该检查。

### `effectblank`
- 处理函数：`effectblankCommand` — `ScriptParser_command.cpp:1215`
- 语法：`effectblank <毫秒>`
- 参数：
  - P1：效果结束后的空白等待时长（`readInt()`，`:1220`），写入 `effect_blank`。
- 可选参数：无。
- 语义：仅 `define` 段（`:1217-1218`）；`doEffect()` 收尾时若 `effect_blank != 0` 且 `effect_counter != 0`，执行 `waitEvent(effect_blank)`（`ONScripter_effect.cpp:474-475`）。
- 坐标：否（时间）。
- 备注：默认 10（`ScriptParser.cpp:240`）。

### `effectcut`
- 处理函数：`effectcutCommand` — `ScriptParser_command.cpp:1205`
- 语法：`effectcut`
- 参数：无。
- 可选参数：无。
- 语义：仅 `define` 段（`:1207-1208`）；置 `effect_cut_flag = true`（`:1210`）。置位后，跳过/Ctrl 状态下时长被压为 0、效果号被替换为 1（立即完成）（`ONScripter_effect.cpp:94-103`、`:118-119`）。
- 坐标：否。
- 备注：默认 `false`（`ScriptParser.cpp:242`）；源码无对应“关闭”命令，只在解析器重置时恢复默认。

### `effectskip`
- 处理函数：`effectskipCommand` — `ScriptParser_command.cpp:1225`
- 语法：`effectskip <0|1>`
- 参数：
  - P1：`readInt()` 结果写入 `effect_skip_flag`（`:1227`），非 0 = 允许点击立即结束效果。
- 可选参数：无。
- 语义：`doEffect()` 中若未处于自动模式/自动点击/滚轮等状态且 `effect_skip_flag` 非 0，把 `effect_counter` 直接置为 `effect_duration` 结束效果（`ONScripter_effect.cpp:454-459`）。
- 坐标：否。
- 备注：默认 1（`ScriptParser.cpp:241`）；无 define 段检查。

### `end`
- 处理函数：`endCommand` — `ONScripter_command.cpp:2880`
- 语法：`end`
- 参数：无。
- 可选参数：无。
- 语义：`quit()` → `stopSMPEG()` → `exit(0)`（`:2882-2884`）；`return RET_CONTINUE` 是永不执行的占位（`:2885`）。
- 坐标：否。

### `english`
- 处理函数：`englishCommand` — `ScriptParser_command.cpp:1194`
- 语法：`english`
- 参数：无。
- 可选参数：无。
- 语义：仅 `define` 段（`:1196-1197`）；`script_h.setEnglishMode(true)`（`:1200`）。开启后 `>` 起始的文本按“英文行”整段取出（忽略 TAB，置 `END_1BYTE_CHAR`）（`ScriptHandler.cpp:272-284`），且 `>` 不再按普通文本字符处理（`:213`）。
- 坐标：否。
- 备注：源码仅能开启，没有对应关闭命令（`english_mode` 只在 `ScriptHandler::reset()` 中恢复 `false`，`ScriptHandler.cpp:128`）。

### `erasetextwindow`
- 处理函数：`erasetextwindowCommand` — `ONScripter_command.cpp:2873`
- 语法：`erasetextwindow <0|1|2>`
- 参数：
  - P1：`readInt()` 结果写入 `erase_text_window_mode`（`:2875`）。
- 可选参数：无。
- 语义：非 0 时，离开文本显示模式会擦除文本窗口（跑一次 `window_effect`）；为 0 则保留窗口（`ONScripter_text.cpp:474-484`）。
- 坐标：否。
- 备注：默认 1（`ONScripter.cpp:762`）；存档中把 2 单独区分（`ONScripter_file2.cpp:340`、`:579`、`:634`），1 与 2 的行为差异**待验证**。

### `exbtn`
- 处理函数：`exbtnCommand` — `ONScripter_command.cpp:2819`（`ONScripter_lut.cpp:298`）
- 语法：`exbtn <精灵号>, <按钮编号>, <控制串>`
- 参数：
  - P1：作为按钮外观的精灵号（`readInt()`，`:2840`），须 0 ≤ P1 < 1000 且该精灵有 `image_surface`、`num_of_cells > 0`（`:2843-2847`）。
  - P2：按钮编号（`readInt()`，`:2841`），须 ≥1（`:2843`）。
  - P3：控制串（`readStr()`，`:2858`），写入 `exbtn_ctl[1]`（`:2863`）。
- 可选参数：无。
- 语义：条件不满足时读掉 P3 静默返回（`:2848-2850`）；满足则新建 `ButtonLink`、插入按钮链、置 `is_exbtn_enabled = true`（`:2852-2855`），并把 `button_type`/`sprite_no`/`no` 写好（`:2860-2862`），命中矩形取 `sprite_info[P1].pos`（`:2865-2868`）。
- 坐标：否。
- 备注：控制串子命令 `C`/`P`/`S`/`M` 的含义见 `decodeExbtnControl()`（`ONScripter.cpp:1377-1435`）；按钮自身 `ctl[1]` 为空时回退到 `exbtn_d`/`bdef` 设置的默认串（`ONScripter.cpp:1006-1011`）。

### `exbtn_d`
- 处理函数：`exbtnCommand` — `ONScripter_command.cpp:2819`（`ONScripter_lut.cpp:297`）
- 语法：`exbtn_d <控制串>`
- 参数：
  - P1：默认控制串（`readStr()`，`:2858`）→ `exbtn_d_button_link.exbtn_ctl[1]`（`:2863`）。
- 可选参数：无。
- 语义：先清空 `exbtn_ctl[0..2]`（`:2826-2832`），再写入 P1；作为所有未单独设置控制串的 exbtn 按钮的默认值（`ONScripter.cpp:989-990`、`:1006-1011`）。
- 坐标：否（同 `bdef`，`M` 子命令可含坐标但不属参数）。
- 备注：与 `bdef` 完全同义。

### `exec_dll`
- 处理函数：`exec_dllCommand` — `ONScripter_command.cpp:2742`（`ONScripter_lut.cpp:296`）
- 语法：`exec_dll <dll名>/<函数名>`
- 参数：
  - P1：单字符串（`readStr()`，`:2744`），以**第一个 `/`** 切分为 DLL 名与函数名（`:2747-2751`）。
- 可选参数：无。
- 语义：`fileutil.dll/mkdir <路径>` 特例——在 `archive_path` 前辍下 `mkdir(...,0755)`（Windows 用 `_mkdir`）（`:2753-2766`）。其他情况打开 `dll_file`（默认 `"dll.txt"`，`ONScripter.cpp:44`、`:367`），查找 `[dll名]` 段，读取其中的 `str = "…"` 写入 `getret_str`、`ret = …` 写入 `getret_int`（`:2768-2816`），找不到仅打印错误。
- 坐标：否。
- 备注：切分循环 `while(buf[c] != '/')` 没有边界与 NUL 判断（`:2747`），P1 不含 `/` 时会读越界——实际后果**待验证**，不建议省略 `/`。

## F

### `fileexist`
- 处理函数：`fileexistCommand` — `ONScripter_command.cpp:2731`
- 语法：`fileexist %结果, <文件名>`
- 参数：
  - P1：结果写入的整型变量（`readInt()` + `pushVariable()`，`:2733-2734`）。
  - P2：文件名（`readStr()`，`:2735`）。
- 可选参数：无。
- 语义：用 `script_h.cBR->getFileLength(P2) > 0` 判定（`:2737`），存在写 1，否则 0；对归档内的文件同样有效（走 `cBR`）。
- 坐标：否。

### `filelog`
- 处理函数：`filelogCommand` — `ScriptParser_command.cpp:1183`
- 语法：`filelog`
- 参数：无。
- 可选参数：无。
- 语义：仅 `define` 段（`:1185-1186`）；置 `filelog_flag = true` 并 `readLog(FILE_LOG)` 从日志文件载入已读文本（`:1188-1189`；`readLog` 见 `ScriptParser.cpp:567-588`）。
- 坐标：否。

### `flushout`
- 处理函数：`flushoutCommand` — `ONScripter_command.cpp:2710`
- 语法：`flushout <毫秒>`
- 参数：
  - P1：特效时长（毫秒），写入 `tmp_effect.duration`（`:2715`）。
- 可选参数：无。
- 语义：效果号固定为 `MAX_EFFECT_NUM + 3 = 259`（`:2716`，`MAX_EFFECT_NUM = 256`，`ONScripter.h:62`），全屏脏区（`:2718`），白屏过渡 30 阶段（注释 `:2712-2714`）：`setEffect()` 失败即返回（`:2720`），否则用白色背景 + 当前累积画面做 `doEffect()` 循环（`:2722-2726`）。
- 坐标：否（内部用整屏 `dirty_rect`，脚本侧无像素参数）。

### `for`
- 处理函数：`forCommand` — `ScriptParser_command.cpp:1132`（`ONScripter_lut.cpp:290`）
- 语法：`for %变量 = <起始值> to <终止值> [step <步长>]`
- 参数：
  - P1：循环变量（`readVariable()`，`:1140`），类型必须**恰好**是 `VAR_INT`（`:1141-1142`），即只接受 `%N`，`$N`/`?N` 会 `errorAndExit("for: no integer variable.")`。
  - P2：起始值表达式（`readInt()`，`:1152`），立即写入 P1（`:1153`）。
  - P3：终止值（`readInt()`，`:1160`）。
  - P4：`[可选]` 步长（`readInt()`，`:1164`），缺省 1（`:1166-1167`）。
- 可选参数：**有**，但是“关键字可选”而非 `END_COMMA`：`compareString("step")` 命中才读步长（`:1162-1168`）。
- 语义：新建 `NestInfo{nest_mode = FOR}`（`:1134-1138`），记录变量号（`:1144`）、终止值、步长，并把 `next_script` 存为循环体起点（`:1178`）；若 `from` 已越过 `to`（步长为正时 `from > to`，为负时 `from < to`）则置 `break_flag = true`（`:1170-1174`）。循环回跳由 `next` 完成（`:545-574`）。
- 坐标：否。
- 备注：`to`/`step` 关键字由 `readLabel()` 消费——`readLabel` 接受字母开头并小写化，因此关键字被当成“标签”读掉（`:1158`、`:1163`；`ScriptHandler.cpp:318-355`），数字则直接落到随后的 `readInt()`。`=` 由 `setCurrent(getNext()+1)` 手工跳过（`:1151`）。

## G

### `game`
- 处理函数：`gameCommand` — `ONScripter_command.cpp:2654`（`ONScripter_lut.cpp:288`）
- 语法：`game`
- 参数：无。
- 可选参数：无。
- 语义：仅 `define` 段（`:2656-2657`）；切到 `NORMAL_MODE`（`:2660`），为 4 张回看图补默认图名并 `parseTaggedString()`/`setupAnimationInfo()`（`:2663-2682`，默认名 `DEFAULT_LOOKBACK_NAME0 = "uoncur.bmp"`，`ScriptParser.h:56`），建立 `max_page_list` 页链表（`:2686-2692`），`resetCommand()`（`:2694`），装载两个光标（`:2696-2697`），Lua 载入 init 脚本并触发 `reset` 回调（`:2699-2705`）。
- 坐标：否。

### `getcselnum`
- 处理函数：`getcselnumCommand` — `ONScripter_command.cpp:2639`（`ONScripter_lut.cpp:285`）
- 语法：`getcselnum %结果`
- 参数：
  - P1：结果写入的整型变量（`readInt()`，`:2648`；`setInt()` 写回 `:2649`）。
- 可选参数：无。
- 语义：遍历 `root_select_link` 计数（`:2641-2647`），把 `csel` 选项条数写入 P1；**空文本项也计数**（循环未检查 `text`）。
- 坐标：否。

### `getcselstr`
- 处理函数：`getcselstrCommand` — `ONScripter_command.cpp:2620`（`ONScripter_lut.cpp:284`）
- 语法：`getcselstr $结果, <选项序号>`
- 参数：
  - P1：结果字符串变量（`readVariable()` + `pushVariable()`，`:2622-2623`；按 `$N` 使用）。
  - P2：`csel` 选项表下标，**0 起**（`readInt()`，`:2625`；匹配循环 `:2627-2632`）。
- 可选参数：无。
- 语义：取第 P2 项的 `text`，`setStr()` 复制进 P1 的字符串槽（`:2634`）；下标越界时传 `NULL`，`setStr()` 会把该字符串清空（`ScriptParser.cpp:617-621`）。
- 坐标：否。
- 备注：代码不校验 P1 类型（`:2622`、`:2634`），传 `%N`/`?N` 会写到同号变量槽的 `str` 字段而不报错；正常应写 `$N`。

## W

### `windoweffect`
- 处理函数：`effectCommand` — `ScriptParser_command.cpp:1232`（`ONScripter_lut.cpp:32`）
- 语法：`windoweffect <效果类型>[, <时长>[, <图像名>]]`
- 参数：
  - P1：效果**类型**号（`readEffect()` 内 `readInt()`，`ScriptParser.cpp:668`）——注意**没有**定义号。
  - P2：`[可选]` 时长（毫秒），P1 后跟逗号时读取（`ScriptParser.cpp:669-671`）。
  - P3：`[可选]` 图像名（`readStr()`），再跟一个逗号时读取（`ScriptParser.cpp:672-676`）。
- 可选参数：**有**（P2/P3，两级 `END_COMMA`，`ScriptParser.cpp:669`、`:672`）。
- 语义：`isName("windoweffect")` 命中时直接作用于全局 `window_effect`，**不**读定义号、**不**要求 `define` 段（`:1236-1238`）；该效果在进入/离开文本显示模式时播放（`ONScripter_text.cpp:467`、`:482`）。
- 坐标：否。
- 备注：与 `effect` 共用 `readEffect()`，但跳过了 `effect` 分支的 2–255 定义号检查（`:1244-1246`）。

---

## 补遗：本册漏项补齐

> 说明：分册 02 首轮生成时遗漏了下列 4 条命令，现按同一模板补齐（均经源码复核）。

### `drawbg`
- 处理函数：`drawbgCommand` — `ONScripter_command.cpp:3052`
- 语法：`drawbg`
- 参数：无。
- 语义：把当前背景 `bg_info` 按它自己的 `pos` 叠加到累积画面，裁剪区为整屏（`:3054-3058`）。背景位置由 `bg` 命令与 `createBackground()` 决定（`ONScripter_image.cpp:836-842` 把 `pos` 置 0）。
- 坐标：否（无脚本参数；位置来自背景状态）。

### `drawclear`
- 处理函数：`drawclearCommand` — `ONScripter_command.cpp:3045`
- 语法：`drawclear`
- 参数：无。
- 语义：用 `SDL_FillRect` 把累积画面填成黑色（RGBA 0,0,0,255），不做刷新（`:3047`）。刷新需另用 `draw`/`repaint`。
- 坐标：否。

### `drawfill`
- 处理函数：`drawfillCommand` — `ONScripter_command.cpp:3034`
- 语法：`drawfill <R>, <G>, <B>`
- 参数：
  - P1/P2/P3：红/绿/蓝，0–255，直接传给 `SDL_MapRGBA(..., 0xff)`（`:3036-3040`）。
- 语义：用指定颜色填满累积画面；不做刷新。
- 坐标：否。

### `drawtext`
- 处理函数：`drawtextCommand` — `ONScripter_command.cpp:2950`
- 语法：`drawtext`
- 参数：无。
- 语义：把 `text_info` 图层叠加到累积画面 `(0,0)`，裁剪区为整屏（`:2952-2956`）。
- 坐标：否（无脚本参数；绘制原点固定 0,0）。

### `dwavestop`
- 处理函数：`dwavestopCommand` — `ONScripter_command.cpp:2888`
- 语法：`dwavestop <通道>`
- 参数：
  - P1：通道号（0…`ONS_MIX_CHANNELS-1`，越界钳制，`:2890-2892`）。
- 语义：若该通道有样本，`Mix_Pause` + `Mix_FreeChunk` + 指针置空（`:2894-2898`）。
- 坐标：否。

### `div`
- 处理函数：`divCommand` — `ScriptParser_command.cpp:1257`
- 语法：`div <被除数>, <结果变量>, <除数>`
- 参数：P1 被除数（`readInt()`）；P2 结果变量（`pushVariable()`）；P3 除数（`readInt()`）；结果 `val1 / val2` 写入 P2（`:1259-1263`）。
- 可选参数：无。
- 语义：整数除法；**除数为 0 未检查**（会触发硬件异常/未定义行为）。
- 坐标：否。

### `dwave` / `dwaveload` / `dwaveloop` / `dwaveplay` / `dwaveplayloop`
- 处理函数：`dwaveCommand` — `ONScripter_command.cpp:2903`
- 语法：`dwave <通道>, <文件名>`（`dwaveload` 只预加载）
- 参数：
  - P1 通道号（0…`ONS_MIX_CHANNELS-1`，越界钳制，`:2923-2925`）。
  - P2 文件名（`readStr()`；仅 `dwave`/`dwaveload`/`dwaveloop` 读取；`dwaveplay*` 复用已加载样本，不读文件名，`:2927-2935`）。
- 语义：按命令名决定 `WAVE_PLAY/WAVE_PRELOAD/WAVE_PLAY_LOADED` 与是否循环（`:2905-2921`）。
- 坐标：否。

### `draw`
- 处理函数：`drawCommand` — `ONScripter_command.cpp:3079`
- 参数：无。
- 语义：`flushDirect(screen_rect, REFRESH_NONE_MODE)` 后清空脏区（`:3081-3082`），即把累积画面推到屏幕。
- 坐标：否。

### `drawbg2`
- 处理函数：`drawbg2Command` — `ONScripter_command.cpp:3063`
- 语法：`drawbg2 <x>, <y>, <scale_x>, <scale_y>, <rot>`
- 参数：P1/P2 目标 x/y（像素，→ `orig_pos`）；P3/P4 缩放倍率；P5 旋转角（`:3066-3071`）。
- 语义：复制当前背景状态，设置仿射参数后 `blendOnSurface2` 绘制（`:3074`）。
- 坐标：**是**（P1/P2）。

### `drawsp`
- 处理函数：`drawspCommand` — `ONScripter_command.cpp:3013`
- 语法：`drawsp <精灵号>, <cell号>, <alpha>, <x>, <y>`
- 参数：P1 精灵号；P2 帧号；P3 透明度；P4/P5 目标 x/y（像素，乘 `screen_ratio` 后使用，`:3018-3019`）。
- 语义：临时切换 cell，按 alpha 混合到累积画面，再恢复原 cell（`:3021-3029`）。
- 坐标：**是**（P4/P5）。

### `drawsp2`
- 处理函数：`drawsp2Command` — `ONScripter_command.cpp:2992`
- 语法：`drawsp2 <精灵号>, <cell号>, <alpha>, <x>, <y>, <scale_x>, <scale_y>, <rot>`
- 参数：P1–P3 同上；P4/P5 x/y（像素）；P6/P7 缩放倍率；P8 旋转角（`:2994-3004`）。
- 坐标：**是**（P4/P5）。

### `drawsp3`
- 处理函数：`drawsp3Command` — `ONScripter_command.cpp:2961`
- 语法：`drawsp3 <精灵号>, <cell号>, <alpha>, <x>, <y>, <m00>, <m01>, <m10>, <m11>`
- 参数：
  - P1–P3 同上；P4/P5 x/y（像素，`:2966-2967`）。
  - P6–P9 2×2 变换矩阵系数（千分比；计算逆矩阵，`:2973-2984`）。
- 坐标：**是**（P4/P5；矩阵系数不是坐标）。

---

## 本册统计

- 处理函数：43 个
- 命令条目：52 条（含别名与重复注册）
- 其中含坐标参数：`cselbtn`、`drawbg2`、`drawsp`、`drawsp2`、`drawsp3`（共 5 条）
- 判定为「部分」：0 条（本册不存在“半坐标”参数——上述 5 条之外的参数全部是编号/时间/音量/颜色/标志/字符串/矩阵系数）
- 别名组：`csp`/`csp2`（`cspCommand`）、`dwave`/`dwaveload`/`dwaveplay`/`dwaveplayloop`/`dwaveloop`（`dwaveCommand`）、`effect`/`windoweffect`（`effectCommand`）、`exbtn`/`exbtn_d`/`cellcheckexbtn`/`bdef`（`exbtnCommand`）
- 未定位：0
- 标为「待验证」：`delay`（负值语义）、`csp2`（编号 256–999 越界）、`drawsp3`（矩阵量纲/逆矩阵）、`exec_dll`（P1 无 `/` 时越界）、`erasetextwindow`（1 与 2 的行为差异）、`div`（除数为 0 未检查）
