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
ms.date: 07/03/2018
ms.author: damaerte
ms.openlocfilehash: 5e318a0f64033aa0c4b306e547c11e1994afa229
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861019"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>适用于 Windows 用户的 Azure Cloud Shell 中的 PowerShell

2018 年 5 月，[宣布了](https://azure.microsoft.com/blog/pscloudshellrefresh/)对 Azure Cloud Shell 中的 PowerShell 的更改。  Azure Cloud Shell 中的 PowerShell 体验现在是 Linux 中的 PowerShell Core 6。
进行此更改后，Cloud Shell 中的 PowerShell 的某些方面可能不同于 Windows PowerShell 5.1 中的预期内容。

## <a name="case-sensitivity"></a>事例敏感性

在 Windows 中，文件系统不区分大小写。  在 Linux 中，文件系统区分大小写。
这意味着以前 `file.txt` 和 `FILE.txt` 被认为是同一个文件，现在它们被认为是不同的文件。
在文件系统中 `tab` 完成时，必须使用正确的大小写。  PowerShell 特定体验（例如 `tab` cmdlet）不区分大小写。 

## <a name="windows-powershell-alias-vs-linux-utilities"></a>Windows PowerShell 别名与 Linux 实用程序

Linux 中的现有命令（例如 `ls`、`sort` 和 `sleep`）优先于其 PowerShell 别名。  下面是常见的已删除别名以及等效命令：  

|已删除的别名   |等效命令   |
|---|---|
|`ls`     | `dir` <br> `Get-ChildItem` |
|`sort`   | `Sort-Object` |
|`sleep`  | `Start-Sleep` |

## <a name="persisting-home-vs-homeclouddrive"></a>保留 $home 与 $home\clouddrive

对于在其云驱动器中保留脚本和其他文件的用户，$HOME 目录现在可以跨会话保留。

## <a name="powershell-profile"></a>PowerShell 配置文件

默认情况下，不会创建 PowerShell 配置文件。  若要创建配置文件，请在 `$HOME/.config` 下创建 `PowerShell` 目录。  在 `$HOME/.config/PowerShell` 中，可以使用名称 `Microsoft.PowerShell_profile.ps1` 创建配置文件。

## <a name="whats-new-in-powershell-core-6"></a>PowerShell Core 6 中的新增功能

有关 PowerShell Core 6 中的新增功能的详细信息，请参考 [PowerShell 文档](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6)和 [PowerShell Core 入门](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/)博客文章
