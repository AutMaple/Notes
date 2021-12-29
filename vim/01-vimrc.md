# Vim的配置文件

```vimscript
let mapleader=" "

" 将光标所在行放在屏幕中间
nnoremap ff zz

" 光标上下滚动时，屏幕上下都留有7行代码
set scrolloff=7

" 映射Esc
inoremap

" 光标移动
noremap i k
noremap k j
noremap j b
noremap l w
noremap m i
noremap M I
noremap I 4k
noremap K 4j
noremap J h
noremap L l
" 复制内容到系统剪切板
noremap Y "+y
" 全选
noremap <C-a> ggVG

" 去行尾
noremap gl $
" 到改行的第一个非空白字符处
noremap gj ^

" 取消高亮
noremap <leader><CR> :nohlsearch<CR>

"在高亮的字符串中移动，并将光标放在屏幕中间
nnoremap = nzz
nnoremap - Nzz

" ===========复制相关的操作============
nnoremap yw yiw
nnoremap y" yi"
nnoremap y( yi(
nnoremap y{ yi{
nnoremap yl yw
" 复制当前行
nnoremap yr yy

" 删除相关的操作
nnoremap <silent> dw diw
nnoremap <silent> d" di
nnoremap <silent> d( di(
nnoremap <silent> d{ di{
nnoremap <silent> dl dw
" 删除当前行
nnoremap dr dd


" 删除并进入插入模式
nnoremap cw ciw
nnoremap c" ci"
nnoremap c( ci(
nnoremap c{ ci{
nnoremap cl cw
```

