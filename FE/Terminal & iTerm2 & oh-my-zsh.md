## 前言
在正式介绍iTerm2之前，需要我们了解Mac自带的终端Terminal，所以本文开始会先介绍Terminal的命令。其实自带的终端已经是很好要的命令行工具了，不过ITerm2是自动终端的增强版，可以自定义很多功能，包括主题、字体、快捷键等等，只要开始使用ITerm2，保证你会抛弃自带的Terminal。
***
## 正文
正文主要记录以下几点：	

1. Terminal的个性化设置
      * 最高权限免密码
      * 自定义命令行前缀
      * alias快捷键设置
2. Terminal的基本指令
      * su/sudo
      * 快捷键
      * 核心命令
      * 链式命名
      * 历史命令
      * 文件管理
      * 文件夹管理
      * 查找
      * ftp
      * 帮助
      * 进程
      * 其他
3. ITerm2
      * 安装
      * 配置
      * 快捷键
      * 主题
4. Oh My Zsh
      * 安装
      * 设置默认shell
      * 主题
      * 字体
      * 高亮
      * 自动填充
      * 自定义
***
#### 1. Terminal的个性化设置
其实每个人在使用终端时，都会对终端做一点个性的设置，以便用起来或看起来比较顺手和顺眼，下面是几个比较常用和基础的设置：	
`ps`: *以下内容都会使用`vim`编译器，如果不是很熟悉可以移步鄙人整理的这篇文章——[vim](https://www.jianshu.com/p/cd3944eac5c7)*

* **最高权限免密码**
当对自己电脑做一下个性化设置的时候，避免不了需要使用最高root权限（`sudo、su -`等），这时候即使只是输入一行命令也需要输入两遍root用户的密码。有些人觉得没什么，认为这样更安全；有些人就会认为这样太麻烦，希望在输入这些命令的时候，不用确认密码。下面就是这对这一需求的设置步骤（[参考](http://www.cnblogs.com/itech/archive/2009/08/07/1541017.html)）：
  * sudo免密码
    1. 切换到root权限
     ```
    sudo -s
    // or
    su -    // 多加"-"会把root的环境变量也一起带过来
    ``` 
    2. 进入sudoers文件
    ```
    visodo
    // or
    vim /etc/sudoers
    ```
    3. 添加新用户
    找到`root ALL=(ALL) ALL`，在其下面加上` your_name ALL=(ALL) ALL`
    4. 设置免密
    添加用户后，默认5分钟后需要再次输入密码，可以给用户设置免密，修改刚才的命令为：`your_name ALL=(ALL) NOPASSWD:ALL`。
    如果需要指定命令可以使用的话，修改刚才的命令为：`your_name ALL=(root) NOPASSWD:/sbin/mount,(root) NOPASSWD:/bin/chown`。
`ps`：有时设置不成功是因为被`%admin`的group给覆盖了，所以把用户设置在这行之后，或者给这个group也添加nopasswd行为。最后设置如下图：
![设置截图](https://upload-images.jianshu.io/upload_images/2317192-4b697e1dc5737dc8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  * su免密码
    1. 切换到root权限：同上
    2. 创建group为wheel：`groupadd wheel`
    3. 将用户加入wheel group中：`usermod -G wheel youer_name`
    4. 修改su的配置文件：`vim /etc/pam.d/su`，添加下面项：
    
    ```
    auth       required   pam_wheel.so group=wheel 
    # Uncomment this if you want wheel members to be able to
    # su without a password.
    auth       sufficient pam_wheel.so trust use_uid
    ```
    至此在使用`su`命令是也不需要密码了。
    `ps`：在进入root权限时，需要使用命令`su -`，不然环境变量不能带进来就设置不成功了。    
***
* **自定义命令行前缀**
在我们打开终端时，书写每一命令行前面都有一个很长的前缀，看起来及碍眼，截图时又会暴露一些不想别人看到的信息，所以这时需要一种这样的操作，自定义个性化前缀：  
  1. 设置前    
![设置前](https://upload-images.jianshu.io/upload_images/2317192-bb3a8ba1d073c483.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  2. 编辑系统文件：`sudo vim /etc/bashrc`
  3. 注释原来的设置：`#PS1='\h:\W \u\$ '`留作备份；在其下方自定义设置：`PS1='\W \$ '`:
![自定义](https://upload-images.jianshu.io/upload_images/2317192-7f7cfcc12d9d3f44.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  4. 强制保存、退出、重启终端：`:wq!`
![设置后](https://upload-images.jianshu.io/upload_images/2317192-eb61e6568f075020.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
***
* **alias/快捷键/自定义命令设置**
很多时候，当我们输入一些系统指令的时候，比方清除显示信息需要输入`clear`，或者其他指令时，需要输入很长一段。都说程序员是“越懒越好”，那么怎么避免输入长指令也可以实现同样的功能呢，下面介绍一下`alias指令`：
  1. 介绍       
      * **alias命令**是用来设置指令的别名，可以将一些较长的命令进行简化；
      * 使用alias设置时，必须用`''`将原来的命令引起来，防止特殊字符导致错误；
      * 正常使用alias命令设置指令的时候只局限当次登入的操作，如果要每次登入都能使用这些命令别名，则需要将相应的alias命名设置放到bash的初始化文件`/etc/bashrc`中；如果加在~/.bashrc中时，只会对当前用户生效。
      * 或者把相应的指令设置编辑在环境变量文件`.bash_profile`中
  2. 语法
      * alias (选项) (参数)
      * 基本设置方法：`alias 新的命令='原命令 -选项/参数'`
      * 查看当前系统已设置别名：`alias -p`
      ![查看系统设置](https://upload-images.jianshu.io/upload_images/2317192-03a0df8a678ffcac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
     * 删除别名：`unalias 别名`
  3. `/etc/bashrc`设置
      * 打开系统文件：`sudo vim /etc/bashrc`
      * 加上相应的指令，强制保存、退出即可
  4. `.bash_profile`设置
      * 打开环境变量文件：`sudo vim .bash_profile`
      * 加上相应的指令别名，保存、退出
      * 加载环境文件：`source .bash_profile`
***
#### 2. Terminal基本指令
作为一名程序员，掌握基本的终端指令是一项基本技能，下面罗列一些基本的指令：

* **su**：全称为**switch user**，表示切换用户身份，使得用户可以在shell中以其他身份运行程序
    *  `su - (root)`：*root*是默认用户，可以是其他用户；不加任何参数时，默认切换到*root*用户，但是没有把*root*用户的**环境变量**切换到当前用户下；加上`-`就可以同时携带*root***环境变量**一起切换
    * 其他命令格式：`su [-fmp] [-c command] [-s shell] [--help] [--version] [-] [USER[ARG]]`
    `-f`、`-fast`：不必读启动文件（如csh、cshrc等），仅用于csh或tcsh两种shell
    `-l`、`-login`：类似重新登录一样
    `-m`、`-p`、`-preserve-environment`：执行su时不改变环境变量
    `-c command`：变更账号为指定user，并执行指令（command）后再变回原来的使用者
    `--help`：显示说明文件
    `--version`：显示版本资讯
    `user`：欲变更的使用者名称
    `ARG`：传入新的shell参数
***
* **sudo**：可以让普通用户具有临时使用root权限的权利，只需要输入自己账户的密码即可，前提是需要把用户加入到`/etc/sudoers`配置文件中，见上面**最高权限免密码**
  * 运行指令：`sudo [option]`，可使用`sudo -h`查询所有指令，如图
![所有指令](https://upload-images.jianshu.io/upload_images/2317192-5ac50eab253a5d14.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  * 几个基本指令：
`sudo -h`：显示指令的使用方法
`sudo -l`：显示当前用户的权限
`sudo -b`：在后台执行指令
`sudo -s`：执行环节变量中SHELL中所指定的shell，或是/etc/passwd里指定的shell
`sudo -v`：如果没有设置免密，用户在输入一次密码后，会有一个N分钟的限制，下次再使用sudo时需要再次输入密码，而这个命令就是更新、重置一下这个时间戳

`ps`：`su`是提权，`sudo`是借权。以上内容[参考](https://blog.csdn.net/crave_shy/article/details/23037787)
***

* **快捷键**：针对当前命令行或当前tab的一些操作		
`Ctrl + a`：**ahead** 把光标移到命令行最前面		
`Ctrl + e`：**end** 把光标移到命令行最后面		
`Ctrl + u`：**cut** 剪切光标之前的文案		
`Ctrl + k`：**kill** 剪切光标之后的文案		
`Ctrl + w`：**word** 向后/backward剪切一个单词		
`Ctrl + y`：**yank** 复制之前剪切的内容 *u/k/w*		
`Ctrl + h`：和**后退**相同，倒退一步（删除一个字符）		
`Ctrl + c`：强制终止当前运行任务		
`Ctrl + z`：暂停当前运作任务，被挂起，可以使用`fg/bg`重新启动		
`jobs`：显示当前暂停的进程		
`Ctrl + d`：退出当前shell，如果没有输入也没运行指令时；类似`command + w`，不管是否允许任务		
`Ctrl + _`：即`Ctrl + shift + minus`，删除最近的一个指令		
`Ctrl + t`：切换光标前的两个字符		
`Ctrl + f`：光标向右移动一个字符，等同`向右箭头`		
`Ctrl + b`：光标向左移动一个字符，等同`向左箭头`		
`Ctrl + l`：清除当前shell显示的内容，等同于`clear`、`command + k`		
`Ctrl + r`：查找历史记录的指令		
`Ctrl + s`：**suspend** 推迟输出		
`Ctrl + q`：继续输出		
`Ctrl + o`：放弃输出		
`Esc + f`：光标向右移到单词最后		
`Esc + b`：光标向左移到单词最前		
`Esc + t`：切换光标前的两个单词		
`tab`：自动补齐文件、文件夹的名称		
***
* **核心命令**	
`cd`：当前用户主目录		
`cd [folder]`：转到指定目录		
`cd /`：根目录	
`cd -`：返回之前的目录	
`ls [路径]`：显示当前目录下内容（简单）		
`ls -l`：**long** 显示当前目录下内容（详细）	
`ls -a`：**all** 显示当前目录下内容（包括隐藏文件）	
`ls -lh`：显示当前目录下内容（详细，把文件大小转换单位）	
`ls -R`：逐条显示文件夹中全部内容	
`open [file]`：打开指定文件	
`top`：显示活动中的任务进程，按`q`退出	
`nano [file]`：用nano编辑器打开文件	
`vim [file]`：用vim编译器打开文件	
`clear`：清屏		
`reset`：重启终端显示
***
* **链式命令**	
`[command a];[command b]`：不管a是否成功，运行完a后运行b	
`[command a]&&[command b]`：如果a成功后运行b		
`[command a]|[command b]`：运行a，并把a的结果传入命令b，e.g `ps auxwww | grep goole`		
`command a &`：后台运行a，挂起运行
***
* **历史命令**		
`history n`：展示最近n条输入的指令		
`Ctrl + r`：搜索最近输入的指令		
`![value]`：执行最近输入以value开始的指令		
`!!`：执行最近输入的指令		
*** 
* **文件管理**		
`touch [file]`：创建文件		
`pwd`：显示当前目录全路径		
`.`、`ls .`：当前文件夹内容		
`..`、`ls ..`：父文件夹内容		
`cat [file]`：**concatenate** 在shell屏幕上显示文件内容		
`less [file]`：按屏幕大小分段展示文件内容，按`y`往上翻；`?`往回搜寻要找的单字		
`more [file]`：一页一页显示内容，按`/`搜寻查找单字		
`rm [-irf] [file]`：删除指定文件；`i`：** interactive**询问 删除前确认，`r`：删除文件及文件夹，`f`：强制永久删除，		
`cp [file] [newfile]`：复制一个文件生成一个新文件		
`cp [file] [dir]`：复制一个文件生成一个新文件到指定文件夹		
`mv [file] [new filename]`：移动或重命名一个文件		
`pbcopy < [file]`：复制一个文件内容到剪切板		
`pbpaste`：粘贴剪切板内容		
`pbpaste > [file]`：粘贴剪切板内容到指定文件中		
***
* **文件夹管理**		
`mkdir [dir]`：创建文件夹			
`mkdir [-pmv] [dir]/[dir]`：链式创建文件夹；`-p`：**--parent** 如果上级目录不存在，一并创建；`-m`：**--mode** 设定权限模式（类似chmod）;`-v`：**--verbose** 每次创建目录都显示信息		
`rmdir [dir]`：删除`空`文件夹		
`chdir [dir]`：将当前目录移到指定目录				
`[command] > [file]`：把命令执行输出写入文件，注意超出		
`[command] >> [file]`：把命令执行输出写入已存在文件		
`[command] < [file]`：执行命令前读取指定文件内容		
***
* **查找**		
`find [dir] -name [search_pattern]`：指定目录下查找指定文件名的文件		
`grep [search_pattern] [file]`：查找指定文件中指定内容的所有行		
`grep [-rnv] -f patttern_file [search_pattern] [file]`：		
`-r`：循环查找不包含指定内容的所有行；		
`-v`：查找不包含指定内容的所有行；		
`-n`：把找到的行加上行号；		
`-A`：除了显示符合范本样式的那一行之外，并显示该行之后的内容		
`-b`：显示符合范本样式的那一行之外，并显示该行之前的内容		
`-c`：计算符合范本样式的列数		
`-e`：指定字符串作为查找文件内容的范本样式			
`-E`：使用扩展正则表达式，等同于`egrep + 正则`		
`-f`：以pattern_file为范本，让grep搜寻文件内容		
`-F`：将范本样式视为固定字符串的列表		
`-h`：在显示符合范本样式的那一列之前，不标示该列的文件名称		
`-H`：在显示符合范本样式的那一列之前，标示该列的文件名称		
`-i`、`y`：忽略字符大小写的差别		
`-l`：列出文件内容符合指定的范本样式的文件名称		
`-L`：列出文件内容不符合指定的范本样式的文件名称		
`-s`：不显示错误信息		
`-w`：只显示全字符合的列		
`-x`：只显示全列符合的列		
`-o`：只输出文件中匹配到的部分		
`ps`：可[参考](http://man.linuxde.net/grep)		
***
* **ftp**：**File Transfer Program**，网络档案传输程序				
`ftp [hostname] [ip-address]`：`hostname`：域名地址、`ip-address`：ip地址		
`ascii`：将传输模式设为 ascii 模式，通常用於传送文字档		
`binary`：将传输模式设为 binary 模式，通常用於传送执行档，压缩档与影像档等		
`cd remote-dir`：改变remote host上的工作目录		
`lcd [dir]`：更改local host的工作目录		
`ls [remote-dir] [loacl-file]`：列出remote host上的文档		
`get [remote-file] [local-file]`：获取远程的文档		
`mget [remote-files]`：使用通用字元一次取多个文档		
`put [local-file] [remote-file]`：将local host的文档传到remote host		
`mput [local-files]`：使用通用字元一次传送多个文档到remote host		
`mkdir [dir-name]`：在remote host创建目录		
`prompt`：更改交谈模式，若为on则在`mput`与`mget`传送文档是都会询问		
`quit/bye`：退出ftp		
`help [command]`：线上辅助指令		
`ps`：可[参考](https://blog.csdn.net/u013850277/article/details/56486370)		
***
* **帮助**		
`[command -h]`、`[command --help]`、`info [command]`：提供帮助信息		
`man [command]`：展示帮助列表		
`whatis [command]`：一句话描述指令含义		
`which [command]`: 查找指定命令的目录（执行路径）		
`apropos [search-patterm]`：通过描述的关键字查找相应的指令		
***
* **进程**		
`ps [-auxw]`：显示process的状态；`-a`：列出包括其他users的process、`-u`：显示定向user的process、`-x`：显示包括没有Terminal控制的process、`-w`：使用较宽的模式显示process的状况		
`kill [-signal] pid`：送一个signal给某一个process；`-signal`：为一个`0~31`的数字，其中`9`是sigkill，用于杀死一个进程；可以使用`kill -l`查看相应的数目字，`man kill`查看详细情况		
***
* **其他**				
`shutdown [-hr] [time]`：关机、重启；`-h`：指定时间；`-r`：重启；`time`：时间，`now`、`+10`、`20:00`		
`kill -9 [pid]`：杀死指定pid的进程		
`passwd [user]`：更改指定用户的密码		
`chmod [-fR] mode [file]`：**change mode** `-f`：**force** chmod或略失败的动作、`-R`：**recurive** 所在子树下的所有目录及文档都改成指定的模式；`mode`：一个三位或四位的八进制数字，表示对象的存取权		
`echo`：显示一串字符在终端上；`-n`：显示完之后不会有跳行的动作		
`SSH`：远程访问`ssh username@site.com`		

`ps`：以上内容[参考1](https://blog.csdn.net/lcl12111211/article/details/62890847)、[参考2](https://blog.csdn.net/xueer8835/article/details/7942990)
***
#### 3. ITerm2
先来介绍一下什么是iTerm2，用官方的解释就是：   
> iTerm2是继承了iTerm的Terminal的替代品。它需要在macOS 10.10或更新的版本上运行。iTerm2因为其有着一些你想要却不知道的优化把Terminal带到了新纪元。             
 
下面主要是基于[官网](https://www.iterm2.com/)学习并记录的自己的一些设置：
* **安装**
  其实有两种方式按钮iTerm2：
  1. 直接从[官网](https://www.iterm2.com/downloads.html)下载安装包
  2. 使用Homebrew下载安装：`brew install iTerm2`   

`ps`：Homebrew可以参考我的`Mac备忘录`中`环境搭建`部分
***
* **配置**
  iTerm2提供了很多特性，可以参考官网自行设置，这只备注了一些鄙人自己设置的功能：
  * **Hotkey**： 很多情况当我们在操作其他应用时，希望快速调出终端到操控区域，iTerm2提供了这一优化设置
    1. `command + ,`：打开ITerm2的performances
    2. 设置Hotkey为：`option + space`（按个人喜好）如下图：    
    ![热键设置](https://upload-images.jianshu.io/upload_images/2317192-050a501774ab1b91.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  * **快捷键**：
    * `⌘ + t`：打开一个新的标签
    * `⌘ + w`：关闭一个标签
    * `⌘ + →`、`⌘ + ←`、`⌘ + }`、`⌘ + {`、`⌘ + <number>`、：切换标签
    * `⌘ + d`：水平切分屏幕（pane）
    * `⌘ + shift + d`：垂直切分屏幕（pane）
    * `⌘ + [`、`⌘ + ]`、`⌘ + option + 方向键`、：切换切分的屏幕（pane）
    * `⌘ + enter`：进入与返回全屏模式
    * `⌘ + ;`：输入命令时弹出自动补齐窗口
    * `⌘ + shift + ;`：输入命令时弹出历史命令记录窗口
    * `⌘ + shift + H`：输入命令时弹出历史粘贴记录窗口
    * `⌘ + option + b`：历史命令操作回放
    * `⌘ + option + e`：iTerm2 Expose
    * `⌘ + f`：查找，支持正则查找
    * `tab`、`shift + tab`：在查找状态时，向右、向左选择查找内容
    * `option + enter`：在查找状态时，复制粘贴选择内容到当前命名行
***
* **主题**
使用iTerm2的除了其各种特性，当然少不了自定义主题了，这里备注两个主题，一个是网上推荐最多的开源主题`Solarized + Zsh`，还有一个是自己自定义的主题
  * **Solarized**：有自己的[官网](http://ethanschoonover.com/solarized)
    1. 到官网[下载](http://ethanschoonover.com/solarized/files/solarized.zip)主题压缩包
    2. 解压下载，在iTerm2中导入主题文件，解压文件夹`solarized/iterm2-colors-solarized`下的`Solarized Dark.itermcolors`文件。操作路径：`⌘ + ,` → `Profiles` → `Colors` → `Color Presets` → `Improt`，导入成功后选择`Solarized Dark`，如图：
    ![导入主题](https://upload-images.jianshu.io/upload_images/2317192-b9ce37668592f651.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
***
#### 4. Oh My Zsh：[GitHub]([https://github.com/robbyrussell/oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)地址
				
* 安装：`sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"`		
* 设置Zsh为当前用户默认Shell：`chsh -s /bin/zsh`		
      使用`cat /etc/shells`查看系统默认安装了哪些shells，可以使用上面的命令切换不同shell
* 修改主题：`vim ~/.zshrc` 修改主题配置为`ZSH_THEME="agnoster"`，默认为`robbyrussell`，[主题列表]([https://github.com/robbyrussell/oh-my-zsh/wiki/themes](https://github.com/robbyrussell/oh-my-zsh/wiki/themes)
)，修改如图
      ![主题修改](https://upload-images.jianshu.io/upload_images/2317192-d1dfc5de18df9d8d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 安装PowerLine：
      ```
      sudo easy_install pip    // 如果安装可或略
      pip install powerline-status --user
      ```
 * 安装PowerFonts
      先创建一个空文件夹，然后再在这个文件夹中clone、安装：
      ```
      # git clone
      git clone https://github.com/powerline/fonts.git --depth=1
      # cd to folder
      cd fonts
      # run install shell
      ./install.sh
      ```
 * 配置Meslo字体：`⌘ + ,` →  `Profiles` →  `Text` →  `Font` →  `Change Font` →   如图：
      ![配置字体](https://upload-images.jianshu.io/upload_images/2317192-65baa4c097538a43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 声明高亮：
          1. 安装：`brew install zsh-syntax-highlighting`
          2. 配置：`vim ~/.zshrc`，
在最后一行添加：`source /usr/local/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh`
* 自动填充：
        1. 克隆`zsh-autosuggestions`项目到指定目录：`git clone https://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions`
         2. 添加插件：`vim ~/.zshrc`，找到`plugins`，增加`zsh-autosuggestions`，如图：
          ![插件](https://upload-images.jianshu.io/upload_images/2317192-9c47768a7ce63823.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
         3. 修改填充字体颜色`586e75 `：如图
          ![填充颜色](https://upload-images.jianshu.io/upload_images/2317192-e3f23f9bbc8e192c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

      * 隐藏用户和主机名：
      编辑：`vim ~/.oh-my-zsh/themes/agnoster.zsh-theme`，注释掉`prompt_context`，如图：
      ![隐藏名称](https://upload-images.jianshu.io/upload_images/2317192-70870ba05b2d37a3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
      * 更换背景图片：iTerm2 -> Preferences -> Profiles -> Window -> BackGround Image，选择自己喜欢的图片     
* **自定义**：
  这里只截一个图，便于之后选取颜色（其他设置同步Solarized），尽管可以把profile的相关设置拷贝成JSON并保存，但是这样可以直观的看到：
     ![自定义](https://upload-images.jianshu.io/upload_images/2317192-f0476e9a1dd62b50.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)            

`ps`：以上除了参考[官网](https://www.iterm2.com/)，也参照了[这篇文章](https://www.cnblogs.com/xishuai/p/mac-iterm2.html)              
***
## 小结
首先感谢以上链接参考文章的作者，感谢文章提供的启发与帮助！
再者以上是鄙人学习、配置Terminal的记录，主要是为了之后换电脑等情况方便查找恢复，如果能给到看官一点帮助，那真是莫大的荣幸！
