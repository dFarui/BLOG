---
title: VIM
date: 2021-07-22 03:32:36
author: dFaruiy
tags: 
 - linux
 - vim

top: true
toc: true
categories: vim
keywords: VIM
---
# VIM

## VIM install
| software | version |
| :----: | :----: |
| system | centos8.4.2105 |
| python | python3.6.8 |
| vim | 8.2 |
| cmake | 3.18.2 |
| ctags | 5.8 |
| other | gcc, gcc-c++, make, kernel-devel |

### update vim version to 8.2
```shell
# vim --version
VIM - Vi IMproved 8.2 (2019 Dec 12, compiled Jul 24 2021 10:33:06)
Compiled by root@centos8
Huge version without GUI.  Features included (+) or not (-):
+acl               -farsi             -mouse_sysmouse    -tag_old_static
+arabic            +file_in_path      +mouse_urxvt       -tag_any_white
+autocmd           +find_in_path      +mouse_xterm       -tcl
+autochdir         +float             +multi_byte        +termguicolors
-autoservername    +folding           +multi_lang        +terminal
-balloon_eval      -footer            -mzscheme          +terminfo
+balloon_eval_term +fork()            +netbeans_intg     +termresponse
-browse            +gettext           +num64             +textobjects
++builtin_terms    -hangul_input      +packages          +textprop
+byte_offset       +iconv             +path_extra        +timers
+channel           +insert_expand     -perl              +title
+cindent           +job               +persistent_undo   -toolbar
-clientserver      +jumplist          +popupwin          +user_commands
-clipboard         +keymap            +postscript        +vartabs
+cmdline_compl     +lambda            +printer           +vertsplit
+cmdline_hist      +langmap           +profile           +virtualedit
+cmdline_info      +libcall           -python            +visual
+comments          +linebreak         +python3           +visualextra
+conceal           +lispindent        +quickfix          +viminfo
+cryptv            +listcmds          +reltime           +vreplace
+cscope            +localmap          +rightleft         +wildignore
+cursorbind        -lua               -ruby              +wildmenu
+cursorshape       +menu              +scrollbind        +windows
+dialog_con        +mksession         +signs             +writebackup
+diff              +modify_fname      +smartindent       -X11
+digraphs          +mouse             -sound             -xfontset
-dnd               -mouseshape        +spell             -xim
-ebcdic            +mouse_dec         +startuptime       -xpm
+emacs_tags        -mouse_gpm         +statusline        -xsmp
+eval              -mouse_jsbterm     -sun_workshop      -xterm_clipboard
+ex_extra          +mouse_netterm     +syntax            -xterm_save
+extra_search      +mouse_sgr         +tag_binary
   system vimrc file: "$VIM/vimrc"
     user vimrc file: "$HOME/.vimrc"
 2nd user vimrc file: "~/.vim/vimrc"
      user exrc file: "$HOME/.exrc"
       defaults file: "$VIMRUNTIME/defaults.vim"
  fall-back for $VIM: "/usr/local/share/vim"
Compilation: gcc -c -I. -Iproto -DHAVE_CONFIG_H     -g -O2 -U_FORTIFY_SOURCE -D_FORTIFY_SOURCE=1
Linking: gcc   -L/usr/local/lib -Wl,--as-needed -o vim        -lm -ltinfo   -ldl     -L/usr/lib/python3.6/config -lpython3.6m
```
确保VIM版本为最新并且支持python3（执行vim --version后，显示`+python3`，表示支持python3）
centos8自带vim版本为vim8.0，并不满足接下来的配置，需要重新编译安装vim8.2，步骤如下：
```shell
# 下载vim8.2的包
wget https://ftp.nluug.nl/pub/vim/unix/vim-8.2.tar.bz2

# 解压
tar xvf vim-8.2.tar.bz2

# cd ./vim82 , 重新编译VIM8.2
cd ./vim82
./configure --enable-python3interp=yes --with-python3-config-dir=/usr/lib/python3.6/config #确保重新编译的VIM8.2支持python3
```
configure 的过程可能会出现如下问题：
```log
no terminal library found
checking for tgetent()… configure: error: NOT FOUND!
You need to install a terminal library; for example ncurses.
Or specify the name of the library with –with-tlib.
```
**workaround**
```shell
dnf install ncurses ncurses-devel
```
**编译安装**
```shell
make && make install
```
如果编译之后，vim并没有支持python3，大概率最重要的参数—with-python3-config-dir 路径写的不正确，可以使用python3-config --configdir查看路径，其实高版本的vim是不需要这个参数的，安装脚本可以自行找到--—with-python-config-dir参数，但是需要有python-devel支持
如果没有，可以使用dnf安装，我的环境使用如下命令安装
```shell
dnf install python36-devel

# test
[root@centos8 vim82]# python3-config --configdir
/usr/lib64/python3.6/config-3.6m-x86_64-linux-gnu
```
## VIM config

### Plugin and Instruction manual
- `VundleVim/Vundle.vim`
   **插件管理**
   ```shell
   git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
   ```
- `Lokaltog/vim-powerline`
   **状态栏，vimrc配置**
   ```vim
   " Powerline 设置
   " 设置状态栏主题风格
   " 将字体设置为Meslo LG S DZ Regular for Powerline 13号大小
   set guifont=Meslo\ LG\ S\ DZ\ Regular\ for\ Powerline:h13
   
   let g:Powerline_symbols = 'fancy'       " Powerline_symbols为状态栏中的箭头，unicode没有箭头
   "let g:Powerline_symbols= 'unicode'
   
   set laststatus=2                " 必须设置为2,否则状态栏不显示
   set t_Co=256                    " 开启256颜色之后，colorschema在vim里好看了许多
   let g:Powerline_colorscheme='solarized256'  " 状态栏使用了solarized256配色方案
   ```
   ![powerline](https://raw.githubusercontent.com/dFarui/images/master/powerline.png)
- `majutsushi/tagbar`
    **使用插件**
    ```shell
    # 依赖ctags
    dnf install ctags
    # 插件使用方式 每个人设置不同，详细根据vimrc文件设置使用<space>键位为leader键
    <space> + t: 打开tagbar
    ?: 快捷帮助文档
    ```
    **vimrc配置**
    ```vim
    "------------tagbar--------
    "设置显示／隐藏标签列表子窗口的快捷键。速记：identifier list by tag
    nnoremap <Leader>t :TagbarToggle<CR>
    let tagbar_right=1                                "设置tagbar 子窗口的位置出现在主编辑区的右边
    ```
- `scrooloose/nerdcommenter`
   **多行注释**
   **插件使用方法**：
   ```shell
   <space> +cc  #生成注释
   <space> +cu  #删除注释
   ```
- `Xuyuanp/nerdtree-git-plugin`
   **目录树添加git图标**
   ```vim
   " 开发的过程中，我们希望git信息直接在NERDTree中显示出来， 和Eclipse一样，修改的文件和增加的文件都给出相应的标注， 这时需要安   装的插件就是 nerdtree-git-plugin,配置信息如下
   let g:NERDTreeGitStatusIndicatorMapCustom = {
       \ "Modified"  : "✹",
       \ "Staged"    : "✚",
       \ "Untracked" : "✭",
       \ "Renamed"   : "➜",
       \ "Unmerged"  : "═",
       \ "Deleted"   : "✖",
       \ "Dirty"     : "✗",
       \ "Clean"     : "✔︎",
       \ "Unknown"   : "?"
       \ }
   ```
- `scrooloose/nerdtree`
   **添加目录树**
   **vimrc配置**  
   ```vim
   "------------NERDTree------------
   " 使用 NERDTree 插件查看工程文件。设置快捷键，速记：file list
   nmap <Leader>f :NERDTreeToggle<CR>
   
   let NERDTreeChDirMode=1
   
   "显示书签"
   let NERDTreeShowBookmarks=1
   
   "设置忽略文件类型"
   let NERDTreeIgnore=['\~$', '\.pyc$', '\.swp$']
   
   "窗口大小"
   let NERDTreeWinSize=35
   
   " 修改默认箭头
   let g:NERDTreeDirArrowExpandable = '▸'
   let g:NERDTreeDirArrowCollapsible = '▾'
   
   "How can I open a NERDTree automatically when vim starts up if no files were specified?
   autocmd StdinReadPre * let s:std_in=1
   autocmd VimEnter * if argc() == 0 && !exists("s:std_in") | NERDTree | endif
   
   " 打开vim时自动打开NERDTree
   " autocmd vimenter * NERDTree
   
   "How can I open NERDTree automatically when vim starts up on opening a directory?
   autocmd StdinReadPre * let s:std_in=1
   autocmd VimEnter * if argc() == 1 && isdirectory(argv()[0]) && !exists("s:std_in") | exe 'NERDTree' argv()   [0] | wincmd p | ene | endif
   
   " 关闭vim时，如果打开的文件除了NERDTree没有其他文件时，它自动关闭，减少多次按:q!
   autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTree") && b:NERDTree.isTabTree()) | q | endif
   
   " 显示行号
   "let NERDTreeShowLineNumbers=1
   let NERDTreeAutoCenter=1
   ```
   **使用插件**
   ```txt
   <sapce> + f: 自定义启用/隐藏目录树
   ?: 快速帮助文档
   o: 打开一个目录或者打开文件，创建的是buffer，也可以用来打开书签
   go: 打开一个文件，但是光标仍然留在NERDTree，创建的是buffer
   t: 打开一个文件，创建的是Tab，对书签同样生效
   T: 打开一个文件，但是光标仍然留在NERDTree，创建的是Tab，对书签同样生效
   i: 水平分割创建文件的窗口，创建的是buffer
   gi: 水平分割创建文件的窗口，但是光标仍然留在NERDTree
   s: 垂直分割创建文件的窗口，创建的是buffer
   gs: 和gi，go类似
   x: 收起当前打开的目录
   X: 收起所有打开的目录
   e: 以文件管理的方式打开选中的目录
   D: 删除书签
   P: 大写，跳转到当前根路径
   p: 小写，跳转到光标所在的上一级路径
   K: 跳转到第一个子路径
   J: 跳转到最后一个子路径
   <C-j>和<C-k>: 在同级目录和文件间移动，忽略子目录和子文件
   C: 将根路径设置为光标所在的目录
   u: 设置上级目录为根路径
   U: 设置上级目录为跟路径，但是维持原来目录打开的状态
   r: 刷新光标所在的目录
   R: 刷新当前根路径
   I: 显示或者不显示隐藏文件
   f: 打开和关闭文件过滤器
   q: 关闭NERDTree
   A: 全屏显示NERDTree，或者关闭全屏
   ```
- `tpope/vim-fugitive`
   VIM CMD 模式中使用git命令，和普通命令一样，但是需要大写G;也有特定的命令:
   |git|fugitive|action
   |:----|:----|:----:|
   |:Git add %|:Gwrite|	Stage the current file to the index|
   |:Git checkout %|:Gread|Revert current file to last checked in version|
   |:Git rm %|:Gremove|Delete the current file and the corresponding Vim buffer|
   |:Git mv %|:Gmove|Rename the current file and the corresponding Vim buffer|
   **Git --> : --> G**

- `ctrlpvim/ctrlp.vim`
   **搜索历史打开文件，在normal模式下按ctrl+p触发**
   **使用方式：**
   ```vim
   Press <F5> to purge the cache for the current directory to get new files, remove deleted files and apply new ignore options.
   Press <c-f> and <c-b> to cycle between modes.
   Press <c-d> to switch to filename only search instead of full path.
   Press <c-r> to switch to regexp mode.
   Use <c-j>, <c-k> or the arrow keys to navigate the result list.
   Use <c-t> or <c-v>, <c-x> to open the selected entry in a new tab or in a new split.
   Use <c-n>, <c-p> to select the next/previous string in the prompt's history.
   Use <c-y> to create a new file and its parent directories.
   Use <c-z> to mark/unmark multiple files and <c-o> to open them.
   ```

- `Lokaltog/vim-easymotion`
   **快速跳转，按两下leader键和f组合,查找单个字符，然后快速跳转**
- `vim-scripts/ShowTrailingWhitespace.git`
   **高亮显示行尾的多余空白字符**
- `kshenoy/vim-signature`
   **书签可视化的插件**
   ```vim
   let g:SignatureMap = {
        \ 'Leader'             :  "m",
        \ 'PlaceNextMark'      :  "m,",
        \ 'ToggleMarkAtLine'   :  "m.",
        \ 'PurgeMarksAtLine'   :  "m-",
        \ 'DeleteMark'         :  "dm",
        \ 'PurgeMarks'         :  "mda",
        \ 'PurgeMarkers'       :  "m<BS>",
        \ 'GotoNextLineAlpha'  :  "']",
        \ 'GotoPrevLineAlpha'  :  "'[",
        \ 'GotoNextSpotAlpha'  :  "`]",
        \ 'GotoPrevSpotAlpha'  :  "`[",
        \ 'GotoNextLineByPos'  :  "]'",
        \ 'GotoPrevLineByPos'  :  "['",
        \ 'GotoNextSpotByPos'  :  "mn",
        \ 'GotoPrevSpotByPos'  :  "mp",
        \ 'GotoNextMarker'     :  "[+",
        \ 'GotoPrevMarker'     :  "[-",
        \ 'GotoNextMarkerAny'  :  "]=",
        \ 'GotoPrevMarkerAny'  :  "[=",
        \ 'ListLocalMarks'     :  "ms",
        \ 'ListLocalMarkers'   :  "m?"
        \ }
   ```
- `vim-scripts/BOOKMARKS--Mark-and-Highlight-Full-Lines`
   **书签行高亮**
- `vim-scripts/Solarized.git`
   **主题方案**
   ```vim
   if has("gui_running")
       set guioptions=gR
       set mousemodel=popup
       set background=light
       ""hi LineNr cterm=bold guibg=black guifg=white
       ""hi CursorLine cterm=none ctermbg=lightgray ctermfg=none
       ""hi CursorColumn cterm=none ctermbg=lightgray ctermfg=none
   else
       set background=dark
       ""hi LineNr cterm=bold ctermbg=black ctermfg=white
       ""hi CursorLine cterm=none ctermbg=darkgray ctermfg=none
       ""hi CursorColumn cterm=none ctermbg=darkgray ctermfg=none
   endif
   
   :silent! colorscheme solarized
   "colorscheme default
   ```
- `nathanaelkane/vim-indent-guides.git`
   **缩进对齐显示**
   \<Leader> + sj : 开/关缩进可视化
   ```vim
   " indent guides
   "let g:indent_guides_enable_on_vim_startup=1
   " 从第二层开始可视化显示缩进
   let g:indent_guides_start_level=2
   "let g:indent_guides_auto_colors = 0
   "hi IndentGuidesOdd  guibg=red   ctermbg=3
   "hi IndentGuidesEven guibg=green ctermbg=4
   " 色块宽度
   let g:indent_guides_guide_size=1
   " 快捷键 <Leader>sj 开/关缩进可视化
   noremap <Leader>sj :IndentGuidesToggle<CR>
   ```
- `vim-scripts/Markdown`
   **Markdown语法高亮**
- `ekalinin/Dockerfile.vim`
   **Dockerfile语法高亮**
- `Valloric/YouCompleteMe`
   自动补全插件,通过Vundle安装后，还需要手动编译
   **安装YCM**
   ```vim
   # 官方推荐使用Vundle安装，只需将下面命令加入到~/.vimrc
   Plugin 'Valloric/YouCompleteMe'

   # 如果`:PluginInstall`过程出错，则退出重新尝试，或者直接从git上下载到`～/.vim/bundle/YouCompleteMe` 目录下
   git clone https://github.com/Valloric/YouCompleteMe#full-installation-guide ～/.vim/bundle/YouCompleteMe
   ```
   **编译YCM**
   ```shell
   cd ~/.vim/bundle/YouCompleteMe
   git submodule update --init --recursive
   python3 install.py
   ```
   **配置YCM**
   ```vim
   " 补全菜单的开启与关闭
   set completeopt=longest,menu                    " 让Vim的补全菜单行为与一般IDE一致(参考VimTip1228)
   let g:ycm_min_num_of_chars_for_completion=2             " 从第2个键入字符就开始罗列匹配项
   let g:ycm_cache_omnifunc=0                      " 禁止缓存匹配项,每次都重新生成匹配项
   let g:ycm_autoclose_preview_window_after_completion=1       " 智能关闭自动补全窗口
   autocmd InsertLeave * if pumvisible() == 0|pclose|endif         " 离开插入模式后自动关闭预览窗口
   
   " 补全菜单中各项之间进行切换和选取：默认使用tab  s-tab进行上下切换，使用空格选取。可进行自定义设置：
   "let g:ycm_key_list_select_completion=['<c-n>']
   "let g:ycm_key_list_select_completion = ['<Down>']      " 通过上下键在补全菜单中进行切换
   "let g:ycm_key_list_previous_completion=['<c-p>']
   "let g:ycm_key_list_previous_completion = ['<Up>']
   " 回车即选中补全菜单中的当前项
   inoremap <expr> <CR> pumvisible() ? "\<C-y>" : "\<CR>"
   
   " 开启各种补全引擎
   let g:ycm_collect_identifiers_from_tags_files=1         " 开启 YCM 基于标签引擎
   let g:ycm_auto_trigger = 1                  " 开启 YCM 基于标识符补全，默认为1
   let g:ycm_seed_identifiers_with_syntax=1                " 开启 YCM 基于语法关键字补全
   let g:ycm_complete_in_comments = 1              " 在注释输入中也能补全
   let g:ycm_complete_in_strings = 1               " 在字符串输入中也能补全
   let g:ycm_collect_identifiers_from_comments_and_strings = 0 " 注释和字符串中的文字也会被收入补全
   
   " 重映射快捷键
   "上下左右键的行为 会显示其他信息,inoremap由i 插入模式和noremap不重映射组成，只映射一层，不会映射到映射的映射
   inoremap <expr> <Down>     pumvisible() ? "\<C-n>" : "\<Down>"
   inoremap <expr> <Up>       pumvisible() ? "\<C-p>" : "\<Up>"
   inoremap <expr> <PageDown> pumvisible() ? "\<PageDown>\<C-p>\<C-n>" : "\<PageDown>"
   inoremap <expr> <PageUp>   pumvisible() ? "\<PageUp>\<C-p>\<C-n>" : "\<PageUp>"
   
   "nnoremap <F5> :YcmForceCompileAndDiagnostics<CR>           " force recomile with syntastic
   "nnoremap <leader>lo :lopen<CR>    "open locationlist
   "nnoremap <leader>lc :lclose<CR>    "close locationlist
   "inoremap <leader><leader> <C-x><C-o>
   
   nnoremap <leader>jd :YcmCompleter GoToDefinitionElseDeclaration<CR> " 跳转到定义处
   let g:ycm_confirm_extra_conf=0                  " 关闭加载.ycm_extra_conf.py确认提示
   ```
- `luochen1990/rainbow`
   **彩虹括号**
   ```vim
   let g:rainbow_active = 1 "0 if you want to enable it later via :RainbowToggle
   ```
- `vim-scripts/indentpython.vim`
   **python 自动缩进**
- `tell-k/vim-autopep8`
   **python代码自动格式化为符合pep8标准**
   ```vim
   " 按照PEP8标准来配置vim
   au BufNewFile,BufRead *.py set tabstop=4 |set softtabstop=4|set shiftwidth=4|set textwidth=79|set expandtab|set autoindent|set fileformat=unix
   
   " Disable show diff window
   let g:autopep8_disable_show_diff=1
   
   " vim-autopep8自1.11版本之后取消了F8快捷键，需要用户自己为:Autopep8设置快捷键：
   autocmd FileType python noremap <buffer> <F8> :call Autopep8()<CR>
   ```
- `ryanoasis/vim-devicons`
   **Adds file type icons to Vim plugins such as: NERDTree, vim-airline, CtrlP, unite, Denite, lightline, vim-startify and many more**

## 配置完成的VIM
![vim](https://raw.githubusercontent.com/dFarui/images/master/vim.png)

![vim1](https://raw.githubusercontent.com/dFarui/images/master/vim1.png)

## 完整的configure 

### vimrc
```vim
" 开启文件类型侦测
filetype on
" 根据侦测到的不同类型加载对应的插件
filetype plugin on

" 设置外观 -------------------------------------
"set number                      "显示行号
set showtabline=0               "隐藏顶部标签栏"
set guioptions-=r               "隐藏右侧滚动条"
set guioptions-=L               "隐藏左侧滚动条"
set guioptions-=b               "隐藏底部滚动条"
set cursorline                  "突出显示当前行"
"set cursorcolumn                "突出显示当前列"
" 变成辅助 -------------------------------------
syntax on                           "开启语法高亮
set nowrap                      "设置代码不折行"
set fileformat=unix             "设置以unix的格式保存文件"
set cindent                     "设置C样式的缩进格式"
set tabstop=4                   "一个 tab 显示出来是多少个空格，默认 8
set shiftwidth=4                "每一级缩进是多少个空格
set backspace+=indent,eol,start "set backspace&可以对其重置
set showmatch                   "显示匹配的括号"
set scrolloff=5                 "距离顶部和底部5行"
set laststatus=2                "命令行为两行"
set ignorecase                  "忽略大小写"
set incsearch
set hlsearch                    "高亮搜索项"
set noexpandtab                 "不允许扩展table"
set whichwrap+=<,>,h,l
set autoread

" 打开上次文件关闭的位置
if has("autocmd")
    au BufReadPost * if line("'\"") > 1 && line("'\"") <= line("$") | exe "normal! g'\"" | endif
endif

" *************** 设置全局快捷键 *****************

" 定义快捷键的前缀，即<Leader>
"let mapleader="\<space>"
"let mapleader="'"
let mapleader="\<space>"
" 指定屏幕上可以进行分割布局的区域
set splitbelow               " 允许在下部分割布局
set splitright               " 允许在右侧分隔布局
" 组合快捷键：
" 组合快捷键：- Ctrl-j 切换到下方的分割窗口
nnoremap <Leader>j <C-W>j
" 组合快捷键：- Ctrl-k 切换到上方的分割窗口
nnoremap <Leader>k <C-W>k
" 组合快捷键：- Ctrl-l 切换到右侧的分割窗口
nnoremap <Leader>l <C-W>l
" 组合快捷键：- Ctrl-h 切换到左侧的分割窗口
nnoremap <Leader>h <C-W>h

" 设置快捷键将选中文本块复制至系统剪贴板
vnoremap <Leader>y "+y
" 设置快捷键将系统剪贴板内容粘贴至 vim
nmap <Leader>p "+p
" 定义快捷键关闭当前分割窗口
nmap <Leader>q :q<CR>
" 定义快捷键保存当前窗口内容
nmap <Leader>w :w<CR>
" 使用sudo强制保存文件
nmap <Leader>W :w !sudo tee %<CR>

" ---------------------plugin--------------------------------------
set nocompatible              " be iMproved, required
filetype off                  " required

" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
" alternatively, pass a path where Vundle should install plugins
"call vundle#begin('~/some/path/here')

" let Vundle manage Vundle, required
Plugin 'VundleVim/Vundle.vim'

"status 美化
Plugin 'Lokaltog/vim-powerline'

"taglist的增强版，查看标签，依赖于ctags
Plugin 'majutsushi/tagbar'

"多行注释，leader键+cc生成, leader+cu删除注释
Plugin 'scrooloose/nerdcommenter'

"文件浏览
Plugin 'scrooloose/nerdtree'
"添加git状态图标
Plugin 'Xuyuanp/nerdtree-git-plugin'
" vim use git
Plugin 'tpope/vim-fugitive'

"搜索历史打开文件，在命令行模式下按ctrl+p触发
"Plugin 'kien/ctrlp.vim'
Plugin 'ctrlpvim/ctrlp.vim'
"快速跳转，按两下leader键和f组合
Plugin 'Lokaltog/vim-easymotion'

"高亮显示行尾的多余空白字符
Plugin 'vim-scripts/ShowTrailingWhitespace.git'

"书签可视化的插件
Plugin 'kshenoy/vim-signature'

"书签行高亮
Plugin 'vim-scripts/BOOKMARKS--Mark-and-Highlight-Full-Lines'

"主题方案
Plugin 'vim-scripts/Solarized.git'

"缩进对齐显示
Plugin 'nathanaelkane/vim-indent-guides.git'

" Markdown语法高亮
Plugin 'vim-scripts/Markdown'

" Dockerfile语法高亮
Plugin 'ekalinin/Dockerfile.vim'

" 自动补全
Plugin 'Valloric/YouCompleteMe'

" 彩虹括号
Plugin 'luochen1990/rainbow'

" python 自动缩进
Plugin 'vim-scripts/indentpython.vim'

" python代码自动格式化为符合pep8标准
Plugin 'tell-k/vim-autopep8'

Plugin 'ryanoasis/vim-devicons'

call vundle#end()
filetype on

" 按照PEP8标准来配置vim
au BufNewFile,BufRead *.py set tabstop=4 |set softtabstop=4|set shiftwidth=4|set textwidth=79|set expandtab|set autoindent|set fileformat=unix

" Disable show diff window
let g:autopep8_disable_show_diff=1

" vim-autopep8自1.11版本之后取消了F8快捷键，需要用户自己为:Autopep8设置快捷键：
autocmd FileType python noremap <buffer> <F8> :call Autopep8()<CR>

let g:rainbow_active = 1 "0 if you want to enable it later via :RainbowToggle

" Powerline 设置
" 设置状态栏主题风格
" 将字体设置为Meslo LG S DZ Regular for Powerline 13号大小
set guifont=Meslo\ LG\ S\ DZ\ Regular\ for\ Powerline:h13

let g:Powerline_symbols = 'fancy'       " Powerline_symbols为状态栏中的箭头，unicode没有箭头
"let g:Powerline_symbols= 'unicode'

set laststatus=2                " 必须设置为2,否则状态栏不显示
set t_Co=256                    " 开启256颜色之后，colorschema在vim里好看了许多
let g:Powerline_colorscheme='solarized256'  " 状态栏使用了solarized256配色方案

"------------tagbar--------
"设置显示／隐藏标签列表子窗口的快捷键。速记：identifier list by tag
nnoremap <Leader>t :TagbarToggle<CR>
let tagbar_right=1                                "设置tagbar 子窗口的位置出现在主编辑区的右边


"------------NERDTree------------
" 使用 NERDTree 插件查看工程文件。设置快捷键，速记：file list
nmap <Leader>f :NERDTreeToggle<CR>

let NERDTreeChDirMode=1

"显示书签"
let NERDTreeShowBookmarks=1

"设置忽略文件类型"
let NERDTreeIgnore=['\~$', '\.pyc$', '\.swp$']

"窗口大小"
let NERDTreeWinSize=25

" 修改默认箭头
let g:NERDTreeDirArrowExpandable = '▸'
let g:NERDTreeDirArrowCollapsible = '▾'

"How can I open a NERDTree automatically when vim starts up if no files were specified?
autocmd StdinReadPre * let s:std_in=1
autocmd VimEnter * if argc() == 0 && !exists("s:std_in") | NERDTree | endif

" 打开vim时自动打开NERDTree
" autocmd vimenter * NERDTree

"How can I open NERDTree automatically when vim starts up on opening a directory?
autocmd StdinReadPre * let s:std_in=1
autocmd VimEnter * if argc() == 1 && isdirectory(argv()[0]) && !exists("s:std_in") | exe 'NERDTree' argv()[0] | wincmd p | ene | endif

" 关闭vim时，如果打开的文件除了NERDTree没有其他文件时，它自动关闭，减少多次按:q!
autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTree") && b:NERDTree.isTabTree()) | q | endif

" 开发的过程中，我们希望git信息直接在NERDTree中显示出来， 和Eclipse一样，修改的文件和增加的文件都给出相应的标注， 这时需要安装的插件就是 nerdtree-git-plugin,配置信息如下
let g:NERDTreeGitStatusIndicatorMapCustom = {
    \ "Modified"  : "✹",
    \ "Staged"    : "✚",
    \ "Untracked" : "✭",
    \ "Renamed"   : "➜",
    \ "Unmerged"  : "═",
    \ "Deleted"   : "✖",
    \ "Dirty"     : "✗",
    \ "Clean"     : "✔︎",
    \ "Unknown"   : "?"
    \ }

" 显示行号
"let NERDTreeShowLineNumbers=1
let NERDTreeAutoCenter=1

"-------------------kshenoy/vim-signature-----------------------
" signature设置
let g:SignatureMap = {
        \ 'Leader'             :  "m",
        \ 'PlaceNextMark'      :  "m,",
        \ 'ToggleMarkAtLine'   :  "m.",
        \ 'PurgeMarksAtLine'   :  "m-",
        \ 'DeleteMark'         :  "dm",
        \ 'PurgeMarks'         :  "mda",
        \ 'PurgeMarkers'       :  "m<BS>",
        \ 'GotoNextLineAlpha'  :  "']",
        \ 'GotoPrevLineAlpha'  :  "'[",
        \ 'GotoNextSpotAlpha'  :  "`]",
        \ 'GotoPrevSpotAlpha'  :  "`[",
        \ 'GotoNextLineByPos'  :  "]'",
        \ 'GotoPrevLineByPos'  :  "['",
        \ 'GotoNextSpotByPos'  :  "mn",
        \ 'GotoPrevSpotByPos'  :  "mp",
        \ 'GotoNextMarker'     :  "[+",
        \ 'GotoPrevMarker'     :  "[-",
        \ 'GotoNextMarkerAny'  :  "]=",
        \ 'GotoPrevMarkerAny'  :  "[=",
        \ 'ListLocalMarks'     :  "ms",
        \ 'ListLocalMarkers'   :  "m?"
        \ }

if has("gui_running")
    set guioptions=gR
    set mousemodel=popup
    set background=light
    ""hi LineNr cterm=bold guibg=black guifg=white
    ""hi CursorLine cterm=none ctermbg=lightgray ctermfg=none
    ""hi CursorColumn cterm=none ctermbg=lightgray ctermfg=none
else
    set background=dark
    ""hi LineNr cterm=bold ctermbg=black ctermfg=white
    ""hi CursorLine cterm=none ctermbg=darkgray ctermfg=none
    ""hi CursorColumn cterm=none ctermbg=darkgray ctermfg=none
endif

:silent! colorscheme solarized
"colorscheme default

" indent guides
"let g:indent_guides_enable_on_vim_startup=1
" 从第二层开始可视化显示缩进
let g:indent_guides_start_level=2
"let g:indent_guides_auto_colors = 0
"hi IndentGuidesOdd  guibg=red   ctermbg=3
"hi IndentGuidesEven guibg=green ctermbg=4
" 色块宽度
let g:indent_guides_guide_size=1
" 快捷键 <Leader>sj 开/关缩进可视化
noremap <Leader>sj :IndentGuidesToggle<CR>

" 每行不能超过180字符，否则高亮显示
highlight OverLength ctermbg=red ctermfg=white guibg=#592929
match OverLength /\%180v.\+/

" 补全菜单的开启与关闭
set completeopt=longest,menu                    " 让Vim的补全菜单行为与一般IDE一致(参考VimTip1228)
let g:ycm_min_num_of_chars_for_completion=2             " 从第2个键入字符就开始罗列匹配项
let g:ycm_cache_omnifunc=0                      " 禁止缓存匹配项,每次都重新生成匹配项
let g:ycm_autoclose_preview_window_after_completion=1       " 智能关闭自动补全窗口
autocmd InsertLeave * if pumvisible() == 0|pclose|endif         " 离开插入模式后自动关闭预览窗口

" 补全菜单中各项之间进行切换和选取：默认使用tab  s-tab进行上下切换，使用空格选取。可进行自定义设置：
"let g:ycm_key_list_select_completion=['<c-n>']
"let g:ycm_key_list_select_completion = ['<Down>']      " 通过上下键在补全菜单中进行切换
"let g:ycm_key_list_previous_completion=['<c-p>']
"let g:ycm_key_list_previous_completion = ['<Up>']
" 回车即选中补全菜单中的当前项
inoremap <expr> <CR>      pumvisible() ? "\<C-y>" : "\<CR>"

" 开启各种补全引擎
let g:ycm_collect_identifiers_from_tags_files=1         " 开启 YCM 基于标签引擎
let g:ycm_auto_trigger = 1                  " 开启 YCM 基于标识符补全，默认为1
let g:ycm_seed_identifiers_with_syntax=1                " 开启 YCM 基于语法关键字补全
let g:ycm_complete_in_comments = 1              " 在注释输入中也能补全
let g:ycm_complete_in_strings = 1               " 在字符串输入中也能补全
let g:ycm_collect_identifiers_from_comments_and_strings = 0 " 注释和字符串中的文字也会被收入补全

" 重映射快捷键
"上下左右键的行为 会显示其他信息,inoremap由i 插入模式和noremap不重映射组成，只映射一层，不会映射到映射的映射
inoremap <expr> <Down>     pumvisible() ? "\<C-n>" : "\<Down>"
inoremap <expr> <Up>       pumvisible() ? "\<C-p>" : "\<Up>"
inoremap <expr> <PageDown> pumvisible() ? "\<PageDown>\<C-p>\<C-n>" : "\<PageDown>"
inoremap <expr> <PageUp>   pumvisible() ? "\<PageUp>\<C-p>\<C-n>" : "\<PageUp>"

"nnoremap <F5> :YcmForceCompileAndDiagnostics<CR>           " force recomile with syntastic
"nnoremap <leader>lo :lopen<CR>    "open locationlist
"nnoremap <leader>lc :lclose<CR>    "close locationlist
"inoremap <leader><leader> <C-x><C-o>

nnoremap <leader>jd :YcmCompleter GoToDefinitionElseDeclaration<CR> " 跳转到定义处
let g:ycm_confirm_extra_conf=0                  " 关闭加载.ycm_extra_conf.py确认提示
```
