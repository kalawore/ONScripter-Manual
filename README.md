# ONScripter 源码说明书（OnscripterYuri 0.7.7beta）

> 本文档面向**没有读过源码**的读者，目标是：读完即可理解 ONScripter（本仓库分支）的运行方式、脚本命令全集、坐标语义与文件格式，并据第 6.5 / 7.3 节实现脚本坐标转换工具。
>
> 证据约定：所有结论后附 `文件:行号`。行号以本仓库快照 `C:\Users\kalaw\Desktop\OnscripterYuri-0.7.7beta` 为准。凡未能在源码中直接证实的，一律标注 **（待验证）**，不做推测性断言。

---

## 1. 项目概览

| 项目 | 值 | 证据 |
|---|---|---|
| 仓库目录名 | `OnscripterYuri-0.7.7beta` | 工作目录 |
| 上游项目 | `YuriSizuku/OnscripterYuri`（README 标题 `Onscripter-Yuri`） | `README.md:1` |
| 本快照版本号 | `0.7.7` | `src/onsyuri/version.h:1`（`ONS_YURI_VERSION "0.7.7"`） |
| 目录名中的 `beta` 后缀 | 未写入 `version.h`，仅存在于目录名 | 对比 `version.h:1` 与目录名 |
| 基线引擎 | ONScripter-Jh `0.8.0` | `src/onsyuri/version.h:2`（`ONS_JH_VERSION "0.8.0"`） |
| 原始 ONScripter 版本标记 | `20181218` | `src/onsyuri/version.h:3`（`ONS_VERSION "20181218"`） |
| NScripter 脚本版本常量 | `NSC_VERSION 296` | `src/onsyuri/version.h:4` |
| Git 提交 | **未知**：快照不含 `.git`，`git rev-parse` 报 `not a git repository` | 本机实测 |
| 许可证 | GNU GPL v2 | `LICENSE:1-2`；源文件头 `src/onsyuri/ONScripter_lut.cpp:10-13` |
| 语言标准 | C++11 | `CMakeLists.txt:483`（`set(CMAKE_CXX_STANDARD 11)`） |

### 1.1 分支定位结论

- 这是 **OnscripterYuri**（YuriSizuku 维护的多平台增强分支），不是原版 ONScripter，也不是 ONScripter-EN / Ponscripter。
- 其直接上游是 **ONScripter-Jh**（README 明言 "base on ONScripter-Jh by SDL2"），因此命令集 = 原版 ONScripter + Jh 扩展 + Yuri 扩展（Lua、utf8、nt2/nt3）。
- 与用户所列候选分支的差异（依据源码特征）：
  - 支持 `--enc:utf8`（`src/onsyuri/onscripter_main.cpp:389`），原版仅 SJIS/GBK。
  - 内建 `onscript.nt2` / `onscript.nt3` 解密（`src/onsyuri/ScriptHandler.cpp:1014-1019`、`1125-1136`），这是 README 所述 "Mine exclusive format"。
  - 内建 Lua（`src/onsyuri/LUAHandler.cpp`，`CMakeLists.txt:521` 定义 `USE_LUA`）。

---

## 2. 目录与模块划分

```
OnscripterYuri-0.7.7beta/
├─ CMakeLists.txt          # 唯一主构建脚本，按平台分发到 config_* 函数
├─ vcpkg.json              # Windows/MSVC 依赖清单（SDL2 系 + lua + bzip2）
├─ docker/                 # 交叉编译镜像与脚本（android/emcc/linuxarm64/linuxx64/mingw64）
├─ .github/workflows/      # CI：android/darwin/linux/web/win/win_msvc
├─ asset/  screenshot/  script/   # 图标、截图、示例脚本
└─ src/
   ├─ onsyuri/             # ★ 引擎核心（92 个文件）
   ├─ onsyuri_android/     # Android 壳（Gradle + JNI）
   ├─ onsyuri_libretro/    # RetroArch 核心（含 SDL 等 submodule 声明）
   └─ onsyuri_web/         # Emscripten 外壳（HTML/JS/Python 服务脚本）
```

### 2.1 核心源文件清单（`src/onsyuri/`）

按职责分组（行数为快照实测）：

| 职责 | 文件 | 关键内容 |
|---|---|---|
| 主类声明/实现 | `ONScripter.h`（769 行）、`ONScripter.cpp`（1384 行） | `class ONScripter : public ScriptParser`（`ONScripter.h:75`）；初始化、主循环、渲染、鼠标 |
| 命令查找表 | `ONScripter_lut.cpp`（387 行） | `func_lut[]`（`:27`）与 `makeFuncLUT()`（`:405`） |
| 命令实现（大头） | `ONScripter_command.cpp`（3574 行） | 180+ 个 `ONScripter::*Command()` |
| 命令实现（解析器层） | `ScriptParser_command.cpp`（1256 行） | 100+ 个 `ScriptParser::*Command()`（流程/变量/系统类） |
| 脚本执行器 | `ScriptParser.h`（478 行）、`ScriptParser.cpp`（756 行） | 标签执行、变量、存档、特效链、用户函数 |
| 脚本原始文本/分词 | `ScriptHandler.h`（324 行）、`ScriptHandler.cpp`（1549 行） | 文件加载、解密、`readToken/readInt/readStr`、标签索引、编码转换 |
| 图像/精灵 | `AnimationInfo.h/.cpp`（147/1157 行）、`ONScripter_image.cpp`（806 行） | 图层、混合、转场、裁剪 |
| 文本渲染 | `FontInfo.h/.cpp`（2722 字节头/304 行）、`ONScripter_text.cpp`（984 行） | TTF、排版、ruby、clickstr |
| 音频 | `ONScripter_sound.cpp`（444 行） | BGM/SE/voice、mp3/ogg、fade |
| 事件/输入 | `ONScripter_event.cpp`（1296 行） | 事件循环、按钮、滚轮、触摸、手柄 |
| 存档/读档 | `ONScripter_file.cpp`（264 行）、`ONScripter_file2.cpp`（651 行） | 截图、save/load、全局变量 |
| 右键菜单 | `ONScripter_rmenu.cpp`（715 行） | rmenu 构建与交互 |
| 归档读取 | `BaseReader.h`、`DirectReader.cpp`（584 行）、`SarReader.cpp`（335 行）、`NsaReader.cpp`（188 行） | 目录直读、SAR/NSA 归档、压缩类型 |
| 动画 | `ONScripter_animation.cpp`（454 行）、`AnimationInfo.cpp` | 动画帧、Lua 动画 |
| 特效 | `ONScripter_effect.cpp`（481 行）、`ONScripter_effect_breakup.cpp`（309 行）、`builtin_dll/*` | 转场、breakup、插件特效 |
| 编码 | `coding2utf16.cpp`、`sjis2utf16.cpp`（11469 行表）、`gbk2utf16.cpp`（24011 行表） | SJIS/GBK ⇄ UTF-16 |
| Lua | `LUAHandler.h/.cpp`（1037 行） | Lua 绑定与 NS* API |
| 平台入口 | `onscripter_main.cpp`（526 行） | `int main()`（`:480`）、命令行解析 |

> 统计口径：`src/onsyuri` 下 43 个 `.cpp`、42 个 `.h`、11 个 `.inl`（含 `renderer/`、`builtin_dll/`、`simd/`、`tool/` 子目录）。

---

## 3. 构建系统与依赖

### 3.1 构建入口

- **没有** `Makefile.Linux`，也**没有** Visual Studio `.sln/.vcxproj`。唯一主构建系统是 **CMake**：`CMakeLists.txt:1-2`（`cmake_minimum_required(VERSION 3.16.3)`、`project(onsyuri)`）。
- 平台分发：`config_platform()` 按 `CMAKE_SYSTEM_NAME` 调用 `config_win/config_linux/config_web/config_android/config_darwin`（`CMakeLists.txt:461-480`）。
- Android 另有 Gradle 工程：`src/onsyuri_android/build.gradle`、`app/build.gradle`、`app/cpp/CMakeLists.txt`。
- Libretro 核心另有 `src/onsyuri_libretro/CMakeLists.txt`。
- 交叉编译通过 `docker/Dockerfile_*` + `docker/docker_*.sh`；CI 见 `.github/workflows/build_*.yml`。

### 3.2 源文件集合与编译宏

- `ONSYURI_CODE` 明确列出 34 个翻译单元（`CMakeLists.txt:484-519`）。
- 全局宏：`USE_LUA`、`USE_BUILTIN_LAYER_EFFECTS`、`ENABLE_1BYTE_CHAR`（`CMakeLists.txt:520-524`）。
- Linux 额外宏：`LINUX`、`USE_PARALLEL`、`USE_GLES`、`USE_FILELOG`（`CMakeLists.txt:8-14`）。

### 3.3 外部依赖（vcpkg，MSVC 路径）

`vcpkg.json:4-38`：SDL2 ≥ 2.26.3、SDL2_image（libjpeg-turbo、libwebp）、SDL2_mixer（libvorbis）、SDL2_ttf（harfbuzz）、bzip2、libjpeg-turbo、lua 5.4.4、libogg。

Linux 路径使用 `pkg_check_modules`（如 `CMakeLists.txt:108` 的 `LUA`），静态链接时可指定 `STATIC_PORT_ROOT`（`CMakeLists.txt:20-30`）。

### 3.4 平台差异（源码级）

| 平台 | 差异点 | 证据 |
|---|---|---|
| Linux | `USE_GLES` + `USE_PARALLEL` + `USE_FILELOG` | `CMakeLists.txt:8-14` |
| Windows | `WIN32` 可执行、`dinput8/shell32/...` 链接 | `CMakeLists.txt:128-129`、`201-203` |
| Web | Emscripten 构建，`EM_ASM` 上报初始化 | `CMakeLists.txt:461-473`、`onscripter_main.cpp:575-579` |
| Android | `ANDROID` 分支、JNI 导出宽高 | `onscripter_main.cpp:144-152`、`492-493` |
| iOS/macOS | `IOS` 分支设置缓存/文档目录 | `onscripter_main.cpp:496-512` |

---

## 4. 核心类与对象模型

### 4.1 继承主线

```
ScriptHandler        原始脚本文本、分词、标签、编码、解密
     ▲
     │ 组合（成员 script_h）
ScriptParser         执行状态机：标签/行号、变量、页面、特效、存读档
     ▲
     │ 继承
ONScripter           平台/渲染/输入/音频/精灵/命令实现
```

- `class ScriptParser` 定义于 `ScriptParser.h:66`；`class ONScripter : public ScriptParser` 定义于 `ONScripter.h:75`。
- `ScriptHandler script_h;` 是 `ScriptParser` 的成员（声明见 `ScriptParser.h` 的 protected 区；`ONScripter.cpp:1116` 等处以 `script_h.` 调用可证）。

### 4.2 类职责表

| 类 | 文件:行 | 职责 | 关键成员/方法 |
|---|---|---|---|
| `ScriptHandler` | `ScriptHandler.h:45` | 把脚本文件读成一块连续 `script_buffer`；提供 `readToken/readInt/readStr/readLabel`；标签索引；变量存储；编码转换 | `openScript()`（`.cpp:864`）、`readScript()`（`:991`）、`readScriptSub()`（`:1082`）、`readConfiguration()`（`:1171`） |
| `ScriptParser` | `ScriptParser.h:66` | 执行一条条命令；维护 `current_label_info`、`current_line`；`gosub/goto/for/if`；变量别名；页面 `Page`；特效链 `EffectLink`；存读档 | `executeLabel()` 的实现实际在 `ONScripter.cpp:1052`；`parseLine` 在 `ONScripter.cpp:1112` |
| `ONScripter` | `ONScripter.h:75` | 主控：SDL 窗口/渲染器、音频、事件循环、精灵数组、命令分派 | `init()`（`.cpp:536`）、`openScript()`（`:518`）、`runScript()`（`:1106`）、`parseLine()`（`:1112`） |
| `AnimationInfo` | `AnimationInfo.h:36` | 一张图/一个精灵/一个图层：表面、位置、透明、动画帧、转场 | `scalePosXY/WH`（`AnimationInfo.h:119-126`） |
| `FontInfo` | `FontInfo.h:38` | TTF 字体、字号、行距、字距、竖排、ruby | `openFont()`（`ONScripter.cpp:680` 调用） |
| `BaseReader` | `BaseReader.h:62` | 归档读取抽象基类 | 子类 `DirectReader/SarReader/NsaReader` |
| `DirectReader` | `DirectReader.h:33` | 直接从目录读文件（含大小写不敏感查找） | `getFileByIndex()`（`.cpp:291`） |
| `SarReader` | `SarReader.h:29` | 读 `.sar` 归档（NScripter 标准归档） | 继承 `DirectReader` |
| `NsaReader` | `NsaReader.h:31` | 读 `.nsa/.ns2/.ns3` 归档 | 继承 `SarReader` |
| `ButtonLink` | `ButtonLink.h:29` | 按钮/可点击文本的命中矩形与状态 | `image_rect/select_rect` |
| `DirtyRect` | `DirtyRect.h:33` | 脏矩形合并，决定重绘范围 | `add()/fill()` |
| `LUAHandler` | `LUAHandler.h:32` | 内嵌 Lua 状态机与 `NS*` API | `init()`（`.cpp:1119`）、`callFunction()` |
| `Coding2UTF16` 族 | `coding2utf16.h:27`、`sjis2utf16.h:27`、`gbk2utf16.h:27` | 脚本字节 → UTF-16 内部表示 | `force_utf8` 标志 |
| `AVIWrapper` | `AVIWrapper.h:33` | 视频播放封装 | — |
| `ThreadPool` | `Parallel.h:55` | `USE_PARALLEL` 下的并行任务 | — |

### 4.3 命令分派的对象关系

- `ONScripter` 内定义函数指针类型 `typedef int (ONScripter::*FuncList)();`（`ONScripter.h:127`）。
- 查找表元素 `struct FuncLUT{ char command[30]; FuncList method; }`（`ONScripter.h:128-131`）。
- 一级索引 `FuncHash func_hash['z'-'a'+1]`（`ONScripter.h:132-135`），按命令首字母分组，组内是 `func_lut` 的连续区间。
- `makeFuncLUT()` 遍历 `func_lut`，把同首字母的条目归入同一 `func_hash` 槽，并记录区间长度 `num`（`ONScripter_lut.cpp:405-417`）。

> 注意：`func_lut` 中命令按**字母降序**排列（`ONScripter_lut.cpp:28` 起 `zenkakko`、`yesnobox`…，末尾 `abssetcursor`），因此每个首字母组在数组中连续，`func_hash` 可用「起点 + 长度」线性查找。

### 4.4 头文件依赖边

```
ONScripter.h ──► ScriptParser.h ──► ScriptHandler.h ──► BaseReader.h
      │                 │                 │
      │                 ├─► NsaReader.h / DirectReader.h
      │                 ├─► AnimationInfo.h
      │                 ├─► FontInfo.h
      │                 ├─► LUAHandler.h
      │                 ├─► coding2utf16.h
      │                 └─► builtin_layer.h
      ├─► DirtyRect.h
      ├─► ButtonLink.h
      └─► renderer/gles_renderer.h
```

证据：`ONScripter.h:29-46`、`ScriptParser.h:34-44`、`ScriptHandler.h:32`、`AnimationInfo.h:28`。

- 组合关系：`ScriptParser` 持有 `ScriptHandler script_h`（`ScriptParser.h:34` 引入类型；`ONScripter.cpp:1116` 等处 `script_h.` 调用）。
- `ONScripter` 持有 `sprite_info[]`、`sprite2_info[]`、`tachi_info[3]`、`bar_info[]`、`bg_info`、`sentence_font` 等（类型均来自 `AnimationInfo.h` / `FontInfo.h`）。
- `ScriptHandler` 持有 `BaseReader *cBR`；初始化时按是否存在 `nsa` 归档选择 `NsaReader` 或 `DirectReader`（`ScriptParser.cpp:264-267`），运行中 `arc` 可切换为 `SarReader`（`ScriptParser_command.cpp:1463`）。

---

## 5. 启动流程与主循环

### 5.1 进程入口

`int main(int argc, char *argv[])` 位于 `src/onsyuri/onscripter_main.cpp:480`。核心顺序（`onscripter_main.cpp:569-581`）：

```cpp
if (coding2utf16 == NULL) coding2utf16 = new GBK2UTF16();  // :569 默认编码器
if (ons.openScript()) exit(-1);                            // :573
if (ons.init()) exit(-1);                                  // :574
ons.executeLabel();                                        // :580
exit(0);                                                   // :581
```

- 默认编码器是 **GBK2UTF16**（`onscripter_main.cpp:569`）；`--enc:sjis|gbk|utf8` 会替换它（`:383-389`）。这意味着**不传 `--enc` 时按 GBK 解释脚本**，这是一个容易踩坑的默认值。
- `ONScripter::openScript()`（`ONScripter.cpp:518-534`）先处理 `--key-exe` 的密钥表，再调用 `ScriptParser::openScript()`。

### 5.2 初始化

`ONScripter::init()`（`ONScripter.cpp:536`）依次：

1. `initSDL()`（`:538`）：创建窗口/渲染器、计算 `screen_device_width/height`、`screen_scale_ratio1/2`；并把 `screen_ratio1 = screen_ratio2 = 1`（`:202-208`）。
2. `openAudio()`（`:539`）。
3. 分配 `accumulation_surface`、`backup_surface`、`effect_*_surface` 等，尺寸均为 `screen_width × screen_height`（`:541-546`）。
4. 初始化字体：默认 `FONT_FILE`，Linux 可走 FontConfig 查找日文字体（`:567-604`）。
5. 初始化音频通道、计时器、临时缓冲等（`:607-655`）。

### 5.3 主循环

`ONScripter::executeLabel()`（`ONScripter.cpp:1052-1104`）是解释器主循环：

```cpp
while (current_line < current_label_info.num_of_lines) {
    int ret = parseLine();                 // :1084
    if (ret & (RET_SKIP_LINE | RET_EOL)) { ... ++current_line ... }  // :1085-1088
    if (!(ret & RET_NO_READ)) readToken(); // :1090
}
current_label_info = script_h.lookupLabelNext(current_label_info.name); // :1093
```

- 每条脚本「指令」由 `readToken()` 切出，再由 `parseLine()` 分派（`ONScripter.cpp:1112-1173`）。
- 标签执行完毕后自动进入**下一个标签**（`lookupLabelNext`，`:1093`），直到没有下一个标签才 `endCommand()`（`:1103`）。
- 需要等待的指令（`wait`、文本显示、按钮）在命令内部调用 `waitEvent()` → `runEventLoop()`（`ONScripter_event.cpp:286-300`），此时主循环暂停在命令内部，事件循环接管。

### 5.4 命令分派细节

`ONScripter::parseLine()`（`ONScripter.cpp:1112`）：

1. `;`、`*`、`:` 开头的行直接 `RET_CONTINUE`（注释/标签/冒号）（`:1117-1119`）。
2. `script_h.isText()` 为真 → `textCommand()`（文本显示）（`:1121-1124`）。
3. `_` 前缀：跳过 `_` 后继续查表（`:1126-1149`）。
4. 用户自定义函数（`user_func_hash`）优先于内建命令（`:1128-1145`）。
5. 内建命令：取首字母槽 `func_hash[cmd[0]-'a']`，在 `num` 个连续条目中 `strcmp` 命中即调用成员函数指针（`:1151-1159`）。
6. 未命中时：`v`+数字 → `vCommand()`（播放 `wav*.wav`）；`dv`+数字 → `dvCommand()`（`:1163-1166`）。
7. 仍未知 → 打印 `command [%s] is not supported yet!!` 并 `skipToken()` 跳过整行（`:1168-1172`）。

> 关键结论：命令名在 `readToken()` 中已被**统一转小写**（`ScriptHandler.cpp:289`、`:335`），所以脚本里 `WINDOW` 与 `window` 等价。

---

## 6. 脚本系统

### 6.1 脚本来源与加载顺序

`ScriptHandler::readScript()`（`ScriptHandler.cpp:991-1080`）按**固定优先级**依次尝试打开：

| 顺序 | 文件名 | 加密模式 | 源码位置 |
|---|---|---|---|
| 1 | `0.txt` | 0（明文） | `ScriptHandler.cpp:999-1001` |
| 2 | `00.txt` | 0（明文） | `:1002-1004` |
| 3 | `nscr_sec.dat` | 2（magic 表异或） | `:1005-1007` |
| 4 | `nscript.___` | 3（key table 异或 0x84） | `:1008-1010` |
| 5 | `nscript.dat` | 1（XOR 0x84） | `:1011-1013` |
| 6 | `onscript.nt2` | 4 | `:1014-1016` |
| 7 | `onscript.nt3` | 5 | `:1017-1019` |

要点：

- 这是 **if/else if 链**：一旦某个文件打开成功就**不再尝试后面的格式**。因此同时存在 `0.txt` 与 `nscript.dat` 时，**明文 `0.txt` 优先**。
- 明文模式下，除 `0.txt`/`00.txt` 外，还会继续拼接 `1.txt … 99.txt`（补零到两位），见 `:1029-1043`（预估长度）与 `:1057-1069`（实际读取）。即**明文脚本由多个编号文件顺序拼接**。
- 注意 `0.txt` 与 `00.txt` 是**二选一**的入口（先试 `0.txt`，失败再试 `00.txt`），而后续循环从 `1.txt` 开始。README 提到修复了「读不到 `00.txt`」的旧 bug（`README.md`）。
- 全部失败时报错文案仍是老版本措辞：`can't open any of 0.txt, 00.txt, nscript.dat and nscript.___`（`:1021-1023`），**并未列出 nt2/nt3**。

### 6.2 编码与加密格式

#### 6.2.1 解密实现

`ScriptHandler::readScriptSub()`（`ScriptHandler.cpp:1082-1169`）逐字节处理，核心分支（`:1116-1136`）：

| 模式 | 算法 | 行号 |
|---|---|---|
| 0 | 原样（仅做换行归一化 `\r\n`→`\n`） | `:1116`、`:1138-1164` |
| 1 | `ch ^= 0x84` | `:1117` |
| 2 | `ch = (ch ^ magic[i]) & 0xff`，`magic[5]={0x79,0x57,0x0d,0x80,0x04}`，`i` 循环 0..4 | `:1084`、`:1118-1121` |
| 3 | `ch = key_table[ch] ^ 0x84`，需 `--key-exe` 提供 EXE 密钥表 | `:1091-1092`、`:1122-1124` |
| 4 | `ch ^= (0x85 & 0x97)`（即 `^= 0x85`，因 `0x85&0x97=0x85`），然后 `ch -= 1` | `:1125-1129` |
| 5 | `nt3_key` 滚动：`nt3_key ^= ch; nt3_key += ch*((nt3_size-0x920)+1-pos) + 0x5D588B65; ch ^= nt3_key` | `:1130-1136` |

nt3 的前置条件（`:1093-1103`）：文件必须 > `0x920` 字节，且从偏移 `0x91C` 读取 4 字节初始 `nt3_key`。

- **`nscript.dat` 的 XOR 0x84 已确认**：`ScriptHandler.cpp:1117`。加密与解密是同一运算（对称）。
- **`nt2`/`nt3` 为内建支持**，不依赖外部工具：`ScriptHandler.cpp:1014-1019`、`1125-1136`。
- **二次加密（double encryption）**：源码中**没有**对已解密内容再做一层解密的逻辑。若 `nscript.dat` 解出后仍是密文，引擎只会在词法分析时报「unknown heading character」，不会自动再解一层。因此「二次加密」在本分支属于**不受支持的外部加工**；检测到时应停止并报告（阶段 B 约束）。相关证据：解密只发生在 `readScriptSub` 一次（`:1082-1169`），此后 `readConfiguration()`/`labelScript()` 直接消费明文缓冲（`:1167`、`:1265`）。

#### 6.2.2 编码处理

- 内部统一为 UTF-16（`Coding2UTF16` 家族，`coding2utf16.h:27`）。
- 默认 `GBK2UTF16`（`onscripter_main.cpp:569`）；`--enc:sjis` → `SJIS2UTF16`，`--enc:gbk` → `GBK2UTF16`，`--enc:utf8` → 设置 `force_utf8`（`onscripter_main.cpp:383-389`）。
- 分词时按 `IS_TWO_BYTE(ch)` 判断双字节字符，UTF-8 模式额外按 `UTF8_N_BYTE(ch)` 处理三字节（`ScriptHandler.cpp:217-231`）。
- 脚本文件本身**不做 BOM 检测**（源码中未见 BOM 分支，**待验证**是否由解码器容错）。

### 6.3 行解析与命令分派机制

#### 6.3.1 词法：`readToken()`

`ScriptHandler::readToken()`（`ScriptHandler.cpp:182-316`）：

- 跳过空白后，按首字符分类：
  - `;` → 整行注释（`:198-204`）；
  - 文本起始字符（数字、`@ \ / % ? $ [ ( < ! # , "`、双字节、以及非英文模式的 `>`）→ 文本 token（`:205-254`）；
  - 字母/下划线 → 命令 token，**大小写归一化为小写**（`:285-297`）；
  - `*` → 标签（`:298-300`）；
  - `~`、换行、`:` → 控制 token（`:301-304`）。
- 文本 token 内会展开 `%变量`（`addIntVariable`）与 `$变量`（`addStrVariable`）（`:234-241`）。
- token 结束后由 `checkComma(buf)` 定位下一个 token 的起点（`:311`）。

#### 6.3.2 参数读取

命令函数通过 `script_h` 的三个原语取参：

| 原语 | 语义 | 实现 |
|---|---|---|
| `readInt()` | 读一个整数表达式，逗号分隔；支持变量/四则运算 | `ScriptHandler.cpp:384-401` → `parseIntExpression()` |
| `readStr()` | 读一个字符串 token；支持 `+` 拼接 | `ScriptHandler.cpp:357-382` |
| `readLabel()` | 读标签名（`*label` 或裸名），归一化小写 | `ScriptHandler.cpp:318-355` |
| `skipToken()` | 丢弃当前行剩余内容（未知命令时） | `ScriptHandler.cpp:403+` |

- **分隔符**：逗号 `,`。`checkComma()`（`ScriptHandler.cpp:1326-1336`）只消费**一个**逗号并置 `END_COMMA`；连续逗号表示**空参数**。`parseInt()` 在「一个字符都没读到」时返回 0（`:1515-1518`），因此空参数 = 0；`readStr()` 对空 token 返回空串。
- **可选参数判定**：命令函数用 `if (script_h.getEndStatus() & ScriptHandler::END_COMMA)` 判断「后面还有参数」（`ScriptHandler.cpp:1329-1331` 置位）。没有这个判断的命令会**无条件**读满参数，缺参数时读到的是空值/0 或下一行的内容。
- **默认值**：命令函数通常写成 `int a = script_h.readInt(); if (a==0) a = 默认;`，例如 `skipCommand`（`ScriptParser_command.cpp:211-215`）。因此**省略参数与显式传 0 在多数命令中不可区分**，这是转换器必须注意的语义陷阱。
- **表达式**：`readInt()` 不是简单 `atoi`，而是 `parseIntExpression()`（`ScriptHandler.cpp:1550+`），支持 `%变量`、`?数组`、`numalias` 别名与最多三段四则运算（`readNextOp`）。转换器若用正则替换数字，必须保留变量/别名表达式原样。
- **参数个数**：不是固定表，而是命令函数**按需顺序读取**；少读的参数不会被校验，多写的参数会被下一条命令误读——这是 NScripter 脚本的经典脆弱点。

#### 6.3.3 分派

见 5.4 节：`parseLine()` → `func_hash[首字母]` → 线性 `strcmp` → 成员函数指针。

### 6.4 命令总表（按功能分类，全量）

> 全量 348 条命令的逐条语法、参数语义与源码位置，见分册：
> - `docs/commands/part-01-add-cos.md`（49 条）
> - `docs/commands/part-02-cselbtn-getcselstr.md`（52 条）
> - `docs/commands/part-03-getcursor-ispage.md`（49 条）
> - `docs/commands/part-04-isskip-mp3.md`（68 条）
> - `docs/commands/part-05-mp3fadein-select.md`（62 条）
> - `docs/commands/part-06-selectcolor-waittimer.md`（60 条）
> - `docs/commands/part-07-wave-zenkakko.md`（8 条）
>
> 本节给出统计口径与分类索引；逐条明细以分册为准（分册与本表同源，均由 `analysis/commands.json` 对 `func_lut` 全量解析生成）。

**统计（已核验）**：

| 指标 | 数值 | 依据 |
|---|---|---|
| `func_lut` 注册条目 | 349 条（含 1 条哨兵 `{"", NULL}`） | `ONScripter_lut.cpp:28-402` |
| 真实命令条目 | **348** | 解析脚本统计 |
| 唯一命令名 | **347**（`nsa` 重复注册两次） | `ONScripter_lut.cpp:148`、`:151` |
| 不同处理函数 | **270** | `analysis/commands.json` |
| 处理函数可定位 | **348 / 348** | 同上 |
| 未定位 | **0** | 同上 |

> `nsa` 的两条注册分别指向 `nsaCommand`（`:148`）与 `arcCommand`（`:151`）。由于同首字母组内**线性查找、先命中先返回**（`ONScripter.cpp:1152-1158`），且 `nsaCommand` 条目在前，`{"nsa", &ONScripter::arcCommand}` 实际**不可达**。这属于「注册但不可达」项，必须在附录中单列。

### 6.4.1 功能分类索引（按名称/实现启发式归类）

> 归类基于命令名与处理函数的关键词，**仅用于导航**，不是语义断言；命令的准确语义以 6.5 分册为准。

| 类别 | 命令数 | 命令 |
|---|---|---|
| 精灵/绘制/特效 | 62 | `allsp2hide`, `allsp2resume`, `allsphide`, `allspresume`, `amsp`, `amsp2`, `bar`, `barclear`, `bg`, `bgcopy`, `bgcpy`, `blt`, `cell`, `cl`, `click`, `csp`, `csp2`, `draw`, `drawbg`, `drawbg2`, `drawclear`, `drawfill`, `drawsp`, `drawsp2`, `drawsp3`, `effect`, `effectblank`, `effectcut`, `effectskip`, `humanorder`, `ld`, `logsp`, `logsp2`, `lookbackcolor`, `lookbackflush`, `lookbacksp`, `lsp`, `lsp2`, `lsp2add`, `lsp2sub`, `lsph`, `lsph2`, `lsph2add`, `lsph2sub`, `monocro`, `msp`, `msp2`, `nega`, `prnum`, `prnumclear`, `quake`, `quakex`, `quakey`, `sp_rgb_gradation`, `spclclk`, `spreload`, `spstr`, `strsp`, `strsph`, `transmode`, `vsp`, `vsp2` |
| 文本与窗口 | 36 | `addkinsoku`, `clickstr`, `defaultfont`, `drawtext`, `english`, `erasetextwindow`, `getlogtext`, `gettext`, `indent`, `kinsoku`, `linepage`, `linepage2`, `locate`, `menu_window`, `menusetwindow`, `print`, `puttext`, `rubyoff`, `rubyon`, `setkinsoku`, `setwindow`, `setwindow2`, `setwindow3`, `tateyoko`, `textclear`, `textcolor`, `texthide`, `textoff`, `texton`, `textshow`, `textspeed`, `textspeeddefault`, `windowback`, `windowchip`, `windoweffect`, `zenkakko` |
| 按钮 | 21 | `bexec`, `bsp`, `btn`, `btndef`, `btndown`, `btntime`, `btntime2`, `btnwait`, `btnwait2`, `cellcheckexbtn`, `cellcheckspbtn`, `cselbtn`, `exbtn`, `exbtn_d`, `existspbtn`, `getbtntimer`, `lookbackbutton`, `selectbtnwait`, `spbtn`, `textbtnwait`, `transbtn` |
| 音频 | 44 | `bgm`, `bgmfadein`, `bgmfadeout`, `bgmonce`, `bgmstop`, `bgmvol`, `chvol`, `clickvoice`, `defmp3vol`, `defsevol`, `defvoicevol`, `dwave`, `dwaveload`, `dwaveloop`, `dwaveplay`, `dwaveplayloop`, `dwavestop`, `getbgmvol`, `getmp3vol`, `getsevol`, `getvoicevol`, `loopbgm`, `loopbgmstop`, `mode_wave_demo`, `movie`, `mp3`, `mp3fadein`, `mp3fadeout`, `mp3loop`, `mp3save`, `mp3stop`, `mp3vol`, `mpegplay`, `play`, `playonce`, `playstop`, `sevol`, `soundpressplgin`, `spi`, `stop`, `voicevol`, `wave`, `waveloop`, `wavestop` |
| 流程控制 | 36 | `break`, `csel`, `cselgoto`, `defsub`, `delay`, `end`, `for`, `getcselnum`, `getcselstr`, `gettimer`, `gosub`, `goto`, `if`, `jumpb`, `jumpf`, `loadgosub`, `luacall`, `menuselectcolor`, `menuselectvoice`, `next`, `nextcsel`, `notif`, `pretextgosub`, `resettimer`, `return`, `select`, `selectcolor`, `selectvoice`, `selgosub`, `selnum`, `systemcall`, `tablegoto`, `textgosub`, `trap`, `wait`, `waittimer` |
| 变量与运算 | 42 | `add`, `atoi`, `checkkey`, `checkpage`, `cmp`, `cos`, `dec`, `dim`, `div`, `getparam`, `getparam2`, `getzxc`, `globalon`, `inc`, `intlimit`, `itoa`, `itoa2`, `len`, `mid`, `mod`, `mode_ext`, `mode_saya`, `mov`, `mov10`, `mov3`, `mov4`, `mov5`, `mov6`, `mov7`, `mov8`, `mov9`, `movl`, `mul`, `numalias`, `rnd`, `rnd2`, `sin`, `split`, `splitstring`, `stralias`, `sub`, `tan` |
| 文件/存档/归档 | 27 | `arc`, `autosaveoff`, `deletescreenshot`, `fileexist`, `filelog`, `getsavestr`, `gettaglog`, `loadgame`, `ns2`, `ns3`, `nsa`, `nsa`, `nsadir`, `ofscopy`, `sar`, `savedir`, `savefileexist`, `savegame`, `savegame2`, `savename`, `savenumber`, `saveoff`, `saveon`, `savepoint`, `savescreenshot`, `savescreenshot2`, `savetime` |
| 系统/配置/调试 | 29 | `autoclick`, `automode`, `automode_time`, `avi`, `btime`, `date`, `defaultspeed`, `definereset`, `exec_dll`, `getlog`, `getscreenshot`, `getspmode`, `getversion`, `isskip`, `kidokumode`, `kidokuskip`, `labellog`, `luasub`, `menu_automode`, `repaint`, `reset`, `rmenu`, `rmode`, `setlayer`, `skip`, `skipoff`, `texec`, `time`, `versionstr` |
| 其它 | 13 | `abssetcursor`, `br`, `caption`, `flushout`, `humanz`, `layermessage`, `lr_trap`, `maxkaisoupage`, `ofscpy`, `roff`, `setcursor`, `shadedistance`, `tal` |
| **合计** | **348** | |

### 6.4.2 未定位 / 不可达 / 别名

- **未定位处理函数：0 条**。348 条全部定位到 `ONScripter::` 或 `ScriptParser::` 的定义（脚本解析结果见 `analysis/commands.json`）。
- **不可达注册：1 条**：`{"nsa", &ONScripter::arcCommand}`（`ONScripter_lut.cpp:151`），被 `:148` 的 `nsaCommand` 先命中。
- **别名组：48 组**，覆盖 348 条命令中的大部分（例如 `wave/waveloop`、`yesnobox/okcancelbox`、`lsp/lsph`、`btnwait/btnwait2/textbtnwait/selectbtnwait/bexec`）。
- 完整清单见 `docs/appendices/anomalies.md`。

### 6.4.3 统计口径

- 命令数 = `func_lut` 中**真实注册条目**（348），不是唯一命令名（347）。
- 「处理函数可定位」指 `analysis/extract_commands.py` 能在 `src/onsyuri/**/*.cpp` 中找到带函数体的 `ONScripter::foo` 或 `ScriptParser::foo` 定义。
- 分册按**处理函数名字典序**切分（便于与查找表顺序对应），因此同一命令的别名可能分布在相邻分册；按命令名检索请用附录 A。

### 6.5 每条命令的语法、参数语义、源码位置

见 6.4 节所列分册。每条记录格式：

```
命令名 | 处理函数 | 文件:行
语法：命令 参数1,参数2,...
参数：P1=类型/语义/是否坐标类；P2=...
备注：别名、默认值、坐标缩放行为
```

---

## 7. 坐标与尺寸系统

### 7.1 逻辑分辨率与窗口分辨率

ONScripter 有两套尺寸：

| 名称 | 变量 | 含义 | 证据 |
|---|---|---|---|
| 逻辑分辨率（脚本坐标系） | `script_h.screen_width/height` | 脚本作者书写的坐标空间；`;mode`/`;S` 设定 | `ScriptHandler.cpp:61-62`（默认 640×480）、`:1201-1249` |
| 引擎画布 | `ONScripter::screen_width/height` | 实际分配的 surface 尺寸；在本分支被设为等于逻辑分辨率 | `ONScripter.cpp:205-206` |
| 窗口/设备尺寸 | `screen_device_width/height` | 实际窗口像素，可由 `--width/--height` 指定 | `ONScripter.cpp:185-200` |
| 设备缩放比 | `screen_scale_ratio1/2` | `逻辑 / 设备`，用于鼠标坐标反算与 blit | `ONScripter.cpp:207-208` |
| 历史缩放比 | `screen_ratio1/2` | **本分支恒为 1**（硬件缩放） | `ONScripter.cpp:202-204` |

关键结论：

- `ONScripter.cpp:202-206` 明确把 `screen_ratio1 = screen_ratio2 = 1`，并令 `screen_width = script_h.screen_width`。因此 `AnimationInfo::scalePosXY()`（`AnimationInfo.h:119-122`）在本分支是**恒等变换**。
- 窗口缩放由渲染阶段完成：`SDL_RenderSetLogicalSize` 被**注释掉**（`ONScripter.cpp:267`），改用 `render_view_rect` + `screen_scale_ratio` 做视口映射（`ONScripter.cpp:884-913`、`ONScripter_event.cpp:1181-1276`）。
- 所以：**脚本坐标 = 逻辑像素，1:1 进入引擎内部**。若把 `;mode` 从 640×480 改为 1280×960 并把所有坐标 ×2，引擎会按 1280×960 的逻辑空间渲染，再整体缩放到窗口——这就是「×2 转换器」可行的原理。

### 7.2 分辨率声明命令

`ScriptHandler::readConfiguration()`（`ScriptHandler.cpp:1171-1263`）解析脚本**第一个 `;` 之后**的配置行：

| 写法 | 效果 | 行号 |
|---|---|---|
| `;mode1024` | 1024×768 | `:1199-1205` |
| `;mode800` | 800×600 | `:1206-1210` |
| `;mode400` | 400×300 | `:1211-1215` |
| `;mode320` | 320×240 | `:1216-1220` |
| `;$S<w>,<h>` 或 `;S<w>,<h>` | 任意宽高（`$` 表示「配置行」标志） | `:1192-1195`、`:1240-1250` |
| `;value<n>` / `;g<n>` / `;G<n>` | 全局变量上界 `global_variable_border` | `:1224-1232` |
| `;v<n>` / `;V<n>` | 变量总数 `variable_range` | `:1233-1239` |
| `;l<n>` | 跳过数字（语言/其它标志，未被使用） | `:1251-1255` |

- **默认值**：`screen_width=640, screen_height=480`（`ScriptHandler.cpp:61-62`）。
- `;mode` 只识别 4 个硬编码值；其他数字会 `break` 出配置解析（`:1221-1222`），即**非法 mode 被静默忽略**（尺寸保持默认或之前的值）。
- `;$S` 的宽度与高度之间可用 `,`、空格、制表符分隔（`:1246`）。
- **`;$S` 没有范围校验**：解析只是逐位累加十进制数字（`:1243-1249`），因此 0、1、超大值都会被接受，随后在 `allocSurface(screen_width, screen_height)`（`ONScripter.cpp:542-546`）处才可能失败；合法实用范围取决于内存与渲染器，源码未限制（**待运行验证**）。
- `;mode` 的合法值只有 4 个；`;S` 才是任意分辨率的唯一入口。
- 配置行必须位于脚本**最前面的 `;` 注释行**内（`:1176-1188` 从文件头扫到第一个 `;`）。

### 7.3 坐标命令全表（参数级标注）

> 见 `docs/appendices/coord-commands.md`。该表从 6.4 全量命令表中筛出所有携带坐标/尺寸/字号/间距/速度类参数的命令，逐参数标注「是否 ×2」「是否需人工复核」，并给出判定依据（源码中对 `orig_pos`、`screen_width`、`font_size` 等的赋值位置）。
>
> 汇总：38 条命令（含别名）、22 个命令组进入白名单；其中 10 个参数标「人工复核」（`blt` P5–P8、`btn` P4–P7、`setcursor` P3–P4）。
>
> 两个**必须记住的反例**：
> - `locate x,y` 与 `indent n` 的单位是**字符/行**，不是像素，**不得 ×2**（`FontInfo.cpp:199-203`、`ONScripter_text.cpp:206-211`）。
> - `getcursorpos` 等取值命令返回的是像素坐标，但命令本身没有坐标输入参数，**不得修改**；脚本若拿返回值与字面量比较，字面量会失配 → 报告为人工复核项。

### 7.4 缩放与坐标系变换机制

- `AnimationInfo` 同时保存 `orig_pos`（脚本坐标）与 `pos`（引擎坐标），`scalePosXY/WH` 在两者间换算（`AnimationInfo.h:119-126`）。本分支比值为 1。
- 文本/字体：`FontInfo::openFont(font_file, screen_ratio1, screen_ratio2)`（`ONScripter.cpp:680`），字号在 `FontInfo` 内部按比例处理（`FontInfo.cpp`，**待逐行验证**）。
- 鼠标/触摸：设备坐标 → 逻辑坐标用 `screen_scale_ratio1/2` 反算（`ONScripter_event.cpp:1181-1276`）。
- 图像 blit：`ONScripter_command.cpp:3735-3737` 对 `dst_rect` 除以 `screen_scale_ratio` 后再 `SDL_RenderCopy`。

> 因此「脚本坐标 ×2」不需要修改引擎任何缩放代码，只需同时改 `;mode`/`;S` 与脚本中的坐标类参数。这为阶段 B 的白名单策略提供了依据。

### 7.5 ×2 的边界：素材不会自动放大

- `createBackground()` 分配的是 `screen_width × screen_height` 的底图，但把**原尺寸**背景图**居中**贴入：图比屏幕小就留黑边，图比屏幕大就裁掉两侧（`ONScripter_image.cpp:842`、`:862-874`）。**没有**任何「按新分辨率拉伸素材」的逻辑。
- `setupAnimationInfo()` 同样按图片原始像素建立 surface（`ONScripter_animation.cpp:139`），精灵的 `pos.w/h` 来自图片尺寸（除非脚本显式给 `scale_x/scale_y`）。
- 结论：**只把脚本坐标 ×2 而不重制素材，画面会变成「1280×960 画布 + 640×480 素材居中/偏小」**。阶段 B 的转换报告必须明确：本工具只改脚本坐标，不生成放大后的图片；素材需另行 ×2 重制（或用 `scale_x/scale_y` 显式放大，但那会改变脚本语义，不在白名单内）。
- 同理，`blt`/`btn` 的源图坐标（N3）是否 ×2，取决于素材是否重制——这是把 N3 标为「人工复核」的根本原因。

---

## 8. 图像与素材加载

### 8.1 一条图像的加载路径

1. 命令把文件名写入 `AnimationInfo::image_name`（`AnimationInfo.cpp:162-166`，`setImageName` 只做拷贝）。
2. `parseTaggedString()` 解析文件名中的标签（`ONScripter_animation.cpp:264`）。
3. `setupAnimationInfo()` 真正取图（`ONScripter_animation.cpp:139`）。
4. `ONScripter::loadImage()`（`ONScripter_image.cpp:37-65`）：
   - 文件名以 `>` 开头 → `createRectangleSurface()`，用 `>w,h,#RRGGBB,#RRGGBB...` 直接生成纯色矩形条（`:67-127`）；
   - 否则 → `createSurfaceFromFile()`（`:134-213`）。
5. `createSurfaceFromFile()`：
   - 通过 `script_h.cBR->getFileLength(filename)` 在**当前归档/目录**中查找（`:137`）。**不做扩展名猜测**，脚本写什么名字就找什么名字。
   - 读入内存后用 SDL_image 按**内容**识别格式（`IMG_isPNG`、`IMG_Load_RW`，`:194-196`）；`.JPG/.jpg` 解码失败时强制走 `IMG_LoadJPG_RW`（`:197-200`）。
   - 若归档里找不到且设置了 `save_dir`，会再尝试 `save_dir + basename`（`:139-159`），用于读档缩略图。
   - 文件大小会参与「平均图片大小」统计，用于复用缓冲区（`:168-187`）。

> 结论：素材**没有**「png→jpg→webp」自动回退；转换器不能假设引擎会自己找替代扩展名。

### 8.2 图像名中的标签语法（直接影响坐标/字号）

`parseTaggedString()`（`ONScripter_animation.cpp:264-410`）在文件名前解析 `:` 前缀：

| 前缀 | 含义 | 行号 |
|---|---|---|
| `:a` | 阿尔法混合（TRANS_ALPHA） | `:277-280` |
| `:l` | 左上角基准（TRANS_TOPLEFT） | `:281-284` |
| `:r` | 右上角基准（TRANS_TOPRIGHT） | `:285-288` |
| `:c` | 直接拷贝（TRANS_COPY） | `:289-292` |
| `:s` | 字符串精灵（TRANS_STRING） | `:293-333` |
| `:m` | 蒙版（TRANS_MASK），`;` 前是蒙版文件名 | `:334-340` |
| `:#RRGGBB` | 直接色（TRANS_DIRECT） | `:341-345` |
| `:!n` | 调色板（TRANS_PALLETTE） | `:346-350` |
| `*n` | 内建图层（TRANS_LAYER） | `:355-375` |

**字符串精灵 `:s`** 的完整语法（`:293-333`）：

```
:s[/<字号x>,<字号y>[,<字距>[,<抗锯齿模式>]]]#RRGGBB[#RRGGBB...]
```

- 若无 `/`，字号/字距继承当前 `sentence_font`（`:315-320`）。
- `/` 后依次读：字号 x、字号 y；若有逗号再读「字距附加值」（加到 `font_pitch[0]` 上）；再有一个可选整数表示抗锯齿模式（0 普通 / 1 无抗锯齿 / 2 袋文字）。
- 之后连续 `#RRGGBB` 的个数决定 `num_of_cells`（颜色 cell 数）。

**动画帧**（`:378-406`）：`/<cells>[,<duration>|<d1,d2,...>],<loop>`：
- `/n` 后，若跟 `,`，则读一个时长或 `<d1,d2,...>` 列表；
- 再读一个数字作为 `loop_mode`（`3` 表示不播放动画）。

> **对 ×2 转换的意义**：`:s/24,24,...` 里的字号与字距属于「坐标类」数值，必须与 `setwindow` 的字号一起翻倍；否则窗口字号翻倍而字符串精灵字号不变，会出现大小不一致。这是全量命令表之外的**隐藏坐标点**，必须在转换器中单独处理（见 7.3 与附录 B）。

### 8.3 归档读取

- 抽象基类 `BaseReader`（`BaseReader.h:62`）定义 `open/getFile/getFileLength/getArchiveName` 等接口。
- `DirectReader`（`DirectReader.h:33`）直接读目录；`DirectReader.cpp:291` 的 `getFileByIndex()` 支持按索引枚举。
- `SarReader`（`SarReader.h:29`）读 NScripter 标准 `.sar`：`SarReader::open()`（`SarReader.cpp:45`）解析文件表。
- 压缩类型：`NO_COMPRESSION`、`NBZ_COMPRESSION`（bzip2）、`SPB_COMPRESSION`（`SarReader.cpp:113`、`:139-157`；注册入口见 `ScriptParser_command.cpp:206` 的 `registerCompressionType`）。
- `NsaReader`（`NsaReader.h:31`）在 `SarReader` 基础上支持 `.nsa/.ns2/.ns3` 多归档（具体扩展名分支**待逐行验证** `NsaReader.cpp`）。
- `arc`/`nsa` 命令在运行时切换 reader（`ScriptParser_command.cpp:1451-1480`）。

### 8.4 背景与图层

- `createBackground()`（`ONScripter_image.cpp:836`）按 `bg_info` 生成背景，尺寸为 `screen_width × screen_height`。
- 精灵数组：`sprite_info[MAX_SPRITE_NUM]`（普通精灵）、`sprite2_info[MAX_SPRITE2_NUM]`（`sp2` 系）、`tachi_info[3]`（立绘左/中/右）、`bar_info[MAX_PARAM_NUM]`（进度条）。
- 绘制顺序由 `humanorder`（z 序）与 `drawTaggedSurface` 控制（`ONScripter_image.cpp:722-773`）。

---

## 9. 文本渲染

### 9.1 字体对象

- `FontInfo`（`FontInfo.h:38`）保存：`ttf_font[2]`、`font_size_xy[2]`、`pitch_xy[2]`（字距）、`num_xy[2]`（每行字数/行数）、`top_xy[2]`（窗口左上角）、`xy[2]`（当前绘制位置）、`wait_time`、`is_bold/is_shadow/is_transparent`、`window_color` 等。
- `FontInfo::openFont(font_file, ratio1, ratio2)`（`FontInfo.cpp:95`）打开 TTF；默认字体文件由 `FONT_FILE` 指定（`ONScripter.cpp:572`），Linux 下可用 FontConfig 查找日文字体（`:574-603`）。

### 9.2 文本窗口参数：`setwindow` / `setwindow2` / `setwindow3`

三者共用 `setwindowCore()`（`ONScripter_command.cpp:660-706`），参数顺序**完全一致**：

| 序号 | 参数 | 写入字段 | 语义 | ×2 |
|---|---|---|---|---|
| P1 | x | `top_xy[0]` | 文本框左上角 x（像素） | 是 |
| P2 | y | `top_xy[1]` | 左上角 y（像素） | 是 |
| P3 | 列数 | `num_xy[0]` | 每行字符数（**不是像素**） | 否 |
| P4 | 行数 | `num_xy[1]` | 可见行数（**不是像素**） | 否 |
| P5 | 字号 x | `font_size_xy[0]` | 字符宽（像素） | 是 |
| P6 | 字号 y | `font_size_xy[1]` | 字符高（像素） | 是 |
| P7 | 字距附加值 | `pitch_xy[0] = P7 + font_size_x` | 字符间距的**附加值**（像素） | 是 |
| P8 | 行距附加值 | `pitch_xy[1] = P8 + font_size_y` | 行间距的**附加值**（像素） | 是 |
| P9 | 等待时间 | `wait_time` | 每字符显示延迟（ms） | 否 |
| P10 | 粗体 | `is_bold` | 0/1 | 否 |
| P11 | 阴影 | `is_shadow` | 0/1 | 否 |
| P12 | 窗口样式 | — | `#RRGGBB`（透明色）或图片文件名 | 否 |

- 若 P12 是 `#` 开头：再读 P13–P16 作为窗口矩形 `x1,y1,x2,y2`（`orig_pos.w = x2-x1+1`、`h = y2-y1+1`）（`:680-688`）。这四个值都是像素，**×2**。
- 否则：P12 是窗口图片名，再读 P13–P14 作为窗口左上角 `x,y`（`:692-698`），**×2**。

> 这就是「字号翻倍但列数不翻倍」的典型例子：窗口能容纳的字符数不变，像素尺寸翻倍。转换器若把 P3/P4 也 ×2，会导致文本窗口逻辑错乱。

### 9.3 文本绘制流程

- `textCommand()`（`ONScripter_text.cpp:682`）处理一行文本：解析 `\` 转义、clickstr、ruby、`%`/`$` 变量展开，逐字调用 `drawChar()`。
- `drawChar()`（`ONScripter_text.cpp:188`）负责换行、缩进（`indent_offset`）、TTF 渲染、阴影/描边、写入 `current_page` 与回看缓冲。
- `enterTextDisplayMode()` / `leaveTextDisplayMode()`（`:455` / `:474`）切换文本显示模式，并触发窗口重绘。

### 9.4 文本相关命令（坐标相关者）

| 命令 | 处理函数 | 坐标参数 | 证据 |
|---|---|---|---|
| `locate` | `locateCommand` | x,y（文本光标绝对定位） | `ONScripter_command.cpp:1856` |
| `indent` | `indentCommand` | 缩进像素 | `:2100` |
| `print` | `printCommand` | 文本 + 可选 x,y? | `:1246` |
| `puttext` | `puttextCommand` | 文本 + x,y | `:1170` |
| `rubyon` | `rubyonCommand` | 字号/位置由 ruby 结构决定 | `:2873` 附近；`rubyonCommand` 见 6.5 分册 |
| `textcolor` | `textcolorCommand` | 颜色（非坐标） | `:282` |
| `textclear` | `textclearCommand` | 无 | `:289` |
| `textspeed` | `textspeedCommand` | 速度（ms/字符） | `:233` |
| `tateyoko` | `tateyokoCommand` | 0=横排/1=竖排 | `:309` |
| `menusetwindow` | `menusetwindowCommand` | 菜单窗口字号/字距 | 见 6.5 分册 |
| `erasetextwindow` | `erasetextwindowCommand` | 无 | `:2873` |

> 具体参数顺序与完整语义以 6.5 分册为准；本表只标注坐标相关性。

---

## 10. 音频系统

### 10.1 初始化与通道

- `openAudio()`（`ONScripter.cpp:328-355`）：`Mix_OpenAudio(44100, ...)`，随后 `Mix_AllocateChannels(ONS_MIX_CHANNELS + ONS_MIX_EXTRA_CHANNELS)` 并注册 `waveCallback`。
- 通道常量（`ONScripter.h:65-70`）：
  - `ONS_MIX_CHANNELS = 50`（编号 0–49，SE/voice）；
  - `ONS_MIX_EXTRA_CHANNELS = 4`；
  - `MIX_WAVE_CHANNEL = 50`（语音/波形）；
  - `MIX_BGM_CHANNEL = 51`（BGM）；
  - `MIX_LOOPBGM_CHANNEL0/1 = 52/53`（循环 BGM）。
- `wave_sample[54]`（`ONScripter.h:735`）保存已加载的 `Mix_Chunk`。

### 10.2 播放接口

- `ONScripter::playSound(filename, format, loop_flag, channel)`（`ONScripter_sound.cpp:72`）：按 `SOUND_CHUNK`/`SOUND_MUSIC` 分派到 `Mix_LoadWAV` / `Mix_LoadMUS`，支持 mp3/ogg（由 SDL_mixer 决定）。
- BGM 入口在 `ONScripter_sound.cpp:171-179`（CD 音轨与文件两条路径）。
- `stopBGM(continue_flag)`（`:423`）。

### 10.3 音量与淡入淡出

- 命令层：`wave/waveloop`、`mp3`、`mp3loop`、`mp3fadein/out`、`sevol`、`voicevol`、`chvol`、`loopbgm`、`loopbgmstop`、`play/playonce/playstop`。
- 音量统一按百分比 0–100 换算：`Mix_Volume(ch, vol * MIX_MAX_VOLUME / 100)`（如 `ONScripter_command.cpp:164`、`:709`）。

---

## 11. 事件与输入

### 11.1 事件循环

- `waitEvent(count)`（`ONScripter_event.cpp:295`）→ `waitEventSub(count)`（`:286`）→ `runEventLoop()`（`:291`）。
- 等待类型由 `event_mode` 位掩码决定：`WAIT_TIMER_MODE`、`WAIT_INPUT_MODE`、`WAIT_BUTTON_MODE`、`WAIT_TEXT_MODE`、`WAIT_RCLICK_MODE`、`WAIT_VOICE_MODE` 等（`ONScripter_event.cpp:335-497`）。
- 典型命令：`wait`（`:131`）、`waittimer`（`:120`）、`click`（`:3232`）、`btnwait`（`:3355`）。

### 11.2 键鼠映射（已核验部分）

- `Return` / 小键盘 Enter / `Space`：推进文本（`ONScripter_event.cpp:1009-1017`）。
- `s`：切换 skip（`:1022-1027`）。
- `o`：切换「整页绘制」`SKIP_TO_EOP`（`:1029-1039`）。
- 鼠标/触摸坐标需乘以 `screen_scale_ratio1/2` 并减去 `render_view_rect` 偏移（`:1181-1276`）。

> 完整键位表（方向键、Ctrl、滚轮、手柄）见 `ONScripter_event.cpp` 的 `runEventLoop` 主体，**待逐键补全**。

### 11.3 按钮

- `ButtonLink`（`ButtonLink.h:29`）保存 `image_rect`（屏幕命中区）、`select_rect`、`button_type`（`NORMAL_BUTTON`/`SPRITE_BUTTON`/`TMP_SPRITE_BUTTON`/`LOOKBACK_BUTTON`）、`exbtn_ctl[3]`。
- 按钮命令：`btn`（`:3572`）、`bsp`（`:3612`）、`spbtn`、`exbtn`、`btndef`、`btntime`、`btnwait`。
- `exbtn` 控制串支持 `C`（改 cell）、`P`（显示/隐藏精灵）、`S`（播放声音）等子命令（`ONScripter.cpp:1377-1410` 的 `decodeExbtnControl`）。

---

## 12. 运行时文件约定

### 12.1 启动必需文件

按 6.1 的优先级，**至少存在一个**脚本载体：

- `0.txt` 或 `00.txt`（明文，可继续拼接 `1.txt`…`99.txt`）；
- `nscript.dat`（XOR 0x84）；
- `onscript.nt2` / `onscript.nt3`；
- `nscr_sec.dat` / `nscript.___`（需额外密钥）。

### 12.2 素材与归档

- 若脚本用 `arc`/`nsa` 指定归档，则素材从 `.sar/.nsa` 读取；否则从当前目录直读（`DirectReader`）。
- 图像、音频文件名均由脚本原样给出，**不做扩展名回退**（见 8.1）。

### 12.3 存档文件

| 文件 | 用途 | 证据 |
|---|---|---|
| `save%d.dat`（1–?) | 每个存档槽 | `ONScripter_file.cpp:62`、`:82`、`:99`、`:124`、`:148` |
| `envdata` | 环境/全局设置（**不使用 save_dir**） | `ScriptParser.cpp:359-364` |
| `gloval.sav` | `globalon` 时的全局变量 | `ScriptParser.cpp:336` |
| `savedir` 指定的目录 | 上述存档的根目录（除 `envdata`） | `ScriptParser.cpp:360` |

- `savedir` 命令设置 `save_dir`；未设置时存档写在 `archive_path`（`ONScripter_file.cpp:62`）。
- 存档读写入口：`ONScripter_file.cpp` 的 `saveSaveFile/loadSaveFile`（函数名与行号见 6.5 分册的 `savegame/loadgame`）。

---

## 13. 扩展机制（Lua）

- 编译期开关 `USE_LUA`（`CMakeLists.txt:521`）；实现 `LUAHandler`（`LUAHandler.h:32`、`LUAHandler.cpp`）。
- 脚本命令 `luacall` / `luasub`（`ONScripter_lut.cpp:196-197`），分别调用 Lua 函数/以子程序方式执行。
- 注册给 Lua 的 API 表（`LUAHandler.cpp:969-1077`，`LUA_FUNC_LUT` 宏）：

| 类别 | API |
|---|---|
| 流程 | `NSGoto`、`NSGosub`、`NSReturn`、`NSEnd`、`NSExec`、`NSExecAnimation`、`NSSleep`、`NSTimer`、`NSDoEvents`、`NSUpdate` |
| 变量 | `NSGetIntValue`、`NSSetIntValue`、`NSGetStrValue`、`NSSetStrValue`、`NSPopInt`、`NSPopIntRef`、`NSPopStr`、`NSPopStrRef`、`NSPopLabel`、`NSPopID`、`NSPopComma`、`NSCheckComma` |
| 精灵 | `NSSpLoad`、`NSSpCell`、`NSSpClear`、`NSSpGetInfo`、`NSSpGetPos`、`NSSpMove`、`NSSpVisible`、`NSSp2Load`、`NSSp2Move`、`NSSp2Clear`、`NSSp2GetInfo`、`NSSp2GetPos`、`NSSp2Visible`、`NSDLoad`、`NSDCall`、`NSDDelete`、`NSDSetSprite`、`NSDSp2`、`NSDPresentRect` |
| 输入 | `NSGetClick`、`NSGetKey`、`NSGetMouse`、`NSGetSkip` |
| 窗口 | `NSGetWindowSize`、`NSLuaAnimationMode`、`NSLuaAnimationInterval` |
| 音频 | `NSOggLoad`、`NSOggPlay`、`NSOggClose`、`NSOggFade`、`NSOggVolume` |
| 编码 | `nsutf_from_ansi`、`nsutf_to_ansi` |
| 占位 | `NSOkBox`、`dpshadow_make`、`dpshadow_merge`（dummy） |

> Lua 动画通过 `NSLuaAnimationMode/Interval` 与 `NSExecAnimation` 驱动，具体帧循环见 `LUAHandler.cpp` 的 `is_animatable` 分支（**待逐行验证**）。

---

## 14. 已知差异与兼容性说明

### 14.1 与本快照直接相关的差异（已核验）

| 差异 | 事实 | 证据 |
|---|---|---|
| 无 `sp` 命令 | 全表检索 `func_lut` **不存在** `{"sp", ...}`；`ONScripter`/`ScriptParser` 也没有 `spCommand` 定义 | `ONScripter_lut.cpp:27-403`；全仓 `grep spCommand` 仅命中 `vsp/strsp/msp/lsp/logsp/drawsp/csp/bsp/amsp` |
| 无 `textwindow` 命令 | 仅存在 `erasetextwindow`；文本窗口用 `setwindow/setwindow2/setwindow3` | `ONScripter_lut.cpp:299`；`ONScripter_command.cpp:660` |
| `nsa` 重复注册 | 第二条 `{"nsa", &ONScripter::arcCommand}` 不可达 | `ONScripter_lut.cpp:148`、`:151`；`ONScripter.cpp:1152-1158` |
| 分辨率声明受限 | `;mode` 只认 1024/800/400/320；640×480 只能靠默认值或 `;$S640,480` | `ScriptHandler.cpp:1199-1222`、`:61-62` |
| 窗口缩放与逻辑分辨率解耦 | `--width/--height` 只改窗口，不改逻辑坐标系 | `ONScripter.cpp:185-208` |
| 加密扩展 | 内建 nt2/nt3 解密；不支持二次加密 | `ScriptHandler.cpp:1014-1019`、`:1125-1136` |
| 编码扩展 | 支持 `--enc:utf8`（原版不支持） | `onscripter_main.cpp:389`；README |

> 关于 `sp`/`textwindow` 缺失：这可能是 OnscripterYuri/Jh 的移植裁剪，也可能是快照不完整。**在阶段 B 之前必须用真实脚本运行验证**；若脚本确实使用 `sp`，本引擎会走 `command [sp] is not supported yet!!`（`ONScripter.cpp:1168`）。此项标记为 **待验证（高优先级）**。

### 14.2 与其它分支的差异（定性）

- **原版 ONScripter**：无 Lua、无 utf8、无 nt2/nt3；坐标系同样以脚本分辨率为准。
- **ONScripter-EN**：英文文本/半角优化，命令集与日文版略有差异（本快照的 `ENABLE_1BYTE_CHAR` 对应半角字符支持，`CMakeLists.txt:523`）。
- **Ponscripter**：以插件与高分辨率支持著称，命令集更宽；本快照不含 Ponscripter 专有命令。

> 上述为方向性结论；逐条命令差异需以各自源码比对，本文不逐一断言。

---

## 15. 附录

- 附录 A：命令索引（按字母）→ `docs/appendices/command-index.md`
- 附录 B：坐标命令全表（参数级 ×2 标注）→ `docs/appendices/coord-commands.md`
- 附录 C：源码行号索引 → `docs/appendices/source-index.md`
- 附录 D：未定位 / 不可达 / 别名 命令清单 → `docs/appendices/anomalies.md`
- 附录 E：转换器覆盖自检 → `docs/appendices/converter-coverage.md`
