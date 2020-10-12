---
title: 支持的语言
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine 上预安装的支持的程序语言和相关工具。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 8a73336ba0a324c65ee0de764d81a8e69ab6079c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87843260"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>数据科学虚拟机支持的语言 

Data Science Virtual Machine (DSVM) 附带了几种预构建的语言和开发工具，用于构建人工智能 (AI) 应用程序。 以下是一些值得注意的语言和开发工具。

## <a name="python-windows-server-2016-edition"></a>Python（Windows Server 2016 版本）

| 类别 | 值 |
| ------------- | ------------- |
| 支持的语言版本 | Python 2.7 和 3.7 |
| 支持的 DSVM 版本      | Windows Server 2016     |
| 如何在 DSVM 上配置/安装它？  | 已创建两个全局 `conda` 环境： <br /> * 位于 `/anaconda/` 的 `root` 环境是 Python 3.7。 <br/> * 位于 `/anaconda/envs/python2` 的 `python2` 环境是 Python 2.7。       |
| 指向示例的链接      | 适用于 Python 的示例 Jupyter 笔记本包括在内。     |
| DSVM 上的相关工具      | PySpark、R、Julia。      |

> [!NOTE]
> 2018 年 3 月之前创建的 Windows Server 2016 版本包含 Python 3.5 和 Python 2.7。 Python 2.7 是 conda 根环境，而 py37 是 Python 3.7 环境 。

### <a name="how-to-use-and-run-it"></a>如何使用和运行它    

* 在命令提示符下运行：

  打开命令提示符，并根据要运行的 Python 版本使用以下方法之一：

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* 在 IDE 中使用：

  使用安装在 Visual Studio Community Edition 中的针对 Visual Studio 的 Python 工具 (PTVS)。 默认情况下，在 PTVS 中自动设置的唯一环境是 Python 3.6。 

    > [!NOTE]
    > 要将 PTVS 指向 Python 2.7，必须在 PTVS 中创建自定义环境。 要在 Visual Studio Community Edition 中设置此环境路径，请转到“工具” -> “Python 工具” -> “Python 环境”，然后选择“自定义”   。 然后，将位置设置为“c:\anaconda\envs\python2”并选择“自动检测”********。

* 在 Jupyter 中使用：

  打开 Jupyter，然后选择“新建”以创建新的笔记本****。 可以将 Python 3.7 的内核类型设置为“Python [Conda Root]”，而将 Python 2.7 的内核类型设置为“Python [Conda env:python2]” 。

* 安装 Python 包：

  DSVM 上的默认 Python 环境是所有用户均可访问的全局环境。 但只有管理员可以编写和安装全局包。 若要将包安装到全局环境中，请以管理员身份使用 `activate` 命令激活根环境或 python2 环境。 然后，你可以使用包管理器（如 `conda` 或 `pip`）来安装或更新包。

## <a name="python-linux-edition"></a>Python（Linux 版）

| 类别 | 值 |
| ------------- | ------------- |
| 支持的语言版本 | Python 2.7 和 3.5 |
| 支持的 DSVM 版本      | Linux   |
| 如何在 DSVM 上配置/安装它？  | 已创建两个全局 `conda` 环境： <br /> *  位于 `/anaconda/` 的 `root` 环境是 Python 2.7。 <br/> *  位于 `/anaconda/envs/py35` 的 `py35` 环境是 Python 3.5。       |
| 指向示例的链接      | 适用于 Python 的示例 Jupyter 笔记本包括在内。     |
| DSVM 上的相关工具      | PySpark、R、Julia      |

### <a name="how-to-use-and-run-it"></a>如何使用和运行它    

* 在终端中运行：

  打开终端，并根据要运行的 Python 版本执行以下某项操作：

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* 在 IDE 中使用：

  使用在 Visual Studio Community Edition 中安装的 PyCharm。 

* 在 Jupyter 中使用：

  打开 Jupyter，然后选择“新建”以创建新的笔记本****。 可以将 Python 2.7 的内核类型设置为“Python [Conda Root]”，而将 Python 3.5 环境的内核类型设置为“Python [Conda env:py35]”********。 

* 安装 Python 包：

  DSVM 上的默认 Python 环境是所有用户均可访问的全局环境。 但只有管理员可以编写和安装全局包。 若要将包安装到全局环境中，请以管理员身份或以拥有 sudo 权限的用户身份使用 `source activate` 命令激活根环境或 py35 环境。 然后，你可以使用包管理器（如 `conda` 或 `pip`）来安装或更新包。


## <a name="r"></a>R

| 类别 | 值 |
| ------------- | ------------- |
| 支持的语言版本 | Microsoft R Open 3.x（与 CRAN-R 完全兼容）<br /> Microsoft R Server 9.x 开发人员版（一个可缩放的企业就绪型 R 平台）|
| 支持的 DSVM 版本      | Linux、Windows     |
| 如何在 DSVM 上配置/安装它？  | Windows： `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux：`/usr/lib64/microsoft-r/3.3/lib64/R`    |
| 指向示例的链接      | 适用于 R 的示例 Jupyter 笔记本包括在内。     |
| DSVM 上的相关工具      | SparkR、Python、Julia      |

### <a name="how-to-use-and-run-it"></a>如何使用和运行它    

**Windows**：

* 在命令提示符下运行：

  打开命令提示符并键入 `R`。

* 在 IDE 中使用：

  使用安装在 Visual Studio Community Edition 或 RStudio 中的针对 Visual Studio 的 R 工具 (RTVS)。 可通过“开始”菜单或桌面图标使用这些工具。 

* 在 Jupyter 中使用

  打开 Jupyter，然后选择“新建”以创建新的笔记本****。 你可以将内核类型设置为 R，以使用 Jupyter R 内核 (IRKernel)****。

* 安装 R 包：

  R 安装在所有用户均可访问的全局环境中的 DSVM 上。 但只有管理员可以编写和安装全局包。 若要将包安装到全局环境中，请使用上述方法之一运行 R。 然后，你可以运行 R 包管理器 `install.packages()` 来安装或更新包。

Linux：

* 在终端中运行：

  打开终端窗口并运行 `R`。  

* 在 IDE 中使用：

  使用 Linux DSVM 上安装的 RStudio。  

* 在 Jupyter 中使用：

  打开 Jupyter，然后选择“新建”以创建新的笔记本****。 你可以将内核类型设置为 R，以使用 Jupyter R 内核 (IRKernel)****。 

* 安装 R 包：

  R 安装在所有用户均可访问的全局环境中的 DSVM 上。 但只有管理员可以编写和安装全局包。 若要将包安装到全局环境中，请使用上述方法之一运行 R。 然后，你可以运行 R 包管理器 `install.packages()` 来安装或更新包。


## <a name="julia"></a>Julia

| 类别 | 值 |
| ------------- | ------------- |
| 支持的语言版本 | 0.6 |
| 支持的 DSVM 版本      | Linux、Windows     |
| 如何在 DSVM 上配置/安装它？  | Windows：安装位置 `C:\JuliaPro-VERSION`<br /> Linux：安装位置 `/opt/JuliaPro-VERSION`    |
| 指向示例的链接      | 适用于 Julia 的示例 Jupyter 笔记本包括在内。     |
| DSVM 上的相关工具      | Python、R      |

### <a name="how-to-use-and-run-it"></a>如何使用和运行它    

**Windows**：

* 在命令提示符下运行

  打开命令提示符并运行 `julia`。
* 在 IDE 中使用：

  使用 `Juno`，在 DSVM 上安装 Julia IDE，并将其用作桌面快捷方式。

* 在 Jupyter 中使用：

  打开 Jupyter，然后选择“新建”以创建新的笔记本****。 可以将内核类型设置为 Julia VERSION****。

* 安装 Julia 包：

  默认 Julia 位置为所有用户均可访问的全局环境。 但只有管理员可以编写和安装全局包。 若要将包安装到全局环境中，请使用上述方法之一运行 Julia。 然后，你可以运行 Julia 包管理器命令（如 `Pkg.add()`）来安装或更新包。


Linux：
* 在终端中运行：

  打开终端窗口并运行 `julia`。
* 在 IDE 中使用：

  使用 `Juno`，并在 DSVM 上安装 Julia IDE 并将其用作应用程序菜单快捷方式****。

* 在 Jupyter 中使用：

  打开 Jupyter，然后选择“新建”以创建新的笔记本****。 可以将内核类型设置为 Julia VERSION****。

* 安装 Julia 包：

  默认 Julia 位置为所有用户均可访问的全局环境。 但只有管理员可以编写和安装全局包。 若要将包安装到全局环境中，请使用上述方法之一运行 Julia。 然后，你可以运行 Julia 包管理器命令（如 `Pkg.add()`）来安装或更新包。

## <a name="other-languages"></a>其他语言

**C#** ：在 Windows 上可用，可通过 Visual Studio Community Edition 访问，或在 `Developer Command Prompt for Visual Studio` 处运行 `csc` 命令以进行访问。

**Java**：OpenJDK 在 Linux 和 Windows 版本的 DSVM 上均可使用，并在路径上进行设置。 若要使用 Java，请在 Windows 中的命令提示符处或在 Linux 中的 bash shell 上键入 `javac` 或 `java` 命令。

**Node.js**：Node.js 在 Linux 和 Windows 版本的 DSVM 上均可使用，并在路径上进行设置。 若要访问 Node.js，请在 Windows 中的命令提示符处或在 Linux 中的 bash shell 上键入 `node` 或 `npm` 命令。 在 Windows 上，安装了 Node.js 工具的 Visual Studio 扩展，以提供图形化 IDE 来开发 Node.js 应用程序。

**F#** ：在 Windows 上可用，并可通过 Visual Studio Community Edition 访问，或在 `Developer Command Prompt for Visual Studio` 上运行 `fsc` 命令即可进行访问。
