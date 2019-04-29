---
title: 在 Azure 上的 Jupyter 笔记本中安装包
description: 如何从在 Azure 上运行的 Jupyter 笔记本内安装 Python、R 和 F# 包。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 6f089c12-128b-4dbd-96e3-1320d37eeba4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 504158f248cde3a399475cdec99de3e6a4ebfcc5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598009"
---
# <a name="install-packages-from-within-a-notebook"></a>从笔记本内安装包

虽然可以在[项目级别为笔记本配置环境](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)，但你可能希望直接在一个单独的笔记本内安装包。

从笔记本中安装的包仅适用于当前服务器会话。 服务器关闭后，不保留包安装。

## <a name="python"></a>Python

可在代码单元内使用 pip 或 conda 命令安装 Python 包：

```bash
!pip install <package_name>

!conda install <package_name> -y
```

如果命令输出指示已满足要求，Azure Notebooks 可能已默认包含了该包。 还可以通过[项目环境设置步骤](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)安装包。

## <a name="r"></a>R

可以在代码单元中使用 `install.packages` 函数，从 CRAN 或 GitHub 安装 R 包：

```r
install.packages("package_name")
```

还可以从 GitHub 使用开发工具库安装预发行版本和其他开发包：

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

可以从代码单元内调用 Paket 依赖项管理器，从 [nuget.org](https://www.nuget.org) 安装 F# 包。 先加载 Paket 管理器：

```fsharp
#load "Paket.fsx"
```

再安装包：

```fsharp
Paket.Package
[ "MathNet.Numerics"
"MathNet.Numerics.FSharp"
]
```

## <a name="next-steps"></a>后续步骤

- [如何：配置和管理项目](configure-manage-azure-notebooks-projects.md)
- [如何：演示幻灯片放映](present-jupyter-notebooks-slideshow.md)
