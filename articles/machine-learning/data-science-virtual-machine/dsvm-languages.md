---
title: "Azure 上的数据科学虚拟机的语言 | Microsoft Docs"
description: "Azure 上的数据科学虚拟机的语言"
keywords: "数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 2f2125e739b738847e03ce429d65801969611685
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>数据科学虚拟机支持的语言 

数据科学虚拟机 (DSVM) 提供了几种预建语言和开发工具，用于构建 AI 应用程序。 以下是一些功能突出的语言和开发工具。 

## <a name="python"></a>Python

|    |           |
| ------------- | ------------- |
| 支持的语言版本 | 2.7 和 3.5 |
| 支持的 DSVM 版本      | Linux、Windows     |
| 如何在 DSVM 上配置 /安装它？  | 已创建两个全局 `conda` 环境。 <br /> 位于 `/anaconda/` 的 * `root` 环境是 Python 2.7。 <br/> 位于 `/anaconda/envs/py35` 的 * `py35` 环境是 Python 3.5。       |
| 到示例的链接      | 适用于 Python 的示例 Jupyter 笔记本包括在内     |
| DSVM 上的相关工具      | PySpark、R、Julia      |
### <a name="how-to-use--run-it"></a>如何使用/运行它？    

Windows：

* 在命令提示符中运行

打开命令提示符，并根据要运行的 Python 版本执行以下操作。 

```
# To run Python 2.7
activate 
python --version

# To run Python 3.5
activate py35
python --version

```
* 在 IDE 中使用

使用安装在 Visual Studio Community Edition 中的针对 Visual Studio 的 Python 工具 (PTVS)。 在 Python 2.7 的 PTVS 中自动设置的唯一环境。 
> [!NOTE]
> 要将 PTVS 指向 Python 3.5，则需要在 PTVS 中创建自定义环境。 要在 Visual Studio Community Edition 中设置此环境路径，请导航到“工具” -> “Python 工具” -> “Python 环境”，然后单击“+ 自定义”。 然后将位置设置为 `c:\anaconda\envs\py35`， 接着单击“自动检测”。 

* 在 Jupyter 中使用

打开 Jupyter，然后单击 `New` 按钮以创建新的笔记本。 此时，你可以为 Python 2.7 选择“Python [Conda Root]”作为内核类型，为 Python 3.5 环境选择“Python [Conda env:py35]”作为内核类型。 

* 安装 Python 程序包

DSVM 上的默认 Python 环境是所有用户均可访问的全局环境。 但只有管理员可以写入/安装全局程序包。 为了将程序包安装到全局环境中，以管理员身份使用 `activate` 命令激活根环境或 py35 环境。 然后，你可以使用程序包管理器（如 `conda` 或 `pip`）来安装或更新程序包。 


Linux：

* 在终端中运行

打开终端，并根据要运行的 Python 版本执行以下操作。 

```
# To run Python 2.7
source activate 
python --version

# To run Python 3.5
source activate py35
python --version

```
* 在 IDE 中使用

使用在 Visual Studio Community Edition 中安装的 PyCharm。 

* 在 Jupyter 中使用

打开 Jupyter，然后单击 `New` 按钮以创建新的笔记本。 此时，你可以为 Python 2.7 选择“Python [Conda Root]”作为内核类型，为 Python 3.5 环境选择“Python [Conda env:py35]”作为内核类型。 

* 安装 Python 程序包

DSVM 上的默认 Python 环境是所有用户均可访问的全局环境。 但只有管理员可以写入/安装全局程序包。 为了将程序包安装到全局环境中，以管理员身份或具有 sudo 权限的用户身份使用 `source activate` 命令激活根环境或 py35 环境。 然后，你可以使用程序包管理器（如 `conda` 或 `pip`）来安装或更新程序包。 

## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| 支持的语言版本 | Microsoft R Open 3.x（与 CRAN-R 完全兼容<br /> Microsoft R Server 9.x 开发人员版（一个可扩展的企业就绪型 R 平台）|
| 支持的 DSVM 版本      | Linux、Windows     |
| 如何在 DSVM 上配置/安装它？  | Windows：`C:\Program Files\Microsoft\R Server\R_SERVER` <br />Linux：` /usr/lib64/microsoft-r/3.3/lib64/R`    |
| 到示例的链接      | 适用于 R 的示例 Jupyter 笔记本包括在内     |
| DSVM 上的相关工具      | SparkR、Python、Julia      |
### <a name="how-to-use--run-it"></a>如何使用/运行它？    

Windows：

* 在命令提示符中运行

打开命令提示符，只需键入 `R` 即可。

* 在 IDE 中使用

使用安装在 Visual Studio Community Edition 或 RStudio 中的针对 Visual Studio 的 R 工具 (RTVS)。 这些工具可通过“开始”菜单或桌面图标使用。 

* 在 Jupyter 中使用

打开 Jupyter，然后单击 `New` 按钮以创建新的笔记本。 此时，你可以选择 R 作为内核类型，以使用 Jupyter R 内核 (IRKernel)。 

* 安装 R 程序包

R 安装在所有用户均可访问的全局环境中的 DSVM 上。 但只有管理员可以写入/安装全局程序包。 若要将程序包安装到全局环境，请使用上述方法之一运行 R。 然后，你可以运行程序包管理器 `install.packages()` 来安装或更新程序包。 

Linux：

* 在终端中运行

打开终端并运行 `R`。  

* 在 IDE 中使用

使用 Linux DSVM 上安装的 RStudio。  

* 在 Jupyter 中使用

打开 Jupyter，然后单击 `New` 按钮以创建新的笔记本。 此时，你可以选择 R 作为内核类型，以使用 Jupyter R 内核 (IRKernel)。 

* 安装 R 程序包

R 安装在所有用户均可访问的全局环境中的 DSVM 上。 但只有管理员可以写入/安装全局程序包。 若要将程序包安装到全局环境，请使用上述方法之一运行 R。 然后，你可以运行程序包管理器 `install.packages()` 来安装或更新程序包。 


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| 支持的语言版本 | 0.5 |
| 支持的 DSVM 版本      | Linux、Windows     |
| 如何在 DSVM 上配置 /安装它？  | Windows：安装位置 `C:\JuliaPro-VERSION`<br /> Linux：安装位置 `/opt/JuliaPro-VERSION`    |
| 到示例的链接      | 适用于 Julia 的示例 Jupyter 笔记本包括在内     |
| DSVM 上的相关工具      | Python、R      |
### <a name="how-to-use--run-it"></a>如何使用/运行它？    

Windows：

* 在命令提示符中运行

打开命令提示符，只需运行 `julia` 即可。 
* 在 IDE 中使用

使用 `Juno`，安装在 DSVM 上并可用作桌面快捷方式的 Julia IDE。

* 在 Jupyter 中使用

打开 Jupyter，然后单击 `New` 按钮以创建新的笔记本。 此时，你可以选择 `Julia VERSION` 作为内核类型 

* 安装 Julia 程序包

默认 Julia 位置为所有用户均可访问的全局环境。 但只有管理员可以写入/安装全局程序包。 若要将程序包安装到全局环境，请使用上述方法之一运行 Julia。 然后，你可以运行 Julia 程序包管理器命令（如 `Pkg.add()`）来安装或更新程序包。 


Linux：
* 在终端中运行。

打开终端并运行 `julia` 即可。 
* 在 IDE 中使用

使用 `Juno`，安装在 DSVM 上并可用作应用程序菜单快捷方式的 Julia IDE。

* 在 Jupyter 中使用

打开 Jupyter，然后单击 `New` 按钮以创建新的笔记本。 此时，你可以选择 `Julia VERSION` 作为内核类型 

* 安装 Julia 程序包

默认 Julia 位置为所有用户均可访问的全局环境。 但只有管理员可以写入/安装全局程序包。 若要将程序包安装到全局环境，请使用上述方法之一运行 Julia。 然后，你可以运行 Julia 程序包管理器命令（如 `Pkg.add()`）来安装或更新程序包。 

## <a name="other-languages"></a>其他语言

C#：在 Windows 上可用，并可通过 Visual Studio Community Edition 进行访问，或在 `Developer Command Prompt for Visual Studio` 中只需运行 `csc` 命令即可进行访问。 

Java：OpenJDK 在 Linux 和 Windows 版本的 DSVM 上均可使用，并在路径上进行设置。 你可以在 Windows 中的命令提示符或 Linux 中的 bash shell 上键入 `javac` 或 `java` 命令来使用 Java。 

node.js：node.js 在 Linux 和 Windows 版本的 DSVM 上均可使用，并在路径上进行设置。 你可以在 Windows 中的命令提示符或 Linux 中的 bash shell 上键入 `node` 或 `npm` 命令来使用 node.js。 在 Windows 上，安装了用于 Visual Studio 扩展的 Node.js 工具，以提供图形化 IDE 来开发 node.js 应用程序。 

F#：在 Windows 上可用，并可通过 Visual Studio Community Edition 访问，或在 `Developer Command Prompt for Visual Studio` 上只需运行 `fsc` 命令即可进行访问。 


