---
title: 支持的语言
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine 上预先安装了支持的程序语言和相关工具。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: aedaa194a9667f1b89a3370c39c74424f2d01734
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208073"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>数据科学虚拟机支持的语言 

Data Science Virtual Machine (DSVM) 附带了几种预生成的语言和开发工具, 用于构建你的人工智能 (AI) 应用程序。 下面是一些值得注意的部分。

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 edition)

|    |           |
| ------------- | ------------- |
| 支持的语言版本 | Python 2.7 和3。6 |
| 支持的 DSVM 版本      | Windows Server 2016     |
| 如何在 DSVM 上配置/安装它？  | 创建两`conda`个全局环境: <br /> `root` *`/anaconda/`位于的环境是 Python 3.6。 <br/> `python2` *`/anaconda/envs/python2`位于的环境是 Python 2.7。       |
| 指向示例的链接      | 包含 Python 的示例 Jupyter 笔记本。     |
| DSVM 上的相关工具      | PySpark、R、Julia。      |

> [!NOTE]
> 2016年 3 2018 月之前创建的 Windows Server 版本包含 Python 3.5 和 Python 2.7。 Python 2.7 是 conda**根**环境, **py35**是 Python 3.5 环境。

### <a name="how-to-use-and-run-it"></a>如何使用和运行它    

* 在命令提示符下运行:

  打开命令提示符, 根据要运行的 Python 版本, 使用以下方法之一:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.6
    activate 
    python --version 
* Use in an IDE:

  Use Python Tools for Visual Studio (PTVS), installed in the Visual Studio Community edition. By default, the only environment that's set up automatically in PTVS is Python 3.6. 

    > [!NOTE]
    > To point PTVS at Python 2.7, you must create a  custom environment in PTVS. To set this environment path in the Visual Studio  Community Edition, go to **Tools** -> **Python Tools** -> **Python Environments** and select **+ Custom**. Then, set the location to **c:\anaconda\envs\python2** and select **Auto Detect**.

* Use in Jupyter:

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as _Python [Conda Root]_ for Python 3.6 and _Python [Conda env:python2]_ for Python 2.7.

* Install Python packages:

  The default Python environments on the DSVM are global environments that are readable by all users. But only administrators can write and install global packages. To install packages to the global environment, activate to the root or python2 environment by using the `activate` command as an administrator. Then, you can use a package manager like `conda` or `pip` to install or update packages.

## Python (Linux and Windows Server 2012 edition)

|    |           |
| ------------- | ------------- |
| Language versions supported | Python 2.7 and 3.5 |
| Supported DSVM editions      | Linux, Windows Server 2012    |
| How is it configured / installed on the DSVM?  | Two global `conda` environments are created: <br /> * `root` environment located at `/anaconda/` is Python 2.7. <br/> * `py35` environment located at `/anaconda/envs/py35`is Python 3.5.       |
| Links to samples      | Sample Jupyter notebooks for Python are included.     |
| Related tools on the DSVM      | PySpark, R, Julia      |
### How to use and run it    

**Linux**
* Run in a terminal:

  Open the terminal and do one of the following, depending on the version of Python you want to run:

    ```
    # <a name="to-run-python-27"></a>运行 Python 2。7
    源激活 python--版本
    
    # <a name="to-run-python-35"></a>运行 Python 3。5
    源激活 py35 python--版本
    
    ```
* Use in an IDE:

  Use PyCharm, installed in the Visual Studio Community edition. 

* Use in Jupyter:

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as **Python [Conda Root]** for Python 2.7 and **Python [Conda env:py35]** for the Python 3.5 environment. 

* Install Python packages:

  The default Python environments on the DSVM are global environments readable by all users. But only administrators can write and install global packages. To install packages to the global environment, activate to the root or py35 environment by using the `source activate` command as an administrator or as a user with sudo permissions. Then, you can use a package manager like `conda` or `pip` to install or update packages.

**Windows 2012**
* Run at a command prompt:

  Open a command prompt and do one of the following, depending on the version of Python you want to run:

     ```
    # <a name="to-run-python-27"></a>运行 Python 2。7
    激活 python--版本
    
    # <a name="to-run-python-35"></a>运行 Python 3。5
    激活 py35 python-版本
    
    ```
* Use in an IDE:

  Use Python Tools for Visual Studio (PTVS) installed in the Visual Studio Community edition. The only environment that's set up automatically in PTVS is Python 2.7.
    > [!NOTE]
    > To point PTVS at Python 3.5, you need to create a custom environment in PTVS. To set this environment path in the Visual Studio Community edition, go to **Tools** -> **Python Tools** -> **Python Environments** and select **+ Custom**. Then, set the location to `c:\anaconda\envs\py35` and select _Auto Detect_.

* Use in Jupyter:

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as **Python [Conda Root]** for Python 2.7 and **Python [Conda env:py35]** for Python 3.5. 

* Install Python packages:

  The default Python environments on the DSVM are global environments that are readable by all users. But only administrators can write and install global packages. To install packages to the global environment, activate to the root or py35 environment by using the `activate` command as an administrator. Then, you can use a package manager like `conda` or `pip` to install or update packages.

## R

|    |           |
| ------------- | ------------- |
| Language versions supported | Microsoft R Open 3.x (100% compatible with CRAN-R)<br /> Microsoft R Server 9.x Developer edition (a scalable enterprise-ready R platform)|
| Supported DSVM editions      | Linux, Windows     |
| How is it configured / installed on the DSVM?  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Links to samples      | Sample Jupyter notebooks for R are included.     |
| Related tools on the DSVM      | SparkR, Python, Julia      |
### How to use and run it    

**Windows**:

* Run at a command prompt:

  Open a command prompt and type `R`.

* Use in an IDE:

  Use RTools for Visual Studio (RTVS) installed in the Visual Studio Community edition or RStudio. These are available on the Start menu or as a desktop icon. 

* Use in Jupyter

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as **R** to use the Jupyter R kernel (IRKernel).

* Install R packages:

  R is installed on the DSVM in a global environment that's readable by all users. But only administrators can write and install global packages. To install packages to the global environment, run R by using one of the preceding methods. Then, you can run the R package manager `install.packages()` to install or update packages.

**Linux**:

* Run in terminal:

  Open a terminal and run `R`.  

* Use in an IDE:

  Use RStudio, installed on the Linux DSVM.  

* Use in Jupyter:

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as **R** to use the Jupyter R kernel (IRKernel). 

* Install R packages:

  R is installed on the DSVM in a global environment that's readable by all users. But only administrators can write and install global packages. To install packages to the global environment, run R by using one of the preceding methods. Then, you can run the R package manager `install.packages()` to install or update packages.


## Julia

|    |           |
| ------------- | ------------- |
| Language versions supported | 0.6 |
| Supported DSVM editions      | Linux, Windows     |
| How is it configured / installed on the DSVM?  | Windows: Installed at `C:\JuliaPro-VERSION`<br /> Linux: Installed at `/opt/JuliaPro-VERSION`    |
| Links to samples      | Sample Jupyter notebooks for Julia are included.     |
| Related tools on the DSVM      | Python, R      |
### How to use and run it    

**Windows**:

* Run at a command prompt

  Open a command prompt and run `julia`.
* Use in an IDE:

  Use `Juno` with the Julia IDE installed on the DSVM and available as a desktop shortcut.

* Use in Jupyter:

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as **Julia VERSION**.

* Install Julia packages:

  The default Julia location is a global environment that's readable by all users. But only administrators can write and install global packages. To install packages to the global environment, run Julia by using one of the preceding methods. Then, you can run Julia package manager commands like `Pkg.add()` to install or update packages.


**Linux**:
* Run in a terminal:

  Open a terminal and run `julia`.
* Use in an IDE:

  Use `Juno`, with the Julia IDE installed on the DSVM and available as an **Application** menu shortcut.

* Use in Jupyter:

  Open Jupyter and select **New** to create a new notebook. You can set the kernel type as **Julia VERSION**.

* Install Julia packages:

  The default Julia location is a global environment that's readable by all users. But only administrators can write and install global packages. To install packages to the global environment, run Julia by using one of the preceding methods. Then, you can run Julia package manager commands like `Pkg.add()` to install or update packages.

## Other languages

**C#**: Available on Windows and accessible through the Visual Studio Community edition or at the `Developer Command Prompt for Visual Studio`, where you can run the `csc` command.

**Java**: OpenJDK is available on both the Linux and Windows editions of the DSVM and is set on the path. To use Java, type the `javac` or `java` command at a command prompt in Windows or on the bash shell in Linux.

**Node.js**: Node.js is available on both the Linux and Windows editions of the DSVM and is set on the path. To access Node.js, type the `node` or `npm` command at a command prompt in Windows or on the bash shell in Linux. On Windows, the Visual Studio extension for the Node.js tools is installed to provide a graphical IDE to develop your Node.js application.

**F#**: Available on Windows and accessible through the Visual Studio Community edition or at a `Developer Command Prompt for Visual Studio`, where you can run the `fsc` command.
