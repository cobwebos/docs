---
title: 在 Jupyter 笔记本中安装包-Azure Notebooks 预览
description: 了解如何在 Azure 上运行的 Jupyter 笔记本F#中安装 Python、R 和包。
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: f6359b4c010834ffaee00b6208d309997339f36e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646222"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>从 Azure Notebooks 预览中安装包

虽然可以在[项目级别为笔记本配置环境](configure-manage-azure-notebooks-projects.md#configure-the-project-environment)，但你可能希望直接在一个单独的笔记本内安装包。

从笔记本中安装的包仅适用于当前服务器会话。 服务器关闭后，不保留包安装。

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

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

然后加载 Paket 生成器：
```fsharp
#load "Paket.Generated.Refs.fsx"
```

打开库：
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>后续步骤

- [如何：配置和管理项目](configure-manage-azure-notebooks-projects.md)
- [如何：显示幻灯片](present-jupyter-notebooks-slideshow.md)
