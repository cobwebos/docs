---
title: Azure Cloud Shell 限制 | Microsoft Docs
description: Azure Cloud Shell 的限制概述
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: damaerte
ms.openlocfilehash: 092dccab82326bb9983f11ff64fe50aee7b1084d
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951473"
---
# <a name="limitations-of-azure-cloud-shell"></a>Azure Cloud Shell 的限制

Azure Cloud Shell 有以下已知限制：

## <a name="general-limitations"></a>一般限制

### <a name="system-state-and-persistence"></a>系统状态和持久性

提供 Cloud Shell 会话的计算机是暂时性的，在会话处于非活动状态 20 分钟后会被回收。 Cloud Shell 需要装载 Azure 文件共享。 因此，订阅必须能够设置存储资源才能访问 Cloud Shell。 其他注意事项包括：

* 使用装载的存储时，仅持久保存 `$Home` 目录中的修改。
* 仅可从[已分配区域](persisting-shell-storage.md#mount-a-new-clouddrive)内部装载 Azure 文件共享。
  * 在 Bash 中，运行 `env` 可以找到设置为 `ACC_LOCATION` 的区域。

### <a name="browser-support"></a>浏览器支持

Cloud Shell 支持最新版本的 Microsoft Edge、Microsoft Internet Explorer、Google Chrome、Mozilla Firefox 和 Apple Safari。 不支持专用模式下的 Safari。

### <a name="copy-and-paste"></a>复制和粘贴

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>对于一个给定的用户，只有一个 shell 可处于活动状态

不管是在 **Bash** 还是 **PowerShell** 中，用户每次只能启动一种类型的 shell。 但是，每次可以运行 Bash 或 PowerShell 的多个实例。 使用菜单在 Bash 或 PowerShell 之间进行交换会导致 Cloud Shell 重新启动，这将终止现有会话。 或者，你可以通过键入 `bash`在 PowerShell 内运行 bash，并通过键入 `pwsh`在 bash 内运行 PowerShell。

### <a name="usage-limits"></a>使用限制

Cloud Shell 适用于交互式用例。 因此，任何长时间运行的非交互式会话都会在没有预警的情况下终止。

## <a name="bash-limitations"></a>Bash 限制

### <a name="user-permissions"></a>用户权限

权限设置为普通用户，不具有 sudo 访问权限。 不会保留 `$Home` 目录外部的任何安装。

### <a name="editing-bashrc-or-profile"></a>编辑. .bashrc 或 $PROFILE

在编辑 .bashrc 或 PowerShell $PROFILE 文件时要格外小心，这样做会导致 Cloud Shell 中出现意外错误。

## <a name="powershell-limitations"></a>PowerShell 限制

### <a name="azuread-module-name"></a>`AzureAD` 模块名称

`AzureAD` 模块名称当前为 `AzureAD.Standard.Preview`，该模块提供相同的功能。

### <a name="sqlserver-module-functionality"></a>`SqlServer` 模块功能

Cloud Shell 中包含的 `SqlServer` 模块仅具有对 PowerShell Core 的预发布版本支持。 具体而言，`Invoke-SqlCmd` 尚不可用。

### <a name="default-file-location-when-created-from-azure-drive"></a>从 Azure 驱动器创建时的默认文件位置：

使用 PowerShell cmdlet，用户无法在 Azure：驱动器下创建文件。 当用户使用其他工具（如 vim 或 nano）创建新文件时，文件将默认保存到 `$HOME`。 

### <a name="gui-applications-are-not-supported"></a>不支持 GUI 应用程序

如果用户运行的命令将创建一个 Windows 对话框，则会看到一条错误消息，例如： `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`。

### <a name="large-gap-after-displaying-progress-bar"></a>在显示进度栏后出现大间距型

如果用户执行了显示进度栏的操作（如在 `Azure:` 驱动器中完成的选项卡），则光标可能未正确设置，并且在进度栏之前出现间隔。

## <a name="next-steps"></a>后续步骤

[Cloud Shell 故障排除](troubleshooting.md) <br>
[Bash 快速入门](quickstart.md) <br>
[PowerShell 快速入门](quickstart-powershell.md)
