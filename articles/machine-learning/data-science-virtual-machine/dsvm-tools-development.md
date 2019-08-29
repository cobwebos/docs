---
title: 数据科学虚拟机数据开发工具 - Azure | Microsoft Docs
description: 了解 Data Science Virtual Machine 上预安装的工具和集成开发环境。
keywords: 数据科学工具, 数据科学虚拟机, 数据科学工具, Linux 数据科学
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: vijetaj
ms.openlocfilehash: 8f9dad0fb007945b69b75daadfdb12f61dc4defb
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074307"
---
# <a name="development-tools-on-the-data-science-virtual-machine"></a>数据科学虚拟机开发工具

Data Science Virtual Machine (DSVM) 在高效集成开发环境 (IDE) 中捆绑了几个常用工具。 以下是 DSVM 中提供的一些工具。

## <a name="visual-studio-2019"></a>Visual Studio 2019  

|    |           |
| ------------- | ------------- |
| 它是什么？   | 常规用途 IDE      |
| 支持的 DSVM 版本      | Windows      |
| 典型用途      | 软件开发    |
| 如何在 DSVM 上配置并安装它？      | 数据科学工作负荷（Python 和 R 工具）、Azure 工作负荷（Hadoop、Data Lake）、Node.js、SQL Server 工具、[用于 Visual Studio Code 的 Azure 机器学习](https://github.com/Microsoft/vs-tools-for-ai)    |
| 如何使用和运行它      | 桌面快捷方式`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`()    |
| DSVM 上的相关工具      |     Visual Studio Code、RStudio、Juno  |

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| 它是什么？   | 常规用途 IDE      |
| 支持的 DSVM 版本      | Windows、Linux     |
| 典型用途      | 代码编辑器和 Git 集成   |
| 如何使用和运行它      | Windows 中的`C:\Program Files (x86)\Microsoft VS Code\Code.exe`桌面快捷方式 (), Linux 中的`code`桌面快捷方式或终端 ()    |
| DSVM 上的相关工具      |     Visual Studio 2019、RStudio、Juno  |

## <a name="rstudio--desktop"></a>RStudio Desktop 

|    |           |
| ------------- | ------------- |
| 它是什么？   | R 语言的客户端 IDE   |
| 支持的 DSVM 版本      | Windows、Linux      |
| 典型用途      |  R 开发     |
| 如何使用和运行它      | Windows 上的`C:\Program Files\RStudio\bin\rstudio.exe`桌面快捷方式 (), Linux`/usr/bin/rstudio`上的桌面快捷方式 ()      |
| DSVM 上的相关工具      |   Visual Studio 2019, Visual Studio Code, Juno      |

## <a name="rstudio--server"></a>RStudio Server 

|    |           |
| ------------- | ------------- |
| 它是什么？   | R 语言的客户端 IDE   |
| 它是什么？   | R 的基于 Web 的 IDE    |
| 支持的 DSVM 版本      | Linux      |
| 典型用途      |  R 开发     |
| 如何使用和运行它      | 启用使用 systemctl 的服务_启用 rstudio_, 然后使用_systemctl start rstudio-server_启动该服务。 然后登录到 http:\//your-vm-ip: 8787 上的 RStudio 服务器。       |
| DSVM 上的相关工具      |   Visual Studio 2019, Visual Studio Code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| 它是什么？   | Julia 语言的客户端 IDE   |
| 支持的 DSVM 版本      | Windows、Linux      |
| 典型用途      |  Julia 开发     |
| 如何使用和运行它      | Windows 上的`C:\JuliaPro-0.5.1.1\Juno.bat`桌面快捷方式 (), Linux`/opt/JuliaPro-VERSION/Juno`上的桌面快捷方式 ()      |
| DSVM 上的相关工具      |   Visual Studio 2019, Visual Studio Code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| 它是什么？   | Python 语言的客户端 IDE    |
| 支持的 DSVM 版本      | Linux      |
| 典型用途      |  Python 开发     |
| 如何使用和运行它      | Linux 上的`/usr/bin/pycharm`桌面快捷方式 ()      |
| DSVM 上的相关工具      |   Visual Studio 2019, Visual Studio Code, RStudio      |



## <a name="power-bi-desktop"></a>Power BI Desktop 

|    |           |
| ------------- | ------------- |
| 它是什么？   | 交互式数据可视化和 BI 工具    |
| 支持的 DSVM 版本      | Windows  |
| 典型用途      |  数据可视化和构建仪表板   |
| 如何使用和运行它      | 桌面快捷方式`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`()      |
| DSVM 上的相关工具      |   Visual Studio 2019, Visual Studio Code, Juno      |

