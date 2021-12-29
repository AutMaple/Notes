# 内置函数

在vim中使用 `help function-list `指令 可以查询内置函数列表

使用 help 函数名 可以查询 函数具体用法

# 字符串

- `nr2char()` 通过数值码值取得一个字符
- `list2str()` 从数值列表取得字符字符串
- `char2nr() `取得字符的数值码值
- `str2list() `从字符串取得数值列表
- `str2nr()` 把字符串转换为数值
- `str2float()` 把字符串转换为浮点数
- `printf()` 根据 % 项目格式化字符串
- `escape()` 将字符串通过 '\' 转义
- `shellescape()` 转义字符串用于外壳命令
- `fnameescape()` 转义 Vim 命令使用的文件名
- `tr()` 把一组字符翻译成另一组
- `strtrans()` 将一个字符串变成可显示的格式
- `tolower()` 将一个字符串转换为小写
- `toupper()` 将一个字符串转换为大写
- `match()` 字符串中的模式匹配处
- `matchend()` 字符串中的模式匹配结束处
- `matchstr()` 在一个字符串中匹配一个模式
- `matchstrpos()` 字符串中满足匹配的模式和位置
- `matchlist()` 类似 matchstr()，同时返回子匹配
- `stridx()` 子串在母串中第一次出现的地方
- `strridx() `子串在母串中最后一次出现的地方
- `strlen()` **字符串字节长度**
- `strchars() `**字符串的字符长度**
- `strwidth()` 字符串的显示长度
- `strdisplaywidth()` 字符串的显示长度，处理制表
- `substitute()` 用一个字符串替换一个匹配的模式
- `submatch()` 取得 ":s" 和 substitute() 匹配中指定的某个匹配
- `strpart()` 用字节索引取得字符串的子串
- `strcharpart()` 用字符索引获取字符串的子串
- `strgetchar()` 用字符索引获取字符串里的字符
- `expand()` 展开特殊的关键字
- `expandcmd()` 像 :edit 那样扩展命令
- `iconv()` 转换文本编码格式
- `byteidx()` 字符串里字符的字节位置
- `byteidxcomp()` 类似于 byteidx()，但计算组合字符
- `repeat()` 重复字符串多次
- `eval()` 计算字符串表达式
- `execute()` 执行 Ex 命令并获取输出
- `win_execute()` 类似于 execute()，但用于指定窗口
- `trim()` 从字符串中删除字符

# 列表



- `get()` 得到项目，错误索引不报错
- `len()` 列表的项目总数
- `empty()` 检查列表是否为空
- `insert()` 在列表某处插入项目
- `add()` 在列表后附加项目
- `extend()` 在列表后附加另一个列表
- `remove()` 删除列表里一或多个项目
- `copy()` 建立列表的浅备份
- `deepcopy()` 建立列表的完整备份
- `filter()` 删除列表的选定项目
- `map()` 改变每个列表项目
- `sort()` 给列表排序
- `reverse()` 反转列表项目的顺序
- `uniq()` 删除重复邻接项目的备份
- `split()` 分割字符串成为列表
- `join()` 合并列表项目成为字符串
- `range()` 返回数值序列的列表
- `string()` 列表的字符串表示形式
- `call()` 调用函数，参数以列表形式提供
- `index()` 列表里某值的索引
- `max()` 列表项目的最大值
- `min()` 列表项目的最小值
- `count()` 计算列表里某值的出现次数
- `repeat()` 重复列表多次

# 字典

- get() 得到项目，错误的键不报错
- len() 字典项目的总数
- has_key() 检查某键是否出现在字典里
- empty() 检查字典是否为空
- remove() 删除字典的项目
- extend() 从一个字典增加项目到另一个字典
- filter() 删除字典的选定项目
- map() 改变每个字典项目
- keys() 得到字典的键列表
- values() 得到字典的值列表
- items() 得到字典的键-值组对的列表
- copy() 建立字典的浅备份
- deepcopy() 建立字典的完整备份
- string() 字典的字符串表示形式
- max() 字典项目的最大值
- min() 字典项目的最小值
- count() 计算字典里某值的出现次数

# 浮点计算

- float2nr() 把浮点数转换为数值
- abs() 绝对值 (也适用于数值)
- round() 四舍五入
- ceil() 向上取整
- floor() 向下取整
- trunc() 删除小数点后的值
- fmod() 除法的余数
- exp() 指数
- log() 自然对数 (以 e 为底的对数)
- log10() 以 10 为底的对数
- pow() x 的 y 次方
- sqrt() 平方根
- sin() 正弦
- cos() 余弦
- tan() 正切
- asin() 反正弦
- acos() 反余弦
- atan() 反正切
- atan2() 反正切
- sinh() 双曲正弦
- cosh() 双曲余弦
- tanh() 双曲正切
- isnan() 检查非数

# 其它計算



- and() 按位与
- invert() 按位取反
- or() 按位或
- xor() 按位异或
- sha256() SHA-256 哈希

# 变量

- `type()` 变量的类型
- islocked() 检查变量是否加锁
- funcref() 返回指向函数的函数引用
- function() 得到函数名对应的函数引用
- getbufvar() 取得指定 buffer 中的变量值
- setbufvar() 设定指定 buffer 中的变量值
- getwinvar() 取得指定窗口的变量值
- gettabvar() 取得指定标签页的变量值
- gettabwinvar() 取得指定窗口和标签页的变量值
- setwinvar() 设定指定窗口的变量值
- settabvar() 设定指定标签页的变量值
- settabwinvar() 设定指定窗口和标签页的变量值
- garbagecollect() 可能情况下释放内存

# 光标和标记位置



- `col() `**光标或位置标记所在的列**
- virtcol() 光标或位置标记所在的屏幕列
- line() 光标或位置标记所在行
- wincol() 光标所在窗口列
- winline() 光标所在窗口行
- cursor() 置光标于 行/列 处
- screencol() 得到光标的屏幕列
- screenrow() 得到光标的屏幕行
- screenpos() 文本字符的屏幕行与列
- getcurpos() 得到光标位置
- getpos() 得到光标、位置标记等的位置
- setpos() 设置光标、位置标记等的位置
- byte2line() 取得某字节位置所在行号
- line2byte() 取得某行之前的字节数
- diff_filler() 得到一行之上的填充行数目
- screenattr() 得到屏幕行的属性
- screenchar() 得到屏幕行的字符代码
- screenchars() 得到屏幕行的多个字符代码
- screenstring() 得到屏幕行的字符字符串

#  buffer 

- `getline()` **从 buffer 中取一行**
- `setline()` **替换 buffer 中的一行**
- append() 附加行或行的列表到 buffer 
- indent() 某行的缩进
- cindent() 根据 C 缩进法则的某行的缩进
- lispindent() 根据 Lisp 缩进法则的某行的缩进
- nextnonblank() 查找下一个非空白行
- prevnonblank() 查找前一个非空白行
- search() 查找模式的匹配
- searchpos() 寻找模式的匹配
- searchpair() 查找 start/skip/end 配对的另一端
- searchpairpos() 查找 start/skip/end 配对的另一端
- searchdecl() 查找名字的声明
- getcharsearch() 返回字符搜索信息
- setcharsearch() 设置字符搜索信息

# 操作另一個 buffer 

- getbufline() 取得指定 buffer 的行列表
- setbufline() 替换指定 buffer 的一行
- appendbufline() 给指定 buffer 附加行列表
- deletebufline() 从指定 buffer 中删除多行

# 系统调用及文件

- glob() 展开通配符
- globpath() 在几个路径中展开通配符
- glob2regpat() 转换 glob 模式到搜索模式
- findfile() 在目录列表里查找文件
- finddir() 在目录列表里查找目录
- resolve() 找到一个快捷方式所指
- fnamemodify() 改变文件名
- pathshorten() 缩短路径里的目录名
- simplify() 简化路径，不改变其含义
- executable() 检查一个可执行程序是否存在
- exepath() 可执行程序的完整路径
- filereadable() 检查一个文件可读与否
- filewritable() 检查一个文件可写与否
- getfperm() 得到文件权限
- setfperm() 设置文件权限
- `getftype()` 得到文件类型
- isdirectory() 检查一个目录是否存在
- getfsize() 取得文件大小
- `getcwd()` 取得当前工作路径
- haslocaldir() 检查当前窗口是否使用过 :lcd 或 :tcd
- tempname() 取得一个临时文件的名称
- `mkdir()` 建立新目录
- `chdir()` 改变当前目录
- `delete()` 删除文件
- `rename()`重命名文件
- system() 得到字符串形式的外壳命令结果
- systemlist() 得到列表形式的外壳命令结果
- environ() 得到所有环境变量
- getenv() 得到一个环境变量
- setenv() 设置一个环境变量
- hostname() 系统的名称
- readfile() 读入文件到一个行列表
- readdir() 从目录得到文件名的列表
- writefile() 把一个行列表或 blob 写到文件里

# 时间日期

- getftime() 得到文件的最近修改时间
- localtime() 得到以秒计的当前时间
- strftime() 把时间转换为字符串
- strptime() 把日期/时间字符串转换为时间
- reltime() 得到准确的当前或者已经经过的时间
- reltimestr() 把 reltime() 的结果转换为字符串
- reltimefloat() 把 reltime() 的结果转换为浮点数

#  buffer  窗口以及参数列表

- argc() 参数列表项数
- argidx() 参数列表中的当前位置
- arglistid() 得到参数列表的编号
- argv() 从参数列表中取得一项
- bufadd() 给 buffer 列表增加文件
- bufexists() 检查 buffer 是否存在
- buflisted() 检查 buffer 是否存在并在列表内
- bufload() 确保 buffer 已加载
- bufloaded() 检查 buffer 是否存在并已加载
- bufname() 取得某 buffer 名
- bufnr() 取得某 buffer 号
- tabpagebuflist() 得到标签页里的 buffer 列表
- tabpagenr() 得到标签页号
- tabpagewinnr() 类似于特定标签页里的 winnr()
- winnr() 取得当前窗口的窗口号
- bufwinid() 取得某 buffer 的窗口 ID
- bufwinnr() 取得某 buffer 的窗口号
- winbufnr() 取得某窗口的 buffer 号
- listener_add() 新增回调来监听改动
- listener_flush() 调用监听器回调
- listener_remove() 删除监听器回调
- win_findbuf() 寻找包括某 buffer 的窗口
- win_getid() 取得窗口的窗口 ID
- win_gotoid() 转到指定 ID 的窗口
- win_id2tabwin() 给出窗口 ID 获取标签页号和窗口号
- win_id2win() 把窗口 ID 转换为窗口号
- getbufinfo() 获取 buffer 信息的列表
- gettabinfo() 获取标签页信息的列表
- getwininfo() 获取窗口信息的列表
- getchangelist() 获取改变列表项目的列表
- getjumplist() 获取跳转列表项目的列表
- swapinfo() 关于交换文件的信息
- swapname() 取得 buffer 的交换文件路径

# 命令行

- getcmdline() 得到当前命令行
- getcmdpos() 得到命令行里的光标位置
- setcmdpos() 设置命令行里的光标位置
- getcmdtype() 得到当前命令行的类型
- getcmdwintype() 返回当前命令行窗口类型
- getcompletion() 命令行补全匹配的列表

# 快速修复和位置列表

- getqflist() 快速修复错误的列表
- setqflist() 修改快速修复列表
- getloclist() 位置列表项目的列表
- setloclist() 修改位置列表

# 插入模式 补全

- complete() 设定要寻找的匹配
- complete_add() 加入要寻找的匹配
- complete_check() 检查补全是否被中止
- complete_info() 取得当前补全的信息
- pumvisible() 检查弹出菜单是否显示
- pum_getpos() 如果可见，弹出菜单的位置及大小

# 折叠

- foldclosed() 检查某一行是否被折叠起来
- foldclosedend() 类似 foldclosed() 但同时返回最后一行
- foldlevel() 检查某行的折叠级别
- foldtext() 产生折叠关闭时所显示的行
- foldtextresult() 得到关闭折叠显示的文本

# 语法和高亮

- clearmatches() 清除 matchadd() 和 :match 诸命令定义的所有 匹配
- getmatches() 得到 matchadd() 和 :match 诸命令定义的所有 匹配
- hlexists() 检查高亮组是否存在
- hlID() 取得高亮组标示
- synID() 取得某位置的语法标示
- synIDattr() 取得某语法标示的特定属性
- synIDtrans() 取得翻译后的语法标示
- synstack() 取得指定位置的语法标示的列表
- synconcealed() 取得和隐藏 (conceal) 相关的信息
- diff_hlID() 得到比较模式某个位置的高亮标示
- matchadd() 定义要高亮的模式 (一个 "匹配")
- matchaddpos() 定义要高亮的位置列表
- matcharg() 得到 :match 参数的相关信息
- matchdelete() 删除 matchadd() 或 :match 诸命令定义的匹配
- setmatches() 恢复 getmatches() 保存的匹配列表

# 拼写



- spellbadword() 定位光标所在或之后的错误拼写的单词
- spellsuggest() 返回建议的拼写校正列表
- soundfold() 返回 "发音相似" 的单词等价形式

# 历史记录

- histadd() 在历史记录中加入一项
- histdel() 从历史记录中删除一项
- histget() 从历史记录中提取一项
- histnr() 取得某历史记录的最大索引号

# 交互

- browse() 显示文件查找器
- browsedir() 显示目录查找器
- confirm() 让用户作出选择
- getchar() 从用户那里取得一个字符输入
- getcharmod() 取得最近键入字符的修饰符
- getmousepos() 取得最近已知的鼠标位置
- feedkeys() 把字符放到预输入队列中
- input() 从用户那里取得一行输入
- inputlist() 让用户从列表里选择一个项目
- inputsecret() 从用户那里取得一行输入，不回显
- inputdialog() 从用户那里取得一行输入，使用对话框
- inputsave() 保存和清除预输入 (typeahead)
- inputrestore() 恢复预输入 (译注: 参阅 input())

# GUI



- getfontname() 得到当前使用的字体名
- getwinpos() Vim 窗口的位置
- getwinposx() Vim 窗口的 X 位置
- getwinposy() Vim 窗口的 Y 位置
- balloon_show() 设置气泡的内容
- balloon_split() 分割消息用于气泡的显示
- balloon_gettext() 取得气泡中的文本

# vim 服务器

- serverlist() 返回服务器列表
- remote_startserver() 启动服务器
- remote_send() 向 Vim 服务器发送字符命令
- remote_expr() 在 Vim 服务器内对一个表达式求值
- server2client() 向一个服务器客户发送应答
- remote_peek() 检查一个服务器是否已经应答
- remote_read() 从一个服务器读取应答
- foreground() 将一个 Vim 窗口移至前台
- remote_foreground() 将一个 Vim 服务器窗口移至前台

# 窗口大小和位置



- winheight() 取得某窗口的高度
- winwidth() 取得某窗口的宽度
- win_screenpos() 取得某窗口的屏幕位置
- winlayout() 取得标签页中窗口的布局
- winrestcmd() 恢复窗口大小的返回命令
- winsaveview() 得到当前窗口的视图
- winrestview() 恢复保存的当前窗口的视图

# 映射

- hasmapto() 检查映射是否存在
- mapcheck() 检查匹配的映射是否存在
- maparg() 取得映射的右部 (rhs)
- wildmenumode() 检查 wildmode 是否激活

# 测试



- assert_equal() 断言两个表达式的值相等
- assert_equalfile() 断言两个文件的内容相同
- assert_notequal() 断言两个表达式的值不等
- assert_inrange() 断言表达式在范围内
- assert_match() 断言模式与值匹配
- assert_notmatch() 断言模式不与值匹配
- assert_false() 断言表达式为假
- assert_true() 断言表达式为真
- assert_exception() 断言命令抛出例外
- assert_beeps() 断言命令会响铃
- assert_fails() 断言命令会失败
- assert_report() 报告测试失败
- test*alloc*fail() 使内存分配失败
- test_autochdir() 启动时打开 'autochdir'
- test_override() 测试 Vim 内部的覆盖
- test*garbagecollect*now() 立即清理内存
- test_getvalue() 取得内部变量的值
- test*ignore*error() 忽略指定的错误信息
- test*null*blob() 返回 null blob
- test*null*channel() 返回 null 通道
- test*null*dict() 返回 null 字典
- test*null*job() 返回 null 作业
- test*null*list() 返回 null 列表
- test*null*partial() 返回 null 偏函数
- test*null*string() 返回 null 字符串
- test_settime() 设置 Vim 内部使用的时间
- test_setmouse() 设置鼠标位置
- test_feedinput() 给输入 buffer 加入键序列
- test*option*not_set() 复位指定选项已设的标志位
- test_scrollbar() 模拟 GUI 中的滚动条动作

# 进程通信



- ch_canread() 检查是否有可读的内容
- ch_open() 打开通道
- ch_close() 关闭通道
- ch*close*in() 关闭通道的 in 部分
- ch_read() 从通道读取信息
- ch_readblob() 从通道读取 blob
- ch_readraw() 从通道读取未处理的信息
- ch_sendexpr() 从通道读取 JSON 信息
- ch_sendraw() 向通道发送未处理的信息
- ch_evalexpr() 通过通道计算表达式
- ch_evalraw() 通过通道计算未经处理的表达式
- ch_status() 获取通道的状态
- ch_getbufnr() 获取通道的 buffer 号
- ch_getjob() 获取通道相关的作业
- ch_info() 获取通道信息
- ch_log() 在通道日志文件写下信息
- ch_logfile() 设置通道日志文件
- ch_setoptions() 设置通道的选项
- json_encode() 把表达式编码为 JSON 字符串
- json_decode() 把 JSON 字符串解码为 Vim 类型
- js_encode() 把表达式编码为 JSON 字符串
- js_decode() 把 JSON 字符串解码为 Vim 类型

# 作业



- job_start() 启动作业
- job_stop() 停止作业
- job_status() 获取作业状态
- job_getchannel() 获取作业使用的通道
- job_info() 获取作业信息
- job_setoptions() 设置作业选项

# 标号

- sign_define() 定义或更新标号
- sign_getdefined() 取得已定义的标号列表
- sign_getplaced() 取得已放置的标号列表
- sign_jump() 跳转到标号
- sign_place() 放置标号
- sign_placelist() 放置一列标号
- sign_undefine() 删除标号的定义
- sign_unplace() 撤销标号的放置
- sign_unplacelist() 撤销一列标号的放置

# 终端窗口



- term_start() 打开终端窗口并运行作业
- term_list() 获取终端 buffer 的列表
- term_sendkeys() 给终端发送键击
- term_wait() 等待屏幕刷新
- term_getjob() 获取终端相关联的作业
- term_scrape() 获取终端屏幕的行
- term_getline() 获取终端的一行文本行
- term_getattr() 获取属性 {what} 的值
- term_getcursor() 获取终端的光标位置
- term_getscrolled() 获取终端的滚动行数
- term_getaltscreen() 获取轮换屏幕标志位
- term_getsize() 获取终端大小
- term_getstatus() 获取终端状态
- term_gettitle() 获取终端标题
- term_gettty() 获取终端 tty 名
- term_setansicolors() 设置 GUI 使用的 16 种 ANSI 颜色
- term_getansicolors() 获取 GUI 使用的 16 种 ANSI 颜色
- term_dumpdiff() 显示两份屏幕截图的差异
- term_dumpload() 在窗口中载入终端屏幕截图
- term_dumpwrite() 把终端屏幕的内容写入文件
- term_setkill() 设置停止终端中的作业的信号
- term_setrestore() 设置恢复终端的命令
- term_setsize() 设置终端的大小

# 弹出窗口



- popup_create() 在屏幕中央创建弹出
- popup_atcursor() 在光标位置正上方创建弹出，光标移开时关闭
- popup*beval() 在 v:beval* 变量指定的位置，光标移开时关闭
- popup_notification() 用三秒钟显示通知
- popup_dialog() 创建带填充和边框中间对齐的弹出
- popup_menu() 提示从列表中选择一个项目
- popup_hide() 临时隐藏弹出
- popup_show() 显示之前隐藏的弹出
- popup_move() 改变弹出的位置和大小
- popup_setoptions() 覆盖弹出的选项
- popup_settext() 替换弹出 buffer 的内容
- popup_close() 关闭一个弹出
- popup_clear() 关闭所有弹出
- popup*filter*menu() 从一列项目中选择
- popup*filter*yesno() 等待直到按了 'y' 或 'n' 为止
- popup_getoptions() 取得弹出的当前选项
- popup_getpos() 取得弹出的实际位置和大小

# 定时器



- timer_start() 建立定时器
- timer_pause() 暂停或继续定时器
- timer_stop() 停止定时器
- timer_stopall() 停止所有定时器
- timer_info() 获取定时器信息

# tags

- taglist() 得到匹配标签的列表
- tagfiles() 得到标签文件的列表
- gettagstack() 得到窗口的标签栈
- settagstack() 修改窗口的标签栈

# 提示 buffer 

- prompt_setcallback() 设置 buffer 的提示回调
- prompt_setinterrupt() 设置 buffer 的中断回调
- prompt_setprompt() 设置 buffer 的提示文本

# 杂项



- mode() 取得当前编辑状态
- visualmode() 最近一次使用过的可视模式
- exists() 检查变量，函数等是否存在
- has() 检查 Vim 是否支持某特性
- changenr() 返回最近的改变号
- cscope_connection() 检查有无与 cscope 的连接
- did_filetype() 检查某文件类型自动命令是否已经使用
- eventhandler() 检查是否在一个事件处理程序内
- getpid() 得到 Vim 的进程号
- libcall() 调用一个外部库函数
- libcallnr() 同上，但返回一个数值
- undofile() 得到撤销文件名
- undotree() 返回撤销树的状态
- getreg() 取得寄存器内容
- getregtype() 取得寄存器类型
- setreg() 设定寄存器内容及类型
- reg_executing() 取得正在执行中的寄存器名
- reg_recording() 取得正在记录中的突破口名
- shiftwidth() 'shiftwidth' 的有效值
- wordcount() 获取 buffer 的字节/单词/字符计数
- luaeval() 计算 Lua 表达式
- mzeval() 计算 MzScheme 表达式
- perleval() 计算 Perl 表达式 ( +perl )
- py3eval() 计算 Python 表达式 ( +python3 )
- pyeval() 计算 Python 表达式 ( +python )
- pyxeval() 计算 python_x 表达式
- debugbreak() 中断正在调试的程序