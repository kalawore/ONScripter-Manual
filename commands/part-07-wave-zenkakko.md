# 命令分册 07：处理函数 `waveCommand` – `zenkakkoCommand`

> 本册覆盖 `func_lut` 中处理函数位于 `waveCommand`…`zenkakkoCommand` 区间的全部命令（含别名），共 6 个处理函数、8 条命令。
> 行号均指快照 `src/onsyuri/`。参数按**源码读取顺序**列出，逗号分隔；`[ ]` 表示可选（由 `script_h.getEndStatus() & END_COMMA` 判断）。本册 8 条命令的处理函数均**没有** `END_COMMA` 分支，即没有真正意义上的可选参数（个别参数“可留空”属于空串行为，见各节备注）。
> 「坐标」列：**是** = 该命令含逻辑坐标/尺寸参数，需在 ×2 转换中处理；**否** = 不含；**部分** = 仅部分参数是坐标。
> 判定依据：参数是否直接写入 `orig_pos.x/y/w/h`、`pos`、`image_rect`、`top_xy`、`num_xy`、`font_size_xy`、`pitch_xy`、`max_width`、`dst_rect/src_rect` 等坐标字段，或与 `screen_width/height` 做像素运算。

---

## O

### `okcancelbox`
- 处理函数：`yesnoboxCommand` — `ONScripter_command.cpp:49`
- 注册：`{"okcancelbox", &ONScripter::yesnoboxCommand}`（`ONScripter_lut.cpp:142`）
- 语法：`okcancelbox <结果变量>, <正文>, <标题>`
- 参数：
  - P1：结果写入的数值变量。`readInt()` 读取（可写 `%N` 或 `?数组`），紧接 `pushVariable()` 记录变量信息（`ONScripter_command.cpp:54-55`）；确认写 **1**、取消/未选择写 **0**（`ONScripter_command.cpp:72-80`）。
  - P2：正文文本（`readStr()`），用 `saveStringBuffer()` 暂存为 `mes1`（`ONScripter_command.cpp:57-58`）。绘制在对话框主体区，最多 3 行（`ONScripter_rmenu.cpp:722-727`）。
  - P3：标题/眉首文本（`readStr()`，`ONScripter_command.cpp:59`）。绘制在对话框顶部 header 区（`ONScripter_rmenu.cpp:729-733`；`DIALOG_HEADER = 32`，`ONScripter_rmenu.cpp:31`）。
- 可选参数：无。三个参数均被无条件读取（`ONScripter_command.cpp:54`、`:57`、`:59`）。
- 语义：先摘下并清空现有按钮链（`ONScripter_command.cpp:60-61`），`buildDialog()` 生成对话框与两个临时按钮（`ONScripter_command.cpp:62`；`ONScripter_rmenu.cpp:799-813`），置 `show_dialog_flag` 并把 `dialog_info.pos` 加入脏矩形后刷新（`ONScripter_command.cpp:64-66`）。随后进入 `WAIT_BUTTON_MODE` 调用 `waitEvent(-1)` **无限阻塞**，直到点到 Yes/OK、No/Cancel，或右键按“未选择”退出（`ONScripter_command.cpp:68-81`）。退出后恢复原按钮链、再刷新一次（`ONScripter_command.cpp:83-88`）。
- 按钮文本：`yesnobox` 为 Yes/No，`okcancelbox` 为 OK/Cancel（`ONScripter_rmenu.cpp:744-751`）。
- 坐标：否。脚本侧无坐标参数；对话框位置由 `buildDialog()` 用 `screen_width/height` 居中算出（`ONScripter_rmenu.cpp:739-740`），对话框尺寸为固定常量 `DIALOG_W=241`、`DIALOG_H=167`（`ONScripter_rmenu.cpp:29-30`）。
- 备注：
  - 右键等价于“未选择”→ 写 0（右键在 `WAIT_BUTTON_MODE` 下置 `button = -1`，`ONScripter_event.cpp:403-407`，处理见 `ONScripter_command.cpp:72-75`）。
  - 点击按钮之外的区域（`button == 0`，`ONScripter_event.cpp:417-421`）以及滚轮事件（若产生 `-2`/`-3`，`ONScripter_event.cpp:480-491`）都不在退出分支内，对话框只会继续等待；因此该命令是纯模态的。
  - 与 `yesnobox` 共用同一处理函数，差别只在按钮标签（`ONScripter_command.cpp:52`）。

## W

### `wave`
- 处理函数：`waveCommand` — `ONScripter_command.cpp:105`
- 注册：`{"wave", &ONScripter::waveCommand}`（`ONScripter_lut.cpp:37`）
- 语法：`wave <文件名>`
- 参数：
  - P1：wave 文件名（`readStr()`，`ONScripter_command.cpp:114`）。可为带引号字符串、`$N` 字符串变量或字符串别名（`ScriptHandler.cpp:1363-1383`、`:1418-1463`）。空串或归档内不存在该文件时不报错、静默不播放（`playSound()` 用 `getFileLength( filename ) == 0` 直接返回 `ONScripter_sound.cpp:76-77`）。
- 可选参数：无（`ONScripter_command.cpp:114` 无条件读取）。
- 语义：先置 `wave_play_loop_flag = false`（`ONScripter_command.cpp:107`）；因命令名不是 `waveloop`，不会置真（`ONScripter_command.cpp:109-110`）。然后调用 `wavestopCommand()` 停掉上一段 wave（`ONScripter_command.cpp:112`），把 P1 写入 `wave_file_name`，以 `SOUND_CHUNK` 在 `MIX_WAVE_CHANNEL` 上播放（`ONScripter_command.cpp:114-115`）。
- 坐标：否。
- 备注：
  - 通道号 `MIX_WAVE_CHANNEL = ONS_MIX_CHANNELS + 0 = 50`（`ONScripter.h:65`、`:67`）；该通道音量取 `se_volume`（`ONScripter_sound.cpp:190`）。
  - 快进或按住 Ctrl 时 wave 会被跳过（`ONScripter_sound.cpp:78-82`，`mode_wave_demo_flag` 可解除）。
  - 播放状态可存档：`wave_file_name` 与循环标志都会被写入（`ONScripter_file2.cpp:562`、`:570`）；读档时需**循环标志为真且 `wave_file_name` 非空**才会自动重播（`ONScripter_file2.cpp:209`、`:223-226`），因此单次 `wave` 读档后不会自动重放。
  - 命令名分发是精确 `strcmp`（`ONScripter.cpp:1154`），所以 `wave` 与 `waveloop` 各自可达；处理函数内再用 `isName()` 前缀比较区分二者（`ScriptHandler.cpp:556-561`）。

### `waveloop`
- 处理函数：`waveCommand` — `ONScripter_command.cpp:105`
- 注册：`{"waveloop", &ONScripter::waveCommand}`（`ONScripter_lut.cpp:36`）
- 语法：`waveloop <文件名>`
- 参数：
  - P1：wave 文件名（`readStr()`，`ONScripter_command.cpp:114`），取值与 `$变量`/别名规则同 `wave` 一节。
- 可选参数：无。
- 语义：与 `wave` 完全相同，唯一区别是 `isName("waveloop")` 命中后把 `wave_play_loop_flag` 置真（`ONScripter_command.cpp:109-110`），`playSound()` 据此以 `-1` 循环次数播放（`ONScripter_sound.cpp:193`）。
- 坐标：否。
- 备注：循环占用 wave 通道；再执行 `wave`/`waveloop`/`wavestop` 都会先停止旧样本（`ONScripter_command.cpp:112`、`:95-99`）。循环标志随存档保存，读档时会按该标志自动重播（`ONScripter_file2.cpp:223-226`）。

### `wavestop`
- 处理函数：`wavestopCommand` — `ONScripter_command.cpp:93`
- 注册：`{"wavestop", &ONScripter::wavestopCommand}`（`ONScripter_lut.cpp:35`）
- 语法：`wavestop`
- 参数：无。
- 可选参数：无（无参数）。
- 语义：若 wave 通道上存在样本，则 `Mix_Pause()` → `Mix_FreeChunk()` → 指针置 `NULL`（`ONScripter_command.cpp:95-99`）；最后把 `wave_file_name` 置空（`ONScripter_command.cpp:100`）。
- 坐标：否。
- 备注：
  - 只作用于 `MIX_WAVE_CHANNEL`，不影响 BGM/循环 BGM 通道（通道定义见 `ONScripter.h:68-70`）。
  - **不**重置 `wave_play_loop_flag`（该标志只在 `wave`/`waveloop` 入口重置，`ONScripter_command.cpp:107`），执行后仍保留原值；但读档重播还要求 `wave_file_name` 非空（`ONScripter_file2.cpp:225`），而 `wavestop` 已把它置空（`ONScripter_command.cpp:100`），故实际不会因残留标志而重播。
  - 被 `wave`/`waveloop` 隐式调用（`ONScripter_command.cpp:112`）。

### `windowback`
- 处理函数：`windowbackCommand` — `ScriptParser_command.cpp:58`
- 注册：`{"windowback", &ONScripter::windowbackCommand}`（`ONScripter_lut.cpp:34`）
- 语法：`windowback`
- 参数：无。
- 可选参数：无（无参数）。
- 语义：仅允许在 `define` 段使用，否则 `errorAndExit("windowback: not in the define section")`（`ScriptParser_command.cpp:60-61`）；随后置 `windowback_flag = true`（`ScriptParser_command.cpp:62`）。
- 绘制顺序影响：开启后，`sprite2`、负片/单色处理、阴影与文字都在普通 `sprite_info` **之前**绘制（`ONScripter_image.cpp:740-756`），即文字层被立绘压住；关闭时这些内容改到 `sprite_info` 之后绘制（`ONScripter_image.cpp:769-780`、`:793-798`）。
- 坐标：否（纯开关标志）。
- 备注：默认 `false`（`ScriptParser.cpp:135`）；源码中没有对应的“关闭”命令，只在脚本重置时恢复默认（`ScriptParser.cpp:135`）。

### `windowchip`
- 处理函数：`windowchipCommand` — `ScriptParser_command.cpp:49`
- 注册：`{"windowchip", &ONScripter::windowchipCommand}`（`ONScripter_lut.cpp:33`）
- 语法：`windowchip <精灵号>`
- 参数：
  - P1：精灵编号（`readInt()`），直接写入 `windowchip_sprite_no`（`ScriptParser_command.cpp:53`）。建议取 `0`…`999`（`MAX_SPRITE_NUM = 1000`，`ONScripter.h:58`；`sprite_info` 按该数量分配，`ONScripter.cpp:376`）；`-1` 表示不指定，也是默认值（`ScriptParser.cpp:144`）。
- 可选参数：无。
- 语义：仅允许在 `define` 段使用（`ScriptParser_command.cpp:51-52`）。该命令只是**登记**精灵编号，本身不产生显示效果；登记的精灵在文字显示/隐藏时被一起切换：`texton` 置可见（`ONScripter_command.cpp:251-252`）、`textoff` 置不可见（`ONScripter_command.cpp:263-264`）、右键唤出“窗口擦除”时临时隐藏再恢复（`ONScripter_rmenu.cpp:278-279`、`:287-288`）。据这些使用点推断，其用途是“文字窗口边框/芯片精灵”（源码无注释说明）。
- 坐标：否（是精灵编号，不是坐标）。
- 备注：赋值与使用两处都**没有上界检查**（`ScriptParser_command.cpp:53`、`ONScripter_command.cpp:252`），传入 ≥1000 会越界访问 `sprite_info[]`，应避免。

## Y

### `yesnobox`
- 处理函数：`yesnoboxCommand` — `ONScripter_command.cpp:49`
- 注册：`{"yesnobox", &ONScripter::yesnoboxCommand}`（`ONScripter_lut.cpp:30`）
- 语法：`yesnobox <结果变量>, <正文>, <标题>`
- 参数：
  - P1：结果写入的数值变量（`readInt()` + `pushVariable()`，`ONScripter_command.cpp:54-55`）；Yes → **1**，No/未选择 → **0**（`ONScripter_command.cpp:72-80`）。
  - P2：正文文本（`readStr()`，暂存为 `mes1`，`ONScripter_command.cpp:57-58`），绘制在对话框主体、最多 3 行（`ONScripter_rmenu.cpp:722-727`）。
  - P3：标题/眉首文本（`readStr()`，`ONScripter_command.cpp:59`），绘制在顶部 header 区（`ONScripter_rmenu.cpp:729-733`）。
- 可选参数：无（无 `END_COMMA` 判断）。P3 在行尾省略时会读到空串（`parseStr()` 的空别名分支，`ScriptHandler.cpp:1444-1447`），标题区显示为空白——这是空串行为，不是语法上的可选参数。
- 语义：与 `okcancelbox` 同一处理函数、同一流程（`ONScripter_command.cpp:49-91`），仅按钮文本取 Yes/No（`ONScripter_rmenu.cpp:744-747`）。阻塞式等待；点 Yes/No 或右键（按 0 处理）才会返回。
- 坐标：否。无坐标参数；对话框由 `buildDialog()` 依 `screen_width/height` 居中（`ONScripter_rmenu.cpp:739-740`），并把 `dialog_info.pos` 作为脏矩形使用（`ONScripter_command.cpp:65`、`:87`）。
- 备注：
  - 右键会立即以“未选择”退出并写 0（右键在 `WAIT_BUTTON_MODE` 下置 `button = -1`，`ONScripter_event.cpp:403-407`；处理见 `ONScripter_command.cpp:72-75`）。
  - 点空白（`button == 0`，`ONScripter_event.cpp:417-421`）与滚轮（若产生 `-2`/`-3`，`ONScripter_event.cpp:480-491`）不在退出分支内，会继续等待（`ONScripter_command.cpp:72-80`）。
  - P1 必须是变量引用。若传字面量，`parseInt()` 会置 `VAR_INT | VAR_CONST` 而不写 `var_no`（`ScriptHandler.cpp:1541`），而 `setInt()` 只判断 `VAR_INT`（`ScriptHandler.cpp:762-770`），写回目标将取决于残留的 `var_no`——具体落点**待验证**，转写脚本时不要依赖。

## Z

### `zenkakko`
- 处理函数：`zenkakkoCommand` — `ScriptParser_command.cpp:40`
- 注册：`{"zenkakko", &ONScripter::zenkakkoCommand}`（`ONScripter_lut.cpp:28`）
- 语法：`zenkakko`
- 参数：无。
- 可选参数：无（无参数）。
- 语义：仅允许在 `define` 段使用，否则 `errorAndExit("zenkakko: not in the define section")`（`ScriptParser_command.cpp:42-43`）；随后置 `zenkakko_flag = true`（`ScriptParser_command.cpp:44`）。开启后，脚本文本里的全角括号 `【`/`】` 可作为标签定界符，与 `[`/`]` 等价：
  - 文本标签解析 `textCommand()`：开括号判定 `ONScripter_text.cpp:692-697`，闭括号判定 `ONScripter_text.cpp:704-710`；
  - `gettag` 前文标签解析：`ONScripter_command.cpp:2222-2227`、`:2245-2246`、`:2270-2274`。
- 坐标：否（纯开关标志）。
- 备注：
  - 默认 `false`（`ScriptParser.cpp:142`），无对应关闭命令。
  - 字符表 `Coding2UTF16::bracket` 在本快照中初始化为 UTF-8 的 `"【】"`（`coding2utf16.cpp:27`，实测字节 `E3 80 90 E3 80 91`），而比较按**两字节**取 `bracket[0]`/`bracket[1]`（开）与 `bracket[2]`/`bracket[3]`（闭）（`ONScripter_text.cpp:694-706`）。因此全角括号的实际匹配/闭合行为依赖脚本与构建的字符编码——**待验证**，转换器不应假定其必然生效。

---

## 本册统计

- 处理函数：6 个（`waveCommand`、`wavestopCommand`、`windowbackCommand`、`windowchipCommand`、`yesnoboxCommand`、`zenkakkoCommand`）
- 命令条目：8 条（`wave`、`waveloop`、`wavestop`、`windowback`、`windowchip`、`yesnobox`、`okcancelbox`、`zenkakko`，含 2 组别名/同函数注册）
- 其中含坐标参数：无（0 条；`windowchip` 只是精灵编号，`yesnobox`/`okcancelbox` 的对话框位置由引擎内部按 `screen_width/height` 计算）
- 未定位：0
