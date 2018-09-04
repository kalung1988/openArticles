## 前言
首先引用[官网](www.vim.org)的介绍：
>vim是一款功能强大、支持各种插件、配置极为灵活的编辑器，且支持多种主流OS(linux、Unix、mac、windows)，可用来各种编程预言的coding和文件编辑，用习惯了vim，你会体会到它是如此的高效和便捷。

而这篇文章主要记录鄙人学习、配置vim的一些知识点，以便后期查询
***
## 正文
正文主要记录以下几点：
1. 安装 
    * 命名安装
    * 下载安装
2. 初始化配置
    * 主要命令参数
3. 插件
    * 安装
    * 配置插件
4. vim常用指令
    * 移动
    * 搜索
    * 删除、复制、粘贴
    * 编辑模式
    * 指令模式
***
#### 1. 安装
在安装之前，需要确认一下本机中有没有安装过vim：直接`vim`或者`which vim`查看，如果没有就需要安装了。
* **命令安装**
由于mac默认自带vim，所以这里介绍几个其他系统的安装方式
```
sudo apt-get install vim   // ubuntu
yum install vim     // redHat/Fedora/CentOS
```
***
* **下载安装**
由于mac自带vim，并且之后可能会涉及到升级vim，这里介绍一种压缩包安装方法
  1. 到git上下载压缩包：[GitHub地址](https://github.com/vim/vim/releases)
  2. 解压：`tar xvzf vim-8.0.0004.tar.gz`，这里以版本8为例
  3. 编译参数：`./configure --disable-selinux --enable-cscope`
  `注`：`./configure -help`查看参数选项含义
  4. 编译：`make`
  5. 安装：`sudo make install`
  6. 新手指南：`vimtutor `

`ps`：以上[参考](https://www.cnblogs.com/qiyuexin/p/6398606.html)
***
#### 2. 初始化配置
linux环境下vim的初始化配置文件为.vimrc, 通常有两个:系统版本和用户版本，前者不同发行版linux会有不同，一般位于/etc/vimrc，是整个系统vim的默认配置；后者位于~/.vimrc，是当前用户的vim配置，会覆盖系统配置。一般把配置写入后者，如果没有此文件，则需要自己创建：`touch ~/.vimrc`

* **主要命令参数**

```
"主题设置"
syntax enable
"打开语法高亮"
syntax on
"使用配色方案 solarized"
set background=dark
colorscheme desert   
"打开文件类型检测功能"
filetype on
"不同文件类型采用不同缩进"
filetype indent on
"允许使用插件"
filetype plugin on
filetype plugin indent on
"关闭vi模式"
set nocp
"与windows共享剪贴板"
set clipboard+=unnamed
"取消VI兼容，VI键盘模式不易用"
set nocompatible
"显示行号, 或set number"
set nu
"历史命令保存行数"
set history=100 
"当文件被外部改变时自动读取"
set autoread 
"取消自动备份及产生swp文件"
set nobackup
set nowb
set noswapfile
"允许使用鼠标点击定位"
set mouse=a
"允许区域选择"
set selection=exclusive
set selectmode=mouse,key
"高亮光标所在行"
set cursorline
"取消光标闪烁"
set novisualbell
"总是显示状态行"
set laststatus=2
"状态栏显示当前执行的命令"
set showcmd
"标尺功能，显示当前光标所在行列号"
set ruler
"设置命令行高度为3"
set cmdheight=3
"粘贴时保持格式"
set paste
"高亮显示匹配的括号"
set showmatch
"在搜索的时候忽略大小写"
set ignorecase
"高亮被搜索的句子"
set hlsearch
"在搜索时，输入的词句的逐字符高亮（类似firefox的搜索）"
set incsearch
"继承前一行的缩进方式，特别适用于多行注释"
set autoindent
"为C程序提供自动缩进"
set smartindent
"使用C样式的缩进"
set cindent
"制表符为2"
set tabstop=2
"统一缩进为2"
set softtabstop=2
set shiftwidth=2
"代码折叠"
set nofoldenable
"左下角显示当前vim模式"
set showmode
"允许使用退格键，或set backspace=2"
set backspace=eol,start,indent
set whichwrap+=<,>,h,l
"取消换行"
set nowrap
"启动的时候不显示那个援助索马里儿童的提示"
set shortmess=atI
"在被分割的窗口间显示空白，便于阅读"
set fillchars=vert:\ ,stl:\ ,stlnc:\
"光标移动到buffer的顶部和底部时保持3行距离, 或set so=3"
set scrolloff=3
"设定默认解码"
set fenc=utf-8
set fencs=utf-8,usc-bom,euc-jp,gb18030,gbk,gb2312,cp936
"设定字体"
set guifont=Courier_New:h11:cANSI
set guifontwide=新宋体:h11:cGB2312
"设定编码  或 set encoding=utf-8"
set enc=utf-8
set fileencodings=ucs-bom,utf-8,chinese
set langmenu=zh_CN.UTF-8
language message zh_CN.UTF-8
source $VIMRUNTIME/delmenu.vim
source $VIMRUNTIME/menu.vim
"自动补全"
filetype plugin indent on
set completeopt=longest,menu
"自动补全命令时候使用菜单式匹配列表"
set wildmenu
autocmd FileType ruby,eruby set omnifunc=rubycomplete#Complete
autocmd FileType python set omnifunc=pythoncomplete#Complete
autocmd FileType javascript set omnifunc=javascriptcomplete#CompleteJS
autocmd FileType html set omnifunc=htmlcomplete#CompleteTags
autocmd FileType css set omnifunc=csscomplete#CompleteCSS
autocmd FileType xml set omnifunc=xmlcomplete#CompleteTags
autocmd FileType java set omnifunc=javacomplete#Complet
```
`ps`：以上内容[参考](https://www.cnblogs.com/qiyuexin/p/6398606.html#_labelTop)
***
#### 3. 插件
插件是为了让vim使用起来更方便、优雅，而这里需要介绍是一款针对vim最好用的插件管理器：[vim-plug](https://github.com/junegunn/vim-plug)
这里建议直接阅读对应GitHub中README操作

* **安装**

```
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```
***

* **配置插件**
  * 打开plug.vim：`vim ~/.vim/autoload/plug.vim`
  * 参照文件开头说明，重新配置`.vimrc`，在文件末尾加上如图信息：
  
 
 ```
"自动装载插件语句"
if empty(glob('~/.vim/autoload/plug.vim'))
  silent !curl -fLo ~/.vim/autoload/plug.vim --create-dirs
    \ https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
  autocmd VimEnter * PlugInstall --sync | source $MYVIMRC
endif

"插件配置"
call plug#begin('~/.vim/plugged')
Plug 'junegunn/vim-plug'
Plug 'scrooloose/nerdtree', {'on': 'NERDTreeToggle'}
Plug 'jistr/vim-nerdtree-tabs'
Plug 'Xuyuanp/nerdtree-git-plugin'
"Plug 'Valloric/YouCompleteMe'"
Plug 'Raimondi/delimitMate'
Plug 'Shougo/deoplete.nvim', { 'do': ':UpdateRemotePlugins' }
Plug 'sheerun/vim-polyglot'
Plug 'w0rp/ale'
Plug 'rking/ag.vim'
Plug 'kien/ctrlp.vim'
Plug 'vim-scripts/mru.vim'
Plug 'itchyny/lightline.vim'
Plug 'scrooloose/nerdcommenter'
"Plug 'airblade/vim-gitgutter'"
Plug 'tpope/vim-fugitive'
Plug 'suan/vim-instant-markdown'
Plug 'junegunn/goyo.vim'
Plug 'amix/vim-zenroom2'
Plug 'mattn/emmet-vim'
Plug 'othree/html5.vim'
Plug 'hail2u/vim-css3-syntax'
Plug 'ap/vim-css-color'
Plug 'pangloss/vim-javascript'
Plug 'maksimr/vim-jsbeautify'
Plug 'mxw/vim-jsx'
Plug 'prettier/vim-prettier', {
  \ 'do': 'yarn install',
  \ 'for': ['javascript', 'typescript', 'css', 'less', 'scss', 'json', 'graphql', 'markdown'] }
Plug 'hotoo/pangu.vim'
"Plug 'junegunn/seoul256.vim'"
"Plug 'junegunn/goyo.vim'"
"Plug 'junegunn/limelight.vim'"
call plug#end()
  ```
  
* **安装插件**
  * 进入vim界面：
保存修改好的`.vimrc`并退出，输入命令`vim`进入到如图显示界面：
  ![vim界面](https://upload-images.jianshu.io/upload_images/2317192-47d12683d8da49d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  * `: + PlugStatus`：
  查看插件情况
  * `: + PlugInstall`：
  安装刚刚配置的插件，一键下载安装，如图（之前已安装完成）：
  ![PlugInstall](https://upload-images.jianshu.io/upload_images/2317192-6df946354934bd06.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



`ps`：以上内容[参照](https://segmentfault.com/a/1190000011466454#articleHeader12)
***
#### 4. 常用命令
在备注常用命令之前，需要通过下图来阐述一下vim的三种模式：`一般模式`、`编辑模式`、`指令模式`
![图解](https://upload-images.jianshu.io/upload_images/2317192-fba089878cc8f161.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* **命令模式**（一般模式，打开文件后）
  * **移动**		
  `Ctrl + f`：屏幕向下移动一页		
  `Ctrl + b`：屏幕向上移动一页		
  `0 或 Home`：移动到光标行最前		
  `$ 或 End`：移动到光标行最后		
  `H`：移到屏幕第一行最前		
  `M`：移到屏幕中间行最前		
  `L`：移到屏幕最后行最前		
  `G`：移到文档最后行最前		
  `nG`：移到文档第n行最前		
  `gg`：移到文档第一行最前，相当于`1G`		
  `n + enter`：向下移动n行		
  * **搜索**		
  `/word`：搜索光标下面名称为word的字符		
  `?word`：搜索光标上面名称为word的字符		
  `n`：重复之前搜索的动作		
  `N`：重复之前搜索的动作，方向相反		
  `n1,n2s/word1/word2/g`：n1到n2行之间，用word2代替word1		
  `1,$s/word1/word2/g`：第一行到最后一行之间，用word2代替word1		
  `1,$s/word1/word2/gc`：同上，但在取代是需要确认		
  * **删除、复制、粘贴**		
  `x,X`：x向后删除一个字符，X向前删除一个字符		
  `dd`：删除光标所在行		
  `ndd`：删除光标向下n行		
  `d0`：删除光标到行首的字符		
  `d$`：删除光标到行末的字符		
  `d1G`：删除光标到文档首的所有字符		
  `dG`：删除光标到文档末的所有字符		
  `yy`：复制光标所在行		
  `nyy`：复制光标向下n行		
  `其他`：同d		
  `p,P`：p在光标行下方粘贴，P在光标行上方粘贴		
  `u`：复原前一个动作		
  `Ctrl + r`：重做上一个动作		
  `.`：重复前一个动作		
***

* **一般模式**`到`**编辑模式**		
    `i,I`：i在光标处输入，I在当前行第一个非空格符处输入		
    `a,A`：a在光标下一字符处输入，A在当前行最后一字符处输入		
    `o,O`：o在光标下一行输入新行，O在当光标上一行输入新行		
    `r,R`：r取代光标字符一次，R一直取代光标字符知道Esc		
    `Esc`：**编辑模式**`到`**一般模式**		
***

* **一般模式**`到`**指令模式**		
    `:w`：保存写入数据到文档		
    `:w!`：强制保存写入数据到文档		
    `:q`：退出vim		
    `:q!`：强制退出vim，不保存修改		
    `:wq`：保存写入数据到文档后退出vim		
    `:wq!`：强制保存写入数据到文档后退出vim		
    `:! + command`：在vim中执行shell命令并查看结构		
    `:set nu`：显示行号		
    `:set nu`：不显示行号		

`ps`：这里列出一下常用指令，详细内容参考[菜鸟教程](http://www.runoob.com/linux/linux-vim.html)

***
## 小结
首先感谢以上链接参考文章的作者，感谢文章提供的启发与帮助！
再者以上是鄙人学习、配置vim的记录，主要是为了之后换电脑等情况方便查找恢复，如果能给到看官一点帮助，那真是莫大的荣幸！

