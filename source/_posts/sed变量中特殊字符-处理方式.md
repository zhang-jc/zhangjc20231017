title: sed变量中特殊字符/处理方式
date: 2021-04-26 14:07:31
tags:
- Shell
- Linux
categories:
- 语言
- Shell
---

如果变量值中包含斜杠（/）特殊字符，在使用sed命令的做行内字符串替换时可以使用井号（#）做为sed语法分隔符，如下：

    ```Shell
    GITLAB_PROJECT_CLONE_URL=ssh://git@192.168.1.10:50022/test/Documentation.git
    sed -i "11s#GITLAB_PROJECT_CLONE_URL#$GITLAB_PROJECT_CLONE_URL#" config.xml
    ```