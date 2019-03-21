---
title: 无提示的 Azure 备份服务器 V2 安装
description: 使用 PowerShell 脚本可以无提示方式安装 Azure 备份服务器 V2。 这种类型安装也称为无人参与安装。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: raynew
ms.openlocfilehash: 66ed5765a91b607bc5b765926c5df87d13ff6a24
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58109842"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>运行 Azure 备份服务器的无人参与安装

了解如何运行 Azure 备份服务器的无人参与安装。

如果是安装 Azure 备份服务器 V1，则这些步骤不适用。

## <a name="install-backup-server"></a>安装备份服务器

1. 在承载 Azure 备份服务器 V2 或更高版本的服务器上，创建一个文本文件。 （可以在记事本或其他文本编辑器中创建该文件。）将该文件保存为 MABSSetup.ini。

2. 将以下代码粘贴在 MABSSetup.ini 文件中。 将括号 (\< \>) 内的文本替换为来自你环境的值。 以下文本是一个示例：

   ```
   [OPTIONS]
   UserName=administrator
   CompanyName=<Microsoft Corporation>
   SQLMachineName=localhost
   SQLInstanceName=<SQL instance name>
   SQLMachineUserName=administrator
   SQLMachinePassword=<admin password>
   SQLMachineDomainName=<machine domain>
   ReportingMachineName=localhost
   ReportingInstanceName=<reporting instance name>
   SqlAccountPassword=<admin password>
   ReportingMachineUserName=<username>
   ReportingMachinePassword=<reporting admin password>
   ReportingMachineDomainName=<domain>
   VaultCredentialFilePath=<vault credential full path and complete name>
   SecurityPassphrase=<passphrase>
   PassphraseSaveLocation=<passphrase save location>
   UseExistingSQL=<1/0 use or do not use existing SQL>
   ```

3. 保存文件。 然后在安装服务器上的提升的命令提示符处，输入以下命令：

   ```
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

可以将以下这些标志用于安装：</br>
/f：.ini 文件路径</br>
**/l**：日志路径</br>
**/i**：安装路径</br>
**/x**：卸载路径</br>

## <a name="next-steps"></a>后续步骤
安装备份服务器之后，了解如何准备服务器或开始保护工作负载。

- [准备备份服务器工作负载](backup-azure-microsoft-azure-backup.md)
- [使用备份服务器备份 VMware 服务器](backup-azure-backup-server-vmware.md)
- [使用备份服务器备份 SQL Server](backup-azure-sql-mabs.md)
- [将新式备份存储添加到备份服务器](backup-mabs-add-storage.md)
