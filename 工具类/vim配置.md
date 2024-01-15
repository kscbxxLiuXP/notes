# vim配置

```vimrc
syntax on
set nu



set nocompatible

if has("autocmd")
  au BufReadPost * if line("'\"") > 1 && line("'\"") <= line("$") | exe "normal! g'\"" | endif
endif

set cursorline
" 高亮当前行
" 备选235 236 237
highlight CursorLine cterm=NONE ctermfg=NONE ctermbg=236
highlight CursorColumn cterm=NONE ctermfg=NONE ctermbg=236
" 取消关键字高亮
highlight CursorLineNR ctermfg=NONE
highlight CursorColumnNR ctermfg=NONE


```





