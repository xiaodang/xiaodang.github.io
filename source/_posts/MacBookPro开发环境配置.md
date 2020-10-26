---
title: MacBookPro开发环境配置
date: 2020-10-26 18:12:43
categories: 开发工具
tags: macOS
---

由于最近换了新的电脑(MacBookPro 16inch),许多开发环境需要重新配置，记录一下安装的软件。

## 环境配置原则：
* 软件方便统一管理，安装、升级
* 优先选择开源的软件
* 尽量不污染系统环境

## 软件
- - - 

### brew
macOS上的包管理工具，很多软件(包括GUI)都可以通过`brew`来进行安装，卸载，更新。`$ brew install`用来安装命令行工具，`$ brew cask install`用来安装GUI工具。

### clashx
一款开源的代理软件 安装:`$ brew cask install clashx`

### neofetch
一个用来展示系统参数的命令行工具。安装:`$ brew install neofetch`
![neofetch](https://cdn.jsdelivr.net/gh/xiaodang/blog-image/img/20201026210721.png)

### zsh & oh my zsh
由于Catalina上的默认`shell`已经从`bash`改为`zsh`，所以安装了`oh my zsh`用来管理`zsh`的插件，查看本机的默认SHELL只需要在`terminal`中执行`$ echo $SHELL`或`$ cat /etc/shells | tail -n 1 `就可以了。`/etc/shells`文件里面存放了所有支持的`shell`,最后一个便是默认的`shell`。

* `zsh-autosuggestions` zsh的智能化提示插件，可以根据历史命令来进行提示。安装:`$ brew install zsh-autosuggestions`
![autosuggestion](https://cdn.jsdelivr.net/gh/xiaodang/blog-image/img/MacBookPro开发环境配置-autosuggestion.png)

* `autojump` zsh的目录跳转插件，仅仅输入几个字母就可以`cd`到目标目录，非常方便。安装:`$ brew install autojump`

### vscode
开发必备IDE，通过命令`$ code`可以快速在vscode中打开当前目录。安装:`$ brew cask install visual-studio-code`

### rectangle
一款开源的windows窗口管理软件。可以通过快捷键快速把窗口切分为1/2，4/1，最大化，接近最大化，屏幕中间等。安装:`$ brew cask install rectangle`

### openinterminal-lite
一款可以快速在当前目录打开`terminal`的开源软件。安装:`$ brew cask install openinterminal-lite`

### google-chrome
谷歌浏览器，习惯了它的开发者调试工具。安装:`$ brew cask install google-chrome`

### docker
一种容器解决方案。可以很方便地对一些服务进行管理。例如：macOS下需要装两个版本的mysql，虽然有实现方案，但是不够优雅。如果使用`docker`就很简单。只需要创建`docker-mysql-version57.yml`文件，并且运行`$ docker-compose -f docker-mysql-version57.yml -d up`即可启动服务。如果需要8.0版本只需要复制一个`yml`配置文件并修改相关参数即可。安装：`brew cask install docker`
```yaml
#mysql 5.7
version: "3.7"
services:
  db:
    image: mysql:5.7
    container_name: mysql5.7
    restart: always
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=ysd1991
    volumes:
      - "/Users/xiaodang/software/mysql/datadir57:/var/lib/mysql"
volumes:
  db_data: null

```

### redis
`redis`同样也选择使用`docker`安装。创建名为`docker-compose.yml`的文件，执行`$ docker-compose up -d`。
```yml
version: "3.7"
services:
    redis:
        hostname: redis
        image: redis
        container_name: redis
        restart: always
        command: redis-server /etc/redis.conf
        environment:
             - TZ=Asia/Shanghai
        volumes:
             - ./data:/data
             - ./redis.conf:/etc/redis.conf
        ports:
             - "6379:6379"
    
```


### redis-cli
通过`docker`安装的redis服务，所以本地环境中没有`redis-cli`工具，通过[homebrew-redis-cli](https://github.com/aoki/homebrew-redis-cli)安装

### mysqlworkbench
一款管理mysql的GUI工具，安装：`$ brew cask install mysqlworkbench`

### nvm
Node的版本管理工具，由于官方不建议通过`brew`安装。只能手动:`$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.36.0/install.sh | bash`

### pyenv
python的版本管理工具。由于macOS系统自带的`python`版本为2.7，并且没有`pip`，所以较好的实践方法是。用python来安装2.7版本和3.X版本，这样不会因为修改系统的python而出现奇怪的问题。 安装:`$ brew install pyenv`

### picGo
一款图床工具，可以用`GitHub`等平台作为图床，用于上传和管理blog里的图片，因为`hexo`的图片管理真的太难用了。安装：`$ brew cask install picGo`。**注意**:picGo默认开启了快捷方式shift+command+p，和vscode的Command Palette快捷键冲突，可以选择关闭。

## 系统设置
- - -
* `设置-触控板-开启 轻点来点按`
* `设置-辅助功能-指针控制-触控板选项-启用三只拖拽`
* `设置-触控板-光标与点按-查询与数据检查器 三指点按`
* `设置-程序坞-置于屏幕左边 并 自动显示和隐藏程序坞`


**注：以上的软件安装步骤仅做本人记录，完整安装需要查看官方文档，以及安装过程中的log输出。**
  

