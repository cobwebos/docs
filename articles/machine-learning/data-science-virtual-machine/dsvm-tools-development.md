---
title: 开发工具
titleSuffix: Azure Data Science Virtual Machine
description: 了解 Data Science Virtual Machine 上提供的工具和集成开发环境。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: bc1f40760c1602d81da042bf6909e44a540d35de
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75612149"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine 上的开发工具

Data Science Virtual Machine （DSVM）在高效集成开发环境（IDE）中捆绑了几个常用工具。 以下是 DSVM 中提供的一些工具。

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

|    |           |
| ------------- | ------------- |
| 它是什么？   | 常规用途 IDE      |
| 支持的 DSVM 版本      | Windows： Visual Studio 2017、Windows 2019 （预览版）： Visual Studio 2019      |
| 典型用途      | 软件开发    |
| 如何在 DSVM 上配置并安装它？      | 数据科学工作负荷（Python 和 R 工具）、Azure 工作负荷（Hadoop、Data Lake）、Node.js、SQL Server 工具、[用于 Visual Studio Code 的 Azure 机器学习](https://github.com/Microsoft/vs-tools-for-ai)    |
| 如何使用和运行它      | 桌面快捷方式（`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`）。 使用桌面图标或 "**开始**" 菜单以图形方式打开 Visual Studio。 依次搜索程序（按 Windows 徽标键+S）、**Visual Studio**。 然后，可以使用 C#、Python、R 和 Node.js 等语言来创建项目。   |
| DSVM 上的相关工具      |     Visual Studio Code、RStudio、Juno  |

> [!NOTE]
> 可能会有一条消息指出评估期已过。 在此情况下，请输入 Microsoft 帐户凭据。 或者新建一个免费帐户来获取 Visual Studio Community 的访问权限。

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| 它是什么？   | 常规用途 IDE      |
| 支持的 DSVM 版本      | Windows、Linux     |
| 典型用途      | 代码编辑器和 Git 集成   |
| 如何使用和运行它      | Windows 中的桌面快捷方式（`C:\Program Files (x86)\Microsoft VS Code\Code.exe`）、Linux 中的桌面快捷方式或终端（`code`）    |
| DSVM 上的相关工具      |     Visual Studio、RStudio、Juno  |

## <a name="rstudio-desktop"></a>RStudio 桌面

|    |           |
| ------------- | ------------- |
| 它是什么？   | R 语言的客户端 IDE   |
| 支持的 DSVM 版本      | Windows、Linux      |
| 典型用途      |  R 开发     |
| 如何使用和运行它      | Windows 上的桌面快捷方式（`C:\Program Files\RStudio\bin\rstudio.exe`），Linux 上的桌面快捷方式（`/usr/bin/rstudio`）      |
| DSVM 上的相关工具      |   Visual Studio、Visual Studio Code、Juno      |

## <a name="rstudio-server"></a>RStudio Server

|    |           |
| ------------- | ------------- |
| 它是什么？   | R 语言的客户端 IDE   |
| 它是什么？   | R 的基于 Web 的 IDE    |
| 支持的 DSVM 版本      | Linux      |
| 典型用途      |  R 开发     |
| 如何使用和运行它      | 启用使用 systemctl 的服务_启用 rstudio_，然后使用_systemctl start rstudio-server_启动该服务。 然后登录到 http：\//your-vm-ip：8787的 RStudio 服务器。       |
| DSVM 上的相关工具      |   Visual Studio、Visual Studio Code、RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| 它是什么？   | Julia 语言的客户端 IDE   |
| 支持的 DSVM 版本      | Windows、Linux      |
| 典型用途      |  Julia 开发     |
| 如何使用和运行它      | Windows 上的桌面快捷方式（`C:\JuliaPro-0.5.1.1\Juno.bat`），Linux 上的桌面快捷方式（`/opt/JuliaPro-VERSION/Juno`）      |
| DSVM 上的相关工具      |   Visual Studio、Visual Studio Code、RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| 它是什么？   | Python 语言的客户端 IDE    |
| 支持的 DSVM 版本      | Windows 2019 （预览版）、Linux      |
| 典型用途      |  Python 开发     |
| 如何使用和运行它      | Windows 上的桌面快捷方式（`C:\Program Files\tk`）。 Linux 上的桌面快捷方式（`/usr/bin/pycharm`）      |
| DSVM 上的相关工具      |   Visual Studio、Visual Studio Code、RStudio      |
