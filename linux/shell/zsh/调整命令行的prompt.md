# 左边增加 root@username
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

# 关闭左边或右边

左边：
```
# 注左边的提示段测试关闭失败，可能是p10k强制不允许关闭左边提示段的原因
typeset -g POWERLEVEL9K_DISABLE_PROMPT=true
```

右边：
```
typeset -g POWERLEVEL9K_DISABLE_RPROMPT=true
```
