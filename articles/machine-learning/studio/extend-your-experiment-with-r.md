---
title: 使用 R 扩展实验 | Microsoft Docs
description: 如何使用执行 R 脚本模块通过 R 语言扩展 Azure 机器学习工作室的功能。
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 2c038a45-ba4d-42ea-9a88-e67391ef8c0a
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 99c57cc095c3eb017ada68417b7b36c8773ed3d4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="extend-your-experiment-with-r"></a>使用 R 扩展试验
如何使用[执行 R 脚本][execute-r-script]模块通过 R 语言扩展 Azure 机器学习工作室的功能。

此模块接受多个输入数据集，并且生成单个数据集作为输出。 可将 R 脚本键入[执行 R 脚本][execute-r-script]模块的 **R 脚本**参数。

使用与以下代码类似的代码可访问模块的每个输入端口：

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>列出所有当前安装的程序包
已安装程序包列表可更改。 可在 [Azure 机器学习支持的 R 包](https://msdn.microsoft.com/library/azure/mt741980.aspx)中找到当前安装的包的列表。

还可获取已安装程序包的最新完整列表，方法是将以下代码输入[执行 R 脚本][execute-r-script]模块：

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

将程序包列表发送至[执行 R 脚本][execute-r-script]模块的输出端口。
要查看程序包列表，请将诸如[连接到 CSV][convert-to-csv] 等转换模块连接到[执行 R 脚本][execute-r-script]模块的左输出，运行实验然后单击转换模块的输出并选择“下载”。 

![下载“转换为 CSV”模块的输出](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](http://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>导入程序包
可通过在[执行 R 脚本][execute-r-script]模块中使用以下命令，导入尚未安装的包：

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

其中 `my_favorite_package.zip` 文件包含包。

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
