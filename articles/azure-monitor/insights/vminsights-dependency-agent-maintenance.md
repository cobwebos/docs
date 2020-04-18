---
title: 如何升级 VM 依赖项代理的 Azure 监视器
description: 本文介绍如何使用命令行、设置向导和其他方法升级 VM 依赖项代理的 Azure 监视器。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/16/2020
ms.openlocfilehash: 01dd8422658aa0c8982733e48782efd27c1bf5be
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617855"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>如何升级 VM 依赖项代理的 Azure 监视器

初始部署 VM 依赖项代理的 Azure 监视器后，将发布包含错误修复或支持新功能或功能的更新。  本文可帮助您了解可用的方法以及如何手动或通过自动化执行升级。

## <a name="upgrade-options"></a>升级选项 

Windows 和 Linux 的依赖项代理可以手动或自动升级到最新版本，具体取决于计算机运行的部署方案和环境。 可以使用以下方法来升级代理。

|环境 |安装方法 |升级方法 |
|------------|--------------------|---------------|
|Azure VM | [适用于 Windows](../../virtual-machines/extensions/agent-dependency-windows.md)和[Linux](../../virtual-machines/extensions/agent-dependency-linux.md)的依赖代理 VM 扩展 | 默认情况下代理会自动升级，除非你通过将 *autoUpgradeMinorVersion* 属性设置为 **false** 来将 Azure 资源管理器模板配置为选择退出。 禁用自动升级的次要版本的升级，以及主要版本升级遵循相同的方法 - 卸载并重新安装扩展。 |
| 自定义 Azure VM 映像 | 手动安装 Windows/Linux 的依赖项代理 | 将 VM 更新到最新版本的代理需要从命令行运行 Windows 安装程序包或 Linux 自解压和可安装的 shell 脚本包。|
| 非 Azure VM | 手动安装 Windows/Linux 的依赖项代理 | 将 VM 更新到最新版本的代理需要从命令行运行 Windows 安装程序包或 Linux 自解压和可安装的 shell 脚本包。 |

## <a name="upgrade-windows-agent"></a>升级 Windows 代理 

要将 Windows VM 上的代理更新到未使用依赖项代理 VM 扩展程序安装的最新版本，请从命令提示符、脚本或其他自动化解决方案运行，或者使用安装依赖代理-Windows.exe 安装程序向导运行。  

你可以[从这里](https://aka.ms/dependencyagentwindows)下载最新版本的Windows代理。

### <a name="using-the-setup-wizard"></a>使用设置向导

1. 使用具有管理权限的帐户登录到计算机。

2. 执行**安装独立代理-Windows.exe**以启动安装程序向导。
   
3. 按照**依赖项代理安装程序**向导卸载依赖项代理的早期版本，然后安装最新版本。


### <a name="from-the-command-line"></a>从命令行

1. 使用具有管理权限的帐户登录到计算机。

2. 运行以下命令。

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    如果`/RebootMode=manual`某些进程使用以前版本中的文件并锁定这些文件，则该参数可防止升级自动重新启动计算机。 

3. 要确认升级成功，请查看`install.log`以获取详细的设置信息。 日志目录为 *%Programfiles%\Microsoft Dependency Agent\logs*。

## <a name="upgrade-linux-agent"></a>升级 Linux 代理 

支持从 Linux 上依赖项代理的早期版本进行升级，并按照与新安装相同的命令执行。

你可以[从这里](https://aka.ms/dependencyagentlinux)下载最新版本的Linux代理。

1. 使用具有管理权限的帐户登录到计算机。

2. 将以下命令作为 root`sh InstallDependencyAgent-Linux64.bin -s`运行。 

如果 Dependency Agent 无法启动，请检查日志以获取详细的错误信息。 在 Linux 代理上，日志目录是 */var/opt/microsoft/依赖代理/日志*。 

## <a name="next-steps"></a>后续步骤

如果要停止监视 VM 一段时间或完全删除 VM 的 Azure 监视器，请参阅[禁用 Azure 监视器中 VM 中的 VM 监视](vminsights-optout.md)。
