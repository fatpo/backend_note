我用的主题：
```
with root@gg ~/www > cat ~/.zshrc | grep -i ZSH_THEME                                                                                                    with root@gg at 05:17:34
ZSH_THEME="powerlevel10k/powerlevel10k"
```

想在左边看到`with root@gg`，需要修改 `~/.p10k.zsh`，加上`context`:
```
 typeset -g POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(
    #os_icon               # os identifier
    context                # 加上这个
    dir                     # current directory
    vcs                     # git status
    prompt_char             # prompt symbol
```

然后重启:
```
source ~/.zshrc
```
