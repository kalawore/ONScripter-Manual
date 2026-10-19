# 附录 B：坐标命令全表（参数级 ×2 标注）

> 本表是阶段 B 转换器的**唯一白名单依据**。凡未出现在本表中的命令，一律不得修改任何数值。
>
> 判定基准：脚本坐标系 = 逻辑分辨率（`ScriptHandler::screen_width/height`，默认 640×480）；引擎内部 `screen_ratio1 = screen_ratio2 = 1`（`ONScripter.cpp:202-206`），因此脚本坐标 1:1 进入渲染，窗口缩放由视口完成。把逻辑分辨率与所有像素量同时 ×2，即可在保持版面比例的前提下提升坐标系精度。

## B.0 参数分类规则

| 类别 | 定义 | 处理 | 例 |
|---|---|---|---|
| **C1 屏幕绝对/相对坐标** | 直接写入 `orig_pos.x/y`、`pos.x/y`、`top_xy`、`image_rect.x/y` 的像素值 | **×2** | `locate x,y`、`lsp ... x,y` |
| **C2 屏幕像素尺寸** | 宽度、高度、最大宽度、缩进、震动幅度、截图尺寸 | **×2** | `bar ... w,h`、`indent n`、`quake amp` |
| **C3 字体度量** | 字号、字距附加值、行距附加值、ruby 字号 | **×2** | `setwindow` P5–P8 |
| **N1 计数/比例/时间** | 每行字符数、行数、持续时间、速度（ms）、缩放倍率、旋转角 | **不修改** | `setwindow` P3/P4、`quake` duration |
| **N2 颜色/透明度/编号/标志** | `#RRGGBB`、alpha、sprite no、0/1 开关 | **不修改** | `bar` P8、`amsp` trans |
| **N3 源图坐标** | 取自素材位图内部的 `src_x/src_y/sw/sh` | **人工复核** | `blt` P5–P8、`btn` P6/P7 |
| **N4 热区/光标点** | 光标热点、按钮命中区中与素材像素绑定的部分 | **人工复核** | `setcursor` P3/P4 |

### 复核判定说明

- **N3 源图坐标**：`blt` 的 `sx,sy,sw,sh` 与 `btn` 的 `src_x,src_y` 是「从 `btndef` 位图里裁剪」的坐标。如果转换只放大脚本坐标而复用**原始尺寸素材**，这些值必须保持不变；如果素材也被 ×2 重制，则它们也要 ×2。二者不可兼得，故标「人工复核」。
- **N4 热区**：`setcursor no,file,x,y` 的 x/y 是光标图像内的热点（`ONScripter_command.cpp:766-772` → `loadCursor`）。热点是否 ×2 取决于光标图是否重制。
- **`msp` 的位移**：`msp` 读的是 **delta**（`orig_pos.x +=`，`ONScripter_command.cpp:1355-1356`），仍是像素，×2；但若脚本用多帧 `msp` 做动画，逐帧位移 ×2 后总位移也 ×2，符合预期。
- **`quake` 的振幅**：P1 是像素振幅（源码用 `no*4` 与 duration 比较，`ONScripter_command.cpp:1158`），×2；duration 是毫秒，不变。

---

## B.1 坐标命令逐参数表

> 「P」为参数序号（从 1 开始，按源码 `readInt/readStr` 顺序）。`×2` 列：**是** / **否** / **复核**。

### B.1.1 精灵/图层定位

| 命令 | P | 参数 | 语义 | 类别 | ×2 | 证据 |
|---|---|---|---|---|---|---|
| `amsp` | 1 | no | 精灵号 | N2 | 否 | `ONScripter_command.cpp:119` |
| `amsp` | 2 | x | 目标 x | C1 | 是 | `:130` |
| `amsp` | 3 | y | 目标 y | C1 | 是 | `:131` |
| `amsp` | 4 | trans | 透明度 0–255 | N2 | 否 | `:144-148` |
| `amsp2` | 1 | no | 精灵号 | N2 | 否 | `:119` |
| `amsp2` | 2 | x | 目标 x | C1 | 是 | `:130` |
| `amsp2` | 3 | y | 目标 y | C1 | 是 | `:131` |
| `amsp2` | 4 | scale_x | 横向缩放倍率 | N1 | 否 | `:134` |
| `amsp2` | 5 | scale_y | 纵向缩放倍率 | N1 | 否 | `:135` |
| `amsp2` | 6 | rot | 旋转角（度） | N1 | 否 | `:136` |
| `amsp2` | 7 | trans | 透明度 | N2 | 否 | `:144-148` |
| `msp` | 1 | no | 精灵号 | N2 | 否 | `:1344` |
| `msp` | 2 | dx | x 位移增量 | C1 | 是 | `:1355` |
| `msp` | 3 | dy | y 位移增量 | C1 | 是 | `:1356` |
| `msp` | 4 | trans | 透明度增量 | N2 | 否 | `:1369-1375` |
| `msp2` | 1 | no | 精灵号 | N2 | 否 | `:1344` |
| `msp2` | 2 | dx | x 位移增量 | C1 | 是 | `:1355` |
| `msp2` | 3 | dy | y 位移增量 | C1 | 是 | `:1356` |
| `msp2` | 4 | dscale_x | 缩放增量 | N1 | 否 | `:1359` |
| `msp2` | 5 | dscale_y | 缩放增量 | N1 | 否 | `:1360` |
| `msp2` | 6 | drot | 旋转增量 | N1 | 否 | `:1361` |
| `msp2` | 7 | trans | 透明度增量 | N2 | 否 | `:1369-1375` |
| `lsp` / `lsph` | 1 | no | 精灵号 | N2 | 否 | `:1706` |
| `lsp` / `lsph` | 2 | file | 图像名 | N2 | 否 | `:1713` |
| `lsp` / `lsph` | 3 | x | x | C1 | 是 | `:1715` |
| `lsp` / `lsph` | 4 | y | y | C1 | 是 | `:1716` |
| `lsp` / `lsph` | 5 | trans | 透明度（可选） | N2 | 否 | `:1719-1722` |
| `lsp2` / `lsp2add` / `lsp2sub` / `lsph2` / `lsph2add` / `lsph2sub` | 1 | no | 精灵号 | N2 | 否 | `:1666` |
| `lsp2` / `lsp2add` / `lsp2sub` / `lsph2` / `lsph2add` / `lsph2sub` | 2 | file | 图像名 | N2 | 否 | `:1674` |
| `lsp2` / `lsp2add` / `lsp2sub` / `lsph2` / `lsph2add` / `lsph2sub` | 3 | x | x | C1 | 是 | `:1677` |
| `lsp2` / `lsp2add` / `lsp2sub` / `lsph2` / `lsph2add` / `lsph2sub` | 4 | y | y | C1 | 是 | `:1678` |
| `lsp2` / `lsp2add` / `lsp2sub` / `lsph2` / `lsph2add` / `lsph2sub` | 5 | scale_x | 缩放倍率 | N1 | 否 | `:1680` |
| `lsp2` / `lsp2add` / `lsp2sub` / `lsph2` / `lsph2add` / `lsph2sub` | 6 | scale_y | 缩放倍率 | N1 | 否 | `:1681` |
| `lsp2` / `lsp2add` / `lsp2sub` / `lsph2` / `lsph2add` / `lsph2sub` | 7 | rot | 旋转角 | N1 | 否 | `:1682` |
| `lsp2` / `lsp2add` / `lsp2sub` / `lsph2` / `lsph2add` / `lsph2sub` | 8 | trans | 透明度 | N2 | 否 | `:1685` |
| `strsp` / `strsph` | 1 | no | 精灵号 | N2 | 否 | `:373` |
| `strsp` / `strsph` | 2 | text | 文本 | N2 | 否 | `:381` |
| `strsp` / `strsph` | 3 | x | x | C1 | 是 | `:382` |
| `strsp` / `strsph` | 4 | y | y | C1 | 是 | `:383` |
| `strsp` / `strsph` | 5 | num_x | 每行字符数 | N1 | 否 | `:388` |
| `strsp` / `strsph` | 6 | num_y | 行数 | N1 | 否 | `:389` |
| `strsp` / `strsph` | 7 | font_x | 字宽 | C3 | 是 | `:390` |
| `strsp` / `strsph` | 8 | font_y | 字高 | C3 | 是 | `:391` |
| `strsp` / `strsph` | 9 | pitch_x | 字距附加值 | C3 | 是 | `:392` |
| `strsp` / `strsph` | 10 | pitch_y | 行距附加值 | C3 | 是 | `:393` |
| `strsp` / `strsph` | 11 | bold | 粗体 | N2 | 否 | `:394` |
| `strsp` / `strsph` | 12 | shadow | 阴影 | N2 | 否 | `:395` |
| `strsp` / `strsph` | 13+ | colors | `#RRGGBB` 列表 | N2 | 否 | `:398-411` |
| `logsp` / `logsp2` | 1 | no | 精灵号 | N2 | 否 | `:1797` |
| `logsp` / `logsp2` | 2 | text | 文本 | N2 | 否 | `:1803` |
| `logsp` / `logsp2` | 3 | x | x | C1 | 是 | `:1805` |
| `logsp` / `logsp2` | 4 | y | y | C1 | 是 | `:1806` |
| `logsp` / `logsp2` | 5+ | colors | 颜色列表 | N2 | 否 | `:1831-1841` |
| `logsp2` | 5 | font_x | 字宽 | C3 | 是 | `:1811` |
| `logsp2` | 6 | font_y | 字高 | C3 | 是 | `:1812` |
| `logsp2` | 7 | pitch_x | 字距附加值 | C3 | 是 | `:1813` |
| `logsp2` | 8 | pitch_y | 行距附加值 | C3 | 是 | `:1814` |
| `logsp2` | 9+ | colors | 颜色列表 | N2 | 否 | `:1839-1840` |

### B.1.2 背景/绘制

| 命令 | P | 参数 | 语义 | 类别 | ×2 | 证据 |
|---|---|---|---|---|---|---|
| `drawbg2` | 1 | x | x | C1 | 是 | `ONScripter_command.cpp:3066` |
| `drawbg2` | 2 | y | y | C1 | 是 | `:3067` |
| `drawbg2` | 3 | scale_x | 缩放倍率 | N1 | 否 | `:3069` |
| `drawbg2` | 4 | scale_y | 缩放倍率 | N1 | 否 | `:3070` |
| `drawbg2` | 5 | rot | 旋转 | N1 | 否 | `:3071` |
| `drawsp` | 1 | no | 精灵号 | N2 | 否 | `:3015` |
| `drawsp` | 2 | cell | 帧号 | N2 | 否 | `:3016` |
| `drawsp` | 3 | alpha | 透明度 | N2 | 否 | `:3017` |
| `drawsp` | 4 | x | x | C1 | 是 | `:3018` |
| `drawsp` | 5 | y | y | C1 | 是 | `:3019` |
| `drawsp2` | 1 | no | 精灵号 | N2 | 否 | `:2994` |
| `drawsp2` | 2 | cell | 帧号 | N2 | 否 | `:2995` |
| `drawsp2` | 3 | alpha | 透明度 | N2 | 否 | `:2996` |
| `drawsp2` | 4 | x | x | C1 | 是 | `:2999` |
| `drawsp2` | 5 | y | y | C1 | 是 | `:3000` |
| `drawsp2` | 6 | scale_x | 缩放倍率 | N1 | 否 | `:3002` |
| `drawsp2` | 7 | scale_y | 缩放倍率 | N1 | 否 | `:3003` |
| `drawsp2` | 8 | rot | 旋转 | N1 | 否 | `:3004` |
| `drawsp3` | 1 | no | 精灵号 | N2 | 否 | `ONScripter_command.cpp:2963` |
| `drawsp3` | 2 | cell | 帧号 | N2 | 否 | `:2964` |
| `drawsp3` | 3 | alpha | 透明度 | N2 | 否 | `:2965` |
| `drawsp3` | 4 | x | x | C1 | 是 | `:2966` |
| `drawsp3` | 5 | y | y | C1 | 是 | `:2967` |
| `drawsp3` | 6–9 | mat00,mat01,mat10,mat11 | 2×2 变换矩阵系数（千分比） | N1 | 否 | `:2973-2976` |

### B.1.3 裁剪/按钮/进度条

| 命令 | P | 参数 | 语义 | 类别 | ×2 | 证据 |
|---|---|---|---|---|---|---|
| `blt` | 1 | dx | 目标 x | C1 | 是 | `ONScripter_command.cpp:349` |
| `blt` | 2 | dy | 目标 y | C1 | 是 | `:350` |
| `blt` | 3 | dw | 目标宽 | C2 | 是 | `:351` |
| `blt` | 4 | dh | 目标高 | C2 | 是 | `:352` |
| `blt` | 5 | sx | 源 x | N3 | **复核** | `:353`、`:361-367` |
| `blt` | 6 | sy | 源 y | N3 | **复核** | `:354` |
| `blt` | 7 | sw | 源宽 | N3 | **复核** | `:355` |
| `blt` | 8 | sh | 源高 | N3 | **复核** | `:356` |
| `btn` | 1 | no | 按钮号 | N2 | 否 | `:473` |
| `btn` | 2 | x | 命中区 x | C1 | 是 | `:474` |
| `btn` | 3 | y | 命中区 y | C1 | 是 | `:475` |
| `btn` | 4 | w | 命中区宽 / 源图裁剪宽 | N3 | **复核** | `:476`、`:490`（`src_rect.w = image_rect.w`） |
| `btn` | 5 | h | 命中区高 / 源图裁剪高 | N3 | **复核** | `:477`、`:491` |
| `btn` | 6 | src_x | 源图 x | N3 | **复核** | `:480` |
| `btn` | 7 | src_y | 源图 y | N3 | **复核** | `:481` |
| `cselbtn` | 1 | csel_no | 选项号 | N2 | 否 | `ONScripter_command.cpp:3202` |
| `cselbtn` | 2 | button_no | 按钮号 | N2 | 否 | `:3203` |
| `cselbtn` | 3 | x | 文本区 x | C1 | 是 | `:3207` |
| `cselbtn` | 4 | y | 文本区 y | C1 | 是 | `:3208` |
| `bar` | 1 | no | 进度条号 | N2 | 否 | `ONScripter_command.cpp:3818` |
| `bar` | 2 | param | 当前值 | N1 | 否 | `:3833` |
| `bar` | 3 | x | 左上 x | C1 | 是 | `:3834` |
| `bar` | 4 | y | 左上 y | C1 | 是 | `:3835` |
| `bar` | 5 | max_width | 最大宽度 | C2 | 是 | `:3836` |
| `bar` | 6 | height | 高度 | C2 | 是 | `:3838` |
| `bar` | 7 | max_param | 最大值 | N1 | 否 | `:3839` |
| `bar` | 8 | color | 颜色 | N2 | 否 | `:3843-3844` |
| `getscreenshot` | 1 | w | 截图宽 | C2 | 是 | `ONScripter_command.cpp:2335` |
| `getscreenshot` | 2 | h | 截图高 | C2 | 是 | `:2337` |

> `bar` 的参数读取位于 `ONScripter_command.cpp:3833-3844`（函数体自 `:3816` 起）。

### B.1.4 文本窗口/光标/文本放置

| 命令 | P | 参数 | 语义 | 类别 | ×2 | 证据 |
|---|---|---|---|---|---|---|
| `setwindow` | 1 | x | 窗口左上 x | C1 | 是 | `ONScripter_command.cpp:664` |
| `setwindow` | 2 | y | 窗口左上 y | C1 | 是 | `:665` |
| `setwindow` | 3 | num_x | 每行字符数 | N1 | 否 | `:666` |
| `setwindow` | 4 | num_y | 行数 | N1 | 否 | `:667` |
| `setwindow` | 5 | font_x | 字宽 | C3 | 是 | `:668` |
| `setwindow` | 6 | font_y | 字高 | C3 | 是 | `:669` |
| `setwindow` | 7 | pitch_x | 字距附加值 | C3 | 是 | `:670` |
| `setwindow` | 8 | pitch_y | 行距附加值 | C3 | 是 | `:671` |
| `setwindow` | 9 | wait | 每字延迟 ms | N1 | 否 | `:672` |
| `setwindow` | 10 | bold | 粗体 | N2 | 否 | `:673` |
| `setwindow` | 11 | shadow | 阴影 | N2 | 否 | `:674` |
| `setwindow` | 12 | style | `#RRGGBB` 或窗口图 | N2 | 否 | `:676-682` |
| `setwindow` | 13–16 | x1,y1,x2,y2 | 透明窗口矩形（仅 `#` 分支） | C1 | 是 | `:685-688` |
| `setwindow` | 13–14 | wx,wy | 窗口图左上（图片分支） | C1 | 是 | `:696-697` |
| `setwindow2` | 1 | style | 窗口图/颜色 | N2 | 否 | `:724-735` |
| `setwindow3` | — | — | 与 `setwindow` 完全相同参数 | — | 同上 | `:708-710` |
| `locate` | 1 | x | 文本光标 x（**全角字符数**） | N1 | 否 | `:1858`；`FontInfo::setXY` 将 x 乘 2 存入半角单位 `FontInfo.cpp:199-203`，故单位是字符而非像素 |
| `locate` | 2 | y | 文本光标 y（**行数**） | N1 | 否 | `:1859`；同上 |
| `indent` | 1 | n | 缩进**全角字符数** | N1 | 否 | `:2102`；每次循环 `advanceCharInHankaku(2)` 推进一个全角字（`ONScripter_text.cpp:206-211`、`FontInfo.cpp:247-250`） |
| `menusetwindow` | 1 | font_x | 菜单字宽 | C3 | 是 | `ScriptParser_command.cpp:694` |
| `menusetwindow` | 2 | font_y | 菜单字高 | C3 | 是 | `:695` |
| `menusetwindow` | 3 | pitch_x | 字距附加值 | C3 | 是 | `:696` |
| `menusetwindow` | 4 | pitch_y | 行距附加值 | C3 | 是 | `:697` |
| `menusetwindow` | 5 | bold | 粗体 | N2 | 否 | `:698` |
| `menusetwindow` | 6 | shadow | 阴影 | N2 | 否 | `:699` |
| `menusetwindow` | 7 | color/file | 颜色或图片 | N2 | 否 | `:701` |
| `rubyon` | 1 | font_x | ruby 字宽 | C3 | 是 | `ScriptParser_command.cpp:394` |
| `rubyon` | 2 | font_y | ruby 字高 | C3 | 是 | `:395` |
| `rubyon` | 3 | font | 字体名 | N2 | 否 | `:398` |
| `setcursor` / `abssetcursor` | 1 | no | 光标号 | N2 | 否 | `ONScripter_command.cpp:766` |
| `setcursor` / `abssetcursor` | 2 | file | 光标图 | N2 | 否 | `:767` |
| `setcursor` / `abssetcursor` | 3 | x | 热点 x | N4 | **复核** | `:769` |
| `setcursor` / `abssetcursor` | 4 | y | 热点 y | N4 | **复核** | `:770` |
| `movemousecursor` | 1 | x | 鼠标 x | C1 | 是 | `:1585` |
| `movemousecursor` | 2 | y | 鼠标 y | C1 | 是 | `:1586` |
| `prnum` | 1 | no | 编号 | N2 | 否 | `:1203` |
| `prnum` | 2 | param | 数值 | N1 | 否 | `:1224` |
| `prnum` | 3 | x | x | C1 | 是 | `:1225` |
| `prnum` | 4 | y | y | C1 | 是 | `:1226` |
| `prnum` | 5 | font_x | 字宽 | C3 | 是 | `:1228` |
| `prnum` | 6 | font_y | 字高 | C3 | 是 | `:1229` |
| `prnum` | 7 | color | 颜色 | N2 | 否 | `:1233-1234` |

### B.1.5 特效/其它

| 命令 | P | 参数 | 语义 | 类别 | ×2 | 证据 |
|---|---|---|---|---|---|---|
| `quake` / `quakex` / `quakey` | 1 | amplitude | 震动幅度单位；实际像素 = `12*no`（x/y 单轴，`ONScripter_effect.cpp:30`、`:339-340`）或 `2*no`（双轴，`:377-378`） | C2 | 是 | `ONScripter_command.cpp:1156`、`:1158` |
| `quake` / `quakex` / `quakey` | 2 | duration | 持续时间 ms | N1 | 否 | `:1157` |

### B.1.6 其它像素度量（易漏）

| 命令 | P | 参数 | 语义 | 类别 | ×2 | 证据 |
|---|---|---|---|---|---|---|
| `shadedistance` | 1 | dx | 文字阴影水平偏移（像素） | C2 | 是 | `ScriptParser_command.cpp:240` |
| `shadedistance` | 2 | dy | 文字阴影垂直偏移（像素） | C2 | 是 | `:242` |
| `underline` | 1 | y | 立绘基线/下划线 y（像素） | C1 | 是 | `ScriptParser_command.cpp:104`；用途见 `ONScripter_command.cpp:1930` |

> 注：`rubyon` 的字号在源码中未做 `screen_ratio` 换算（`ScriptParser_command.cpp:394-395`），但按 C3 规则应随字号体系一起 ×2；分册 05 将其列为「待验证」。转换器应 ×2 并在报告中标注该项。
>
> 注：分册 06 把 `shadedistance` 判为「非坐标」（理由：它按 `screen_ratio` 缩放且不写入 `orig_pos` 字段）。本表按 C2 规则把它计入白名单——它是**像素偏移量**，逻辑分辨率 ×2 后阴影偏移若不 ×2 会显得过窄。二者不矛盾，属于分类口径差异；阶段 B 以本表为准。

---

## B.2 明确「不修改」的命令类别（防止误伤）

以下类别即使内部出现像素运算，也**没有脚本可写的坐标参数**，转换器不得改动：

| 类别 | 命令（示例） | 原因 |
|---|---|---|
| 纯开关/标志 | `windowback`、`windowchip`、`zenkakko`、`bdown`、`btndown`、`autosaveoff`、`globalon` | 无坐标参数 |
| 颜色/透明度 | `textcolor`、`lookbackcolor`、`selectcolor`、`menuselectcolor`、`sp_rgb_gradation`、`monocro`、`nega` | 颜色不是坐标 |
| 音频 | `wave`、`waveloop`、`mp3`、`sevol`、`voicevol`、`chvol` | 无坐标 |
| 计数/时间 | `automode_time`、`autoclick`、`waittimer`、`delay`、`btntime` | 时间不是像素 |
| 文件/流程 | `bg`、`ld`、`csp`、`csp2`、`bsp`、`spbtn`、`exbtn`、`arc`、`nsa`、`goto`、`gosub`、`if`、`for` | 文件名/编号/流程 |
| 只读取坐标 | `getsppos`、`getspsize`、`getmousepos`、`getcursorpos`、`getcursorpos2` | 输出坐标，非输入；其返回值自动落在新坐标系 |

> **只读取坐标的陷阱**：`getsppos`/`getmousepos` 等返回的数值会自动变成「×2 后的坐标」。若脚本把它与**字面量**比较（如 `if %0=320`），该字面量属于「隐含坐标常量」，本表无法从命令参数层面发现，必须在转换报告中列为**待人工复核**（阶段 B 报告项）。

---

## B.3 隐藏坐标点（不在命令参数中）

| 位置 | 形态 | 处理 | 证据 |
|---|---|---|---|
| 分辨率声明 | `;mode800` / `;$S640,480` | 改为 `;$S<2w>,<2h>`（`;mode` 无法表达 640×480/1280×960） | `ScriptHandler.cpp:1199-1250` |
| 字符串精灵标签 | `:s/<font_x>,<font_y>[,<pitch>]` | 字号与字距 ×2 | `ONScripter_animation.cpp:302-311` |
| 窗口图片名标签 | 同 `:s` 规则 | 同上 | 同上 |
| 数字别名 | `numalias` 定义的常量若被用作坐标 | 无法静态判定 → 报告 | `ScriptParser_command.cpp`（`numaliasCommand`） |
| 变量初值 | `mov %x,320` 之类 | 无法静态判定 → 报告 | — |

> 阶段 B 转换器至少应处理「分辨率声明」与「`:s/` 标签」两类；其余列为报告项。

---

## B.4 统计

| 指标 | 数值 |
|---|---|
| 含坐标输入参数的命名字段（命令组） | 22 组（`amsp/amsp2`、`msp/msp2`、`lsp/lsp2/lsph*`、`strsp/strsph`、`logsp/logsp2`、`drawbg2`、`drawsp/drawsp2/drawsp3`、`blt`、`btn`、`cselbtn`、`bar`、`getscreenshot`、`setwindow/setwindow3`、`menusetwindow`、`rubyon`、`setcursor/abssetcursor`、`movemousecursor`、`prnum`、`quake/quakex/quakey`、`shadedistance`、`underline`） |
| 字符单位、**不 ×2** 的易混命令 | `locate`（x=全角字数，y=行数）、`indent`（全角字数） |
| 其中需要 ×2 的参数条目 | 见 B.1 各表「是」行 |
| 白名单命令总数（含别名） | **38 条**（附录 A 中标 ● 者） |
| 需要人工复核的参数条目 | `blt` P5–P8、`btn` P4–P7、`setcursor` P3–P4（共 10 条） |
| 仅读取坐标、不得修改的命令 | `getsppos`、`getspsize`、`getmousepos`、`getcursorpos`、`getcursorpos2` |

> 本表与 `docs/commands/part-0X-*.md` 的「坐标」标注互为校验；若二者冲突，以源码为准并在附录 D 记录差异。
