先安装下上传工具：
```
apt-get install -y lrzsz
```

执行下面脚本：
```
#!/bin/bash
apt-get install -y zsh

sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" -y


echo "zsh" >> ~/.bashrc


folder1=${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-history-substring-search
folder2=${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
folder3=${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

echo "check $folder1"
if [ ! -d "$folder1" ]; then
  git clone https://github.com/zsh-users/zsh-history-substring-search ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-history-substring-search
fi

echo "check $folder2"
if [ ! -d "$folder2" ]; then
  git clone https://github.com/zsh-users/zsh-syntax-highlighting ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
fi

echo "check $folder3"
if [ ! -d "$folder3" ]; then
  git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
fi


sed -i 's/plugins=(git)/plugins=(git zsh-autosuggestions zsh-syntax-highlighting zsh-history-substring-search)/g' ~/.zshrc

if [ -f "./diff_total.zip" ]; then
  
    unzip ./diff_total.zip

    cp ./diff_total/diff* ./diff_total/id
    cp ./diff_total/diff* ./diff_total/th
    cp ./diff_total/diff* ./diff_total/my

    unzip ./diff_total/id/id.zip -d ./diff_total/id/
    unzip ./diff_total/th/th.zip -d ./diff_total/th/
    unzip ./diff_total/my/my.zip -d ./diff_total/my/
   
    head -3000 ./diff_total/id/id.txt > ./diff_total/id/id_3000.txt
    head -5000 ./diff_total/id/id.txt > ./diff_total/id/id_5000.txt
    head -10000 ./diff_total/id/id.txt > ./diff_total/id/id_10000.txt

    unzip ./diff_total/th/th.zip
    head -1000 ./diff_total/th/th.txt > ./diff_total/th/th_1000.txt
    head -3000 ./diff_total/th/th.txt > ./diff_total/th/th_3000.txt
    head -5000 ./diff_total/th/th.txt > ./diff_total/th/th_5000.txt
    head -10000 ./diff_total/th/th.txt > ./diff_total/th/th_10000.txt

    unzip ./diff_total/my/my.zip
    head -1000 ./diff_total/my/my.txt > ./diff_total/my/my_1000.txt
    head -3000 ./diff_total/my/my.txt > ./diff_total/my/my_3000.txt
    head -5000 ./diff_total/my/my.txt > ./diff_total/my/my_5000.txt
    head -10000 ./diff_total/my/my.txt > ./diff_total/my/my_10000.txt
fi

echo "you can exec like this: cd ./diff_total/id; ./diff.sh IP1:8080 IP2:8080 20 10 &"

chsh -s /bin/zsh
```

请注意：
```
如果之前没安装过 zsh，需要执行脚本两次，第一次安装 zsh 会中断这个 shell 的执行，第二次解压 diff 数据。
```

