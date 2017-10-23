---
title: "Azure Cloud Shell（预览版）限制 | Microsoft Docs"
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
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: fe325cf5fa5e3d4b0a188599de7308cf2008b458
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="limitations-of-azure-cloud-shell"></a>Azure Cloud Shell 的限制
Azure Cloud Shell 有以下已知限制：

## <a name="general-limitations"></a>一般限制

### <a name="system-state-and-persistence"></a>系统状态和持久性
提供 Cloud Shell 会话的计算机是暂时性的，在会话处于非活动状态 20 分钟后会被回收。 Cloud Shell 需要装载文件共享。 因此，订阅必须能够设置存储资源才能访问 Cloud Shell。 其他注意事项包括：
* 使用装载的存储时，仅持久保存 `clouddrive` 目录中的修改。 在 Bash 中，`$Home` 目录也会持久保存。
* 仅可从[已分配区域](persisting-shell-storage.md#mount-a-new-clouddrive)内部装载文件共享。
  * 在 Bash 中，运行 `env` 可以找到设置为 `ACC_LOCATION` 的区域。
* Azure 文件仅支持本地冗余存储和异地冗余存储帐户。

### <a name="browser-support"></a>浏览器支持
Cloud Shell 支持最新版本的 Microsoft Edge、Microsoft Internet Explorer、Google Chrome、Mozilla Firefox 和 Apple Safari。 不支持专用模式下的 Safari。

### <a name="copy-and-paste"></a>复制和粘贴

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>对于一个给定的用户，只有一个 shell 可处于活动状态
不管是在 **Bash** 还是 **PowerShell** 中，用户每次只能启动一种类型的 shell。 但是，每次可以运行 Bash 或 PowerShell 的多个实例。 在 Bash 和 PowerShell 之间切换会导致 Cloud Shell 重启，从而终止现有会话。

### <a name="usage-limits"></a>使用限制
Cloud Shell 适用于交互式用例。 因此，任何长时间运行的非交互式会话都会在没有预警的情况下终止。

## <a name="bash-limitations"></a>Bash 限制

### <a name="user-permissions"></a>用户权限
权限设置为普通用户，不具有 sudo 访问权限。 不会保留 `$Home` 目录外部的任何安装。
尽管 `clouddrive` 目录内的某些命令（如 `git clone`）不具有适当权限，但 `$Home` 目录具有相应权限。

### <a name="editing-bashrc"></a>编辑 .bashrc
编辑 .bashr 时要小心，执行这一操作可能导致 Cloud Shell 出现意外错误。 

### <a name="bashhistory"></a>.bash_history
由于 Cloud Shell 会话中断或并发会话，bash 命令的历史记录可能不一致。

## <a name="powershell-limitations"></a>PowerShell 限制

### <a name="slow-startup-time"></a>启动速度缓慢
在预览期，Azure Cloud Shell 中的 PowerShell 最长可能需要 60 秒才能完成初始化。

### <a name="no-home-directory-persistence"></a>$Home 目录没有持久性
由任何应用程序（例如 git、vim，等等）写入 $Home 的数据不会持久保存在不同的 PowerShell 会话中。  有关解决方法，请[参阅此文](troubleshooting.md#powershell-resolutions)。

### <a name="error-if-azurerm-module-is-updated-from-powershellgallery"></a>如果从 PowerShellGallery 更新 AzureRM 模块，则出错
如果将 AzureRM 模块更新为最新版本 (4.4.0)，在 Cloudshell 启动时可能会看到以下错误。
``` powershell
VERBOSE: Authenticating to Azure ...
Import-Module : Method 'RemoveUser' in type 'Microsoft.Azure.PSCloudConsole.ADAuth.ADAuthFactory' from assembly 'Microsoft.Azure.PSCloudConsole.ADAuth, Version=0.0.0.0,
Culture=neutral, PublicKeyToken=null' does not have an implementation.
At C:\Program Files\WindowsPowerShell\Modules\PSCloudShellADAuth\PSCloudShellADAuth.psm1:12 char:1
+ Import-Module -Name $AdAuthModulePath -PassThru -Force
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Import-Module], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.PowerShell.Commands.ImportModuleCommand

Unable to find type [Microsoft.Azure.PSCloudConsole.ADAuth.ADAuthFactory].
At C:\Users\ContainerAdministrator\PSCloudShellStartup.ps1:94 char:9
+         [Microsoft.Azure.PSCloudConsole.ADAuth.ADAuthFactory]::Update ...
+         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (Microsoft.Azure...h.ADAuthFactory:TypeName) [], RuntimeException
    + FullyQualifiedErrorId : TypeNotFound
```

## <a name="next-steps"></a>后续步骤
[Cloud Shell 故障排除](troubleshooting.md) <br>
[Bash 快速入门](quickstart.md) <br>
[PowerShell 快速入门](quickstart-powershell.md)
