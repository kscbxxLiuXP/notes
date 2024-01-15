# oh-my-zsh

## Omz.sh

```shell

chsh -s $(which zsh)

sh -c "$(curl -fsSL https://gitee.com/kanderWall/oh-my-zsh-shell/releases/download/omz.sh/omz.sh)"

wget https://gitee.com/kanderWall/oh-my-zsh-shell/releases/download/omz.sh/omz.sh
chmod a+x omz.sh
./omz.sh
```





```shell
git clone https://gitee.com/kanderWall/zsh-autosuggestions.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```



```shell
git clone https://gitee.com/kanderWall/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```





```
ZSH_THEME="agnoster"

plugins=(
     git
     zsh-syntax-highlighting
     zsh-autosuggestions
)
```



vim .oh-my-zsh/themes/agnoster.zsh-theme

```
 90 prompt_context() {
 91   if [[ "$USERNAME" != "$DEFAULT_USER" || -n "$SSH_CLIENT" ]]; then
 92     prompt_segment white default "%(!.%{%F{yellow}%}.)%n@%m"
 93   fi
 94 }
```

