# Vim

## Mouseless with vim

{% embed url="https://www.youtube.com/watch?v=E-ZbrtoSuzw" %}

![](../.gitbook/assets/text-object.png)

![](../.gitbook/assets/text-object.png)

![](../.gitbook/assets/combination.png)

![](../.gitbook/assets/motions.png)

![](../.gitbook/assets/scrolling.png)

![](../.gitbook/assets/find.png)

![](../.gitbook/assets/marks.png)

![](../.gitbook/assets/jumplist.png)

![](../.gitbook/assets/buffer.png)

![](../.gitbook/assets/window-buffer.png)

![](../.gitbook/assets/tabs.png)

![](../.gitbook/assets/buffer.png)

## Move line\(s\) up and down with ALT+jk

```bash
nnoremap ª :m .+1<CR>==
nnoremap º :m .-2<CR>==

inoremap ª <Esc>:m .+1<CR>==gi
inoremap º <Esc>:m .-2<CR>==gi

vnoremap ª :m '>+1<CR>gv=gv
vnoremap º :m '<-2<CR>gv=gv
```

{% embed url="http://vim.wikia.com/wiki/Moving\_lines\_up\_or\_down" %}

