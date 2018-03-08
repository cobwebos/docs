---
title: "Azure Cloud Shell 限制 | Microsoft Docs"
description: "Azure Cloud Shell 的限制概述"
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: juluk
ms.openlocfilehash: 245e2e1ca52e7d3c5bd22d5f2569e3e8d7ae6671
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="limitations-of-azure-cloud-shell"></a>Azure Cloud Shell 的限制

Azure Cloud Shell 有以下已知限制：

## <a name="general-limitations"></a>一般限制

### <a name="system-state-and-persistence"></a>系统状态和持久性

提供 Cloud Shell 会话的计算机是暂时性的，在会话处于非活动状态 20 分钟后会被回收。 Cloud Shell 需要装载 Azure 文件共享。 因此，订阅必须能够设置存储资源才能访问 Cloud Shell。 其他注意事项包括：

* 使用装载的存储时，仅持久保存 `clouddrive` 目录中的修改。 在 Bash 中，`$Home` 目录也会持久保存。
* 仅可从[已分配区域](persisting-shell-storage.md#mount-a-new-clouddrive)内部装载 Azure 文件共享。
  * 在 Bash 中，运行 `env` 可以找到设置为 `ACC_LOCATION` 的区域。

### <a name="browser-support"></a>浏览器支持

Cloud Shell 支持最新版本的 Microsoft Edge、Microsoft Internet Explorer、Google Chrome、Mozilla Firefox 和 Apple Safari。 不支持专用模式下的 Safari。

### <a name="copy-and-paste"></a>复制和粘贴

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>对于一个给定的用户，只有一个 shell 可处于活动状态

不管是在 **Bash** 还是 **PowerShell** 中，用户每次只能启动一种类型的 shell。 但是，每次可以运行 Bash 或 PowerShell 的多个实例。 在 Bash 和 PowerShell 之间切换会导致 Cloud Shell 重启，从而终止现有会话。

### <a name="usage-limits"></a>使用限制

Cloud Shell 适用于交互式用例。 因此，任何长时间运行的非交互式会话都会在没有预警的情况下终止。

## <a name="bash-limitations"></a>Bash 限制

### <a name="user-permissions"></a>用户权限

权限设置为普通用户，不具有 sudo 访问权限。 不会保留 `$Home` 目录外部的任何安装。

### <a name="editing-bashrc"></a>编辑 .bashrc

编辑 .bashr 时要小心，执行这一操作可能导致 Cloud Shell 出现意外错误。

## <a name="powershell-limitations"></a>PowerShell 限制

### <a name="slow-startup-time"></a>启动速度缓慢

PowerShell in Azure Cloud Shell（预览版）最长可能需要 60 秒才能完成初始化。

### <a name="no-home-directory-persistence"></a>$Home 目录没有持久性

由任何应用程序（例如 git、vim，等等）写入 `$Home` 的数据不会在 PowerShell 会话之间持久保留。 有关解决方法，请[参阅此文](troubleshooting.md#powershell-troubleshooting)。

### <a name="default-file-location-when-created-from-azure-drive"></a>从 Azure 驱动器创建时的默认文件位置：

使用 PowerShell cmdlet，用户无法在 Azure 驱动器下创建文件。 当用户使用其他工具（如 vim 或 nano）创建新文件时，文件将默认保存到 C:\Users 文件夹。 

### <a name="gui-applications-are-not-supported"></a>不支持 GUI 应用程序

如果用户运行一条会创建 Windows 对话框的命令（例如 `Connect-AzureAD` 或 `Login-AzureRMAccount`），将看到如下所示的错误消息：`Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`。

## <a name="next-steps"></a>后续步骤

[Cloud Shell 故障排除](troubleshooting.md) <br>
[Bash 快速入门](quickstart.md) <br>
[PowerShell 快速入门](quickstart-powershell.md)
