---
title: 使用 R 扩展试验
titleSuffix: ML Studio (classic) - Azure
description: 如何使用执行 R 脚本模块通过 R 语言扩展 Azure 机器学习 Studio （经典）的功能。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7b4b869695eb2073121a889cd81d99c4fc06d4b9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79218035"
---
# <a name="azure-machine-learning-studio-classic-extend-your-experiment-with-r"></a>Azure 机器学习 Studio （经典）：通过 R 扩展试验 

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
您可以使用 "[执行 r 脚本][execute-r-script]" 模块，通过 R 语言扩展 Azure 机器学习 Studio （经典）的功能。

此模块接受多个输入数据集，并且生成单个数据集作为输出。 可以将 R 脚本键入到[执行 r 脚本][execute-r-script]模块的**r 脚本**参数中。

使用与以下代码类似的代码可访问模块的每个输入端口：

    dataset1 <- maml.mapInputPort(1)

## <a name="listing-all-currently-installed-packages"></a>列出所有当前安装的程序包
已安装程序包列表可更改。 可在[Azure 机器学习 Studio （经典）支持的 R 包](https://msdn.microsoft.com/library/azure/mt741980.aspx)中找到当前已安装包的列表。

你还可以通过在 "[执行 R 脚本][execute-r-script]" 模块中输入以下代码来获取已安装包的完整列表：

    out <- data.frame(installed.packages(,,,fields="Description"))
    maml.mapOutputPort("out")

这会将包列表发送到[执行 R 脚本][execute-r-script]模块的输出端口。
若要查看包列表，请将转换模块（如[转换为 CSV][convert-to-csv] ）连接到[执行 R 脚本][execute-r-script]模块的左侧输出，运行实验，然后单击转换模块的输出并选择 "**下载**"。 

![下载“转换为 CSV”模块的输出](./media/extend-your-experiment-with-r/download-package-list.png)


<!--
For convenience, here is the [current full list with version numbers in Excel format](https://az754797.vo.msecnd.net/docs/RPackages.xlsx).
-->

## <a name="importing-packages"></a>导入程序包
你可以使用[执行 R 脚本][execute-r-script]模块中的以下命令导入尚未安装的包：

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

其中 `my_favorite_package.zip` 文件包含包。




<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
