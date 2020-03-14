---
title: 支持的语言
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine 上预先安装了支持的程序语言和相关工具。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: e7b32579712e89c0d5595303ee7e03d8b2462607
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283649"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>数据科学虚拟机支持的语言 

Data Science Virtual Machine （DSVM）附带了几种预生成的语言和开发工具，用于构建你的人工智能（AI）应用程序。 下面是一些值得注意的部分。

## <a name="python-windows-server-2016-edition"></a>Python （Windows Server 2016 edition）

|    |           |
| ------------- | ------------- |
| 支持的语言版本 | Python 2.7 和3。7 |
| 支持的 DSVM 版本      | Windows Server 2016     |
| 如何在 DSVM 上配置/安装它？  | 创建了两个全局 `conda` 环境： <br /> * 位于 `/anaconda/` 的 `root` 环境是 Python 3.7。 <br/> * 位于 `/anaconda/envs/python2` 的 `python2` 环境是 Python 2.7。       |
| 指向示例的链接      | 包含 Python 的示例 Jupyter 笔记本。     |
| DSVM 上的相关工具      | PySpark、R、Julia。      |

> [!NOTE]
> 2016年 3 2018 月之前创建的 Windows Server 版本包含 Python 3.5 和 Python 2.7。 Python 2.7 是 conda**根**环境， **py37**是 Python 3.7 环境。

### <a name="how-to-use-and-run-it"></a>如何使用和运行它    

* 在命令提示符下运行：

  打开命令提示符，根据要运行的 Python 版本，使用以下方法之一：

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* 在 IDE 中使用：

  使用安装在 Visual Studio 社区版中的针对 Visual Studio 的 Python 工具（PTVS）。 默认情况下，在 PTVS 中自动设置的唯一环境是 Python 3.6。 

    > [!NOTE]
    > 若要将 PTVS 指向 Python 2.7，必须在 PTVS 中创建自定义环境。 若要在 Visual Studio 社区版中设置此环境路径，请前往 "**工具**" -> **Python Tools** -> **python 环境**，然后选择 " **+ 自定义**"。 然后，将 "位置" 设置为**c:\anaconda\envs\python2 (适用**，然后选择 "**自动检测**"。

* 在 Jupyter 中使用：

  打开 Jupyter 并选择 "**新建**" 以创建新的笔记本。 可以将 python 3.7 和_python [Conda env： python2]_ 的内核类型设置为 Python _[Conda Root]_ 以用于 python 2.7。

* 安装 Python 包：

  DSVM 上的默认 Python 环境是所有用户都可读取的全局环境。 但只有管理员可以编写和安装全局程序包。 若要将程序包安装到全局环境中，请通过使用 `activate` 命令作为管理员来激活根环境或 python2 环境。 然后，可以使用程序包管理器（如 `conda` 或 `pip`）来安装或更新包。

## <a name="python-linux-edition"></a>Python （Linux 版）

|    |           |
| ------------- | ------------- |
| 支持的语言版本 | Python 2.7 和3。5 |
| 支持的 DSVM 版本      | Linux   |
| 如何在 DSVM 上配置/安装它？  | 创建了两个全局 `conda` 环境： <br /> 位于 `/anaconda/` * `root` 环境是 Python 2.7。 <br/> 位于 `/anaconda/envs/py35`* `py35` 环境是 Python 3.5。       |
| 指向示例的链接      | 包含 Python 的示例 Jupyter 笔记本。     |
| DSVM 上的相关工具      | PySpark、R、Julia      |
### <a name="how-to-use-and-run-it"></a>如何使用和运行它    

* 在终端中运行：

  打开终端并根据要运行的 Python 版本执行以下操作之一：

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* 在 IDE 中使用：

  使用安装在 Visual Studio 社区版中的 PyCharm。 

* 在 Jupyter 中使用：

  打开 Jupyter 并选择 "**新建**" 以创建新的笔记本。 可以将 python 2.7 和**python [Conda env： py35]** 的内核类型设置为 Python **[Conda Root]** ，以用于 python 3.5 环境。 

* 安装 Python 包：

  DSVM 上的默认 Python 环境是所有用户均可访问的全局环境。 但只有管理员可以编写和安装全局程序包。 若要将程序包安装到全局环境中，请使用 `source activate` 命令作为管理员或具有 sudo 权限的用户激活到根环境或 py35 环境。 然后，可以使用程序包管理器（如 `conda` 或 `pip`）来安装或更新包。


## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| 支持的语言版本 | Microsoft R Open 1.x （100% 兼容 CRAN-R）<br /> Microsoft R Server 1.x 开发人员版（可扩展的企业级 R 平台）|
| 支持的 DSVM 版本      | Linux、Windows     |
| 如何在 DSVM 上配置/安装它？  | Windows：`C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux：`/usr/lib64/microsoft-r/3.3/lib64/R`    |
| 指向示例的链接      | 随附适用于 R 的 Jupyter 笔记本示例。     |
| DSVM 上的相关工具      | SparkR、Python、Julia      |
### <a name="how-to-use-and-run-it"></a>如何使用和运行它    

Windows：

* 在命令提示符下运行：

  打开命令提示符，然后键入 `R`。

* 在 IDE 中使用：

  使用安装在 Visual Studio Community Edition 或 RStudio 中的针对 Visual Studio 的 R 工具 (RTVS)。 它们可在 "开始" 菜单中或作为桌面图标。 

* 在 Jupyter 中使用

  打开 Jupyter 并选择 "**新建**" 以创建新的笔记本。 可以将内核类型设置为**R** ，以使用 Jupyter R 内核（IRKernel）。

* 安装 R 包：

  R 安装在可由所有用户读取的全局环境中的 DSVM 上。 但只有管理员可以编写和安装全局程序包。 若要将程序包安装到全局环境，请使用上述方法之一运行 R。 然后，可以运行 R 包管理器 `install.packages()` 来安装或更新包。

Linux：

* 在终端中运行：

  打开终端并运行 `R`。  

* 在 IDE 中使用：

  使用 Linux DSVM 上安装的 RStudio。  

* 在 Jupyter 中使用：

  打开 Jupyter 并选择 "**新建**" 以创建新的笔记本。 可以将内核类型设置为**R** ，以使用 Jupyter R 内核（IRKernel）。 

* 安装 R 包：

  R 安装在可由所有用户读取的全局环境中的 DSVM 上。 但只有管理员可以编写和安装全局程序包。 若要将程序包安装到全局环境，请使用上述方法之一运行 R。 然后，可以运行 R 包管理器 `install.packages()` 来安装或更新包。


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| 支持的语言版本 | 0.6 |
| 支持的 DSVM 版本      | Linux、Windows     |
| 如何在 DSVM 上配置/安装它？  | Windows：安装位置 `C:\JuliaPro-VERSION`<br /> Linux：安装位置 `/opt/JuliaPro-VERSION`    |
| 指向示例的链接      | 包括 Julia 的示例 Jupyter 笔记本。     |
| DSVM 上的相关工具      | Python、R      |
### <a name="how-to-use-and-run-it"></a>如何使用和运行它    

Windows：

* 在命令提示符下运行

  打开命令提示符并运行 `julia`。
* 在 IDE 中使用：

  使用 `Juno` 安装在 DSVM 上的 Julia IDE，并将其作为桌面快捷方式提供。

* 在 Jupyter 中使用：

  打开 Jupyter 并选择 "**新建**" 以创建新的笔记本。 可以将内核类型设置为 " **JULIA 版本**"。

* 安装 Julia 包：

  默认 Julia 位置是所有用户都可读取的全局环境。 但只有管理员可以编写和安装全局程序包。 若要将程序包安装到全局环境，请使用上述方法之一运行 Julia。 然后，你可以运行 Julia 包管理器命令（如 `Pkg.add()`）来安装或更新包。


Linux：
* 在终端中运行：

  打开终端并运行 `julia`。
* 在 IDE 中使用：

  使用 `Juno`，并在 DSVM 上安装 Julia IDE，并将其作为**应用程序**菜单快捷方式使用。

* 在 Jupyter 中使用：

  打开 Jupyter 并选择 "**新建**" 以创建新的笔记本。 可以将内核类型设置为 " **JULIA 版本**"。

* 安装 Julia 包：

  默认 Julia 位置是所有用户都可读取的全局环境。 但只有管理员可以编写和安装全局程序包。 若要将程序包安装到全局环境，请使用上述方法之一运行 Julia。 然后，你可以运行 Julia 包管理器命令（如 `Pkg.add()`）来安装或更新包。

## <a name="other-languages"></a>其他语言

**C#** ：在 Windows 上可用，并可通过 Visual Studio 社区版或 `Developer Command Prompt for Visual Studio`进行访问，你可以在其中运行 `csc` 命令。

**Java**： OPENJDK 在 DSVM 的 Linux 和 Windows 版本上都可用，并在路径上进行了设置。 若要使用 Java，请在 Windows 中的命令提示符处或在 Linux 的 bash shell 中键入 `javac` 或 `java` 命令。

**Node.js： node.js**适用于 DSVM 的 Linux 和 Windows 版本，并在路径上进行设置。 若要访问 node.js，请在 Windows 中的命令提示符处或在 Linux 的 bash shell 中键入 `node` 或 `npm` 命令。 在 Windows 上，安装了用于 node.js 工具的 Visual Studio 扩展，提供了一个图形 IDE 来开发 node.js 应用程序。

**F#** ：在 Windows 上可用，并可通过 Visual Studio 社区版或 `Developer Command Prompt for Visual Studio`进行访问，你可以在其中运行 `fsc` 命令。
