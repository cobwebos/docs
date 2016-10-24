Azure 将按以下优先级确定要用于其虚拟环境的 Python 的版本：

1. 在根文件夹中的 runtime.txt 内指定的版本
1. 由 Python 设置在 Web 应用配置中指定的版本（Azure 门户中 Web 应用的“设置”>“应用程序设置”边栏选项卡）
1. 如果未指定以上任何项，则 python 2.7 是默认值

内容的有效值

    \runtime.txt

是：

- python-2.7
- python-3.4

如果指定了 micro 版本（第三个数字）时，将其忽略。

<!---HONumber=AcomDC_0921_2016-->