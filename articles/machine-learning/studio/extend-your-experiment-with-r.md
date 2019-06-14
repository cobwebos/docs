---
title: 使用 R 扩展试验
titleSuffix: Azure Machine Learning Studio
description: 如何使用执行 R 脚本模块通过 R 语言扩展 Azure 机器学习工作室的功能。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 8c1292d0d36874892a286d91b1e367c7336b99aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60811420"
---
# <a name="azure-machine-learning-studio-extend-your-experiment-with-r"></a>Azure 机器学习工作室：使用 R 扩展试验 
如何使用[执行 R 脚本][execute-r-script]模块通过 R 语言扩展 Azure 机器学习工作室的功能。

此模块接受多个输入数据集，并且生成单个数据集作为输出。 可将 R 脚本键入[执行 R 脚本][execute-r-script]模块的 **R 脚本**参数。

使用与以下代码类似的代码可访问模块的每个输入端口：

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>列出所有当前安装的程序包
已安装程序包列表可更改。 可在 [Azure 机器学习工作室支持的 R 包](https://msdn.microsoft.com/library/azure/mt741980.aspx)中找到当前安装的包的列表。

还可获取已安装程序包的最新完整列表，方法是将以下代码输入[执行 R 脚本][execute-r-script]模块：

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

将程序包列表发送至[执行 R 脚本][execute-r-script]模块的输出端口。
要查看程序包列表，请将诸如[连接到 CSV][convert-to-csv] 等转换模块连接到[执行 R 脚本][execute-r-script]模块的左输出，运行实验然后单击转换模块的输出并选择“下载”  。 

![下载“转换为 CSV”模块的输出](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>导入程序包
可通过在[执行 R 脚本][execute-r-script]模块中使用以下命令，导入尚未安装的包：

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

其中 `my_favorite_package.zip` 文件包含包。




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
