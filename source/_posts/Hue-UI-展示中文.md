title: Hue UI 展示中文
date: 2020-09-17 17:14:19
tags:
- Hue
- 大数据
categories:
- 大数据
- Hue
---

如果使用开发分支代码（如master分支）编译安装，需要自己编译语言文件。例如Hue安装目录为“/opt/hue”，则安装后执行以下命令：

    $ cd /opt/hue
    $ make locales
    
如果需要则重启Hue即可。是否需要重启我没有验证：）
