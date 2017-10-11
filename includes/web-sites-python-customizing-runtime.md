Azure 将确定要用于具有以下优先级其虚拟环境的 Python 版本：

1. 根文件夹中的 runtime.txt 内指定的版本
2. 由 web 应用程序配置中的 Python 设置指定的版本 (**设置** > **应用程序设置**在 Azure 门户中 web 应用的边栏选项卡)
3. 如果上述任何未指定，python 2.7 是默认值

有效值的内容 

    \runtime.txt

是：

* python 2.7
* python 3.4

如果了 micro 版本指定 （第三个数字），它将被忽略。

