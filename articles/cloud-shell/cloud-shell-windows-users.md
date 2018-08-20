---
title: 适用于 Windows 用户的 Azure Cloud Shell | Microsoft Docs
description: 适用于不熟悉 Linux 系统的用户指南
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: damaerte
ms.openlocfilehash: aad474195060c01a3f9d85e6f9037b568b0c16ad
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630380"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>适用于 Windows 用户的 Azure Cloud Shell 中的 PowerShell

2018 年 5 月，[宣布了](https://azure.microsoft.com/blog/pscloudshellrefresh/)对 Azure Cloud Shell 中的 PowerShell 的更改。
Azure Cloud Shell 中的 PowerShell 体验当前在 Linux 环境中运行 [PowerShell Core 6](https://github.com/powershell/powershell)。
进行此更改后，与 Windows PowerShell 体验相比，Cloud Shell 中的 PowerShell 体验可能会有些不同。

## <a name="file-system-case-sensitivity"></a>文件系统区分大小写

在 Windows 中，文件系统不区分大小写，而在 Linux 上，文件系统区分大小写。
以前，`file.txt` 和 `FILE.txt` 被视为同一个文件，但现在它们被视为不同的文件。
当在文件系统中进行 `tab-completing` 时，必须使用正确的大小写。
PowerShell 特定体验（例如 `tab-completing` cmdlet 名称、参数和值）不区分大小写。

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Windows PowerShell 别名与 Linux 实用程序

某些现有 PowerShell 别名与内置 Linux 命令具有相同的名称，例如 `cat`、`ls`、`sort`、`sleep`，等等。PowerShell Core 6 中已删除了与内置 Linux 命令冲突的别名。
下面是已删除的常用别名及其等效命令：  

|已删除的别名   |等效命令   |
|---|---|
|`cat`    | `Get-Content` |
|`curl`   | `Invoke-WebRequest` |
|`diff`   | `Compare-Object` |
|`ls`     | `dir` <br> `Get-ChildItem` |
|`mv`     | `Move-Item`   |
|`rm`     | `Remove-Item` |
|`sleep`  | `Start-Sleep` |
|`sort`   | `Sort-Object` |
|`wget`   | `Invoke-WebRequest` |

## <a name="persisting-home"></a>持久保留 $HOME

以前的用户只能在其云驱动器中持久保留脚本和其他文件。
现在，用户的 $HOME 目录在各个会话之间也将持久保留。

## <a name="powershell-profile"></a>PowerShell 配置文件

默认情况下，不会创建用户的 PowerShell 配置文件。
若要创建配置文件，请在 `$HOME/.config` 下创建 `PowerShell` 目录。

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

在 `$HOME/.config/PowerShell` 下，可以创建配置文件- `profile.ps1` 和/或 `Microsoft.PowerShell_profile.ps1`。

## <a name="whats-new-in-powershell-core-6"></a>PowerShell Core 6 中的新增功能

有关 PowerShell Core 6 中的新增功能的详细信息，请参考 [PowerShell 文档](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6)和 [PowerShell Core 入门](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/)博客文章。
