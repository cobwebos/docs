Azure 存储空间中的每个 blob 必须驻留在一个容器。 该容器构成 blob 名称的一部分。 例如，`mycontainer`是这些示例 blob Uri 中的容器的名称：

    https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
    https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg

容器名称必须是有效的 DNS 名称，符合以下命名规则：

1. 容器名称必须以字母或数字开头，并且只能包含字母、 数字和短划线 （-） 字符。
2. 必须立即前面并且后跟字母或数字; 每个短划线 （-） 字符容器名称中不允许使用连续划线。
3. 容器名称中的所有字母必须都为小写。
4. 容器名称必须是 3 到 63 个字符。

> [!IMPORTANT]
> 请注意，容器的名称必须始终为小写。 如果你在容器名称，包括大写字母-或其他方式违反了容器命名规则，你可能会收到 400 错误 （错误请求）。 
> 
> 

