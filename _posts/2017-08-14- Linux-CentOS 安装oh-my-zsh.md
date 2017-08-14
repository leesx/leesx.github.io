> Zsh和bash一样，是一种Unix shell，但大多数Linux发行版本都默认使用bash shell。但Zsh有强大的自动补全参数、文件名、等功能和强大的自定义配置功能。Zsh虽然功能强大，但刚上手配置比较麻烦。别担心，Github已经有开源项目oh-my-zsh。按照官方的文档安装，非常简单。

#### 安装zsh套件
```
yum install zsh -y
```

#### 安装oh-my-zsh套件
http://ohmyz.sh/
```
sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

![ozsh](http://img.blog.csdn.net/20150923162644064?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

查看什么Theme可以用
`ls ~/.oh-my-zsh/themes`

#### 编辑  vi ~/.zshrc
更换zsh的theme的主题 
ZSH_THEME="candy"

![ZSH_THEME](https://pic2.zhimg.com/b25081d7b89a242e90332413d2492235_b.png)

![](http://img.blog.csdn.net/20150923162738511?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### 更换Plugin
`plugins=(git git-flow grails rvm history-substring-search github gradle svn node npm zsh-syntax-highlighting sublime)`

#### 设置zsh为默认的shell
`chsh -s /bin/zsh`

[参考](http://blog.csdn.net/aoshilang2249/article/details/48681849)
