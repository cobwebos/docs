---
title: "Azure Cloud Shell（预览版）中的 PowerShell 功能 | Microsoft Docs"
description: "Azure Cloud Shell 中的 PowerShell 功能概述"
services: Azure
documentationcenter: 
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: damaerte
ms.openlocfilehash: 1113589507934d814bc8924267c07c99f8864657
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2018
---
# <a name="features--tools-for-powershell-in-azure-cloud-shell-preview"></a>PowerShell in Azure Cloud Shell（预览版）的功能和工具

[!include [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> 此外还提供 [Bash](features.md) 的功能和工具。

PowerShell in Cloud Shell（预览版）在 `Windows Server 2016` 上运行。

## <a name="features"></a>功能

### <a name="secure-automatic-authentication"></a>安全自动身份验证

PowerShell in Cloud Shell（预览版）安全并自动地验证帐户对 Azure PowerShell 的访问权限。

### <a name="files-persistence-across-sessions"></a>不同会话中的文件持久性

要在会话之间保存文件，Cloud Shell 会指导完成在首次启动时附加 Microsoft Azure 文件共享。
完成后，Cloud Shell 会自动为所有将来的会话附加存储（装载为 `$home\clouddrive`）。
由于 Cloud Shell 的每个请求分配临时计算机，因此，不同的会话不会持久保存 `$home\clouddrive` 外部的文件以及计算机状态。

[详细了解如何将 Azure 文件共享附加到 Cloud Shell](persisting-shell-storage-powershell.md)。

### <a name="azure-drive-azure"></a>Azure 驱动器 (Azure:)

PowerShell in Cloud Shell（预览版）启动后，用户会进入 Azure 驱动器 (`Azure:`) 路径下。
使用 Azure 驱动器可以轻松发现和浏览 Azure 资源，例如计算、网络、存储等，类似于文件系统导航。
可以继续使用熟悉的 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure) 来管理这些资源。
直接在 Azure 门户中或者通过 Azure PowerShell cmdlet 对 Azure 资源所做的任何更改会立即反映在 Azure 驱动器中。

![](media/features-powershell/azure-drive.png)

#### <a name="contextual-awareness"></a>上下文感知

- **资源组范围**：在 Azure 驱动器 (`Azure:`) 中资源组路径的上下文内，资源组名称会自动传递到 Azure PowerShell cmdlet。

    ![](media/features-powershell/resource-group-autocomplete.png)

- **Get-AzureRmCommand**：此 cmdlet 返回适用于 Azure 驱动器 (`Azure:`) 位置上下文的命令列表。 例如，如果用户在 `Azure:\<subscription name>\StorageAccounts` 下，此 cmdlet 仅显示存储相关的命令

    ![](media/features-powershell/get-azurermcommand.png)

### <a name="rich-powershell-script-editing"></a>丰富格式的 PowerShell 脚本编辑

使用 VIM 编辑 PowerShell 文件 (`.ps1,.psm1,.psd1`) 时，语法会自动突出显示，并支持 IntelliSense。
IntelliSense 支持是通过可与 [PowerShell 编辑器服务](https://github.com/PowerShell/PowerShellEditorServices)的本地实例交互的 vim-plugin 实现的。

> [!TIP]
> 使用 `TAB` 可填写 (IntelliSense) 命令名称、参数名称和参数值（如果适用）。

![](media/features-powershell/powershell-editing-vim.png)

### <a name="extensible-model"></a>可扩展的模型

使用 [PowerShellGet](https://docs.microsoft.com/powershell/module/powershellget)，可以轻松地从[PowerShell 库](https://www.powershellgallery.com)安装（和更新）自定义模块与脚本。
安装完成后，模块会自动持久保存在不同的 Cloud Shell 会话中。

> [!TIP]
> 用户安装的模块保存在 `$Home\CloudDrive\.pscloudshell\WindowsPowerShell` 文件夹中。 在用户的 documents 文件夹 (`$home\Documents\WindowsPowerShell`) 中创建此文件夹的符号链接。

![](media/features-powershell/powershellget-module.png)

### <a name="management-of-guest-vms"></a>来宾 VM 的管理

使用两个内置命令 - `Enter-AzureRmVM` 和 `Invoke-AzureRmVMCommand`，可以远程管理 Azure VM。
这些命令构建在 PowerShell 远程处理的基础之上，需要 PowerShell 与 Azure VM 建立连接。

## <a name="tools"></a>工具

|**类别**    |**Name**                                 |
|----------------|-----------------------------------------|
|Azure 工具     |[Azure PowerShell (5.1.1)](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-5.1.1)<br> [Azure CLI (2.0.25)](https://docs.microsoft.com/cli/azure/overview)|
|文本编辑器    |vim<br> nano                             |
|包管理器 |PowerShellGet<br> PackageManagement<br> npm<br> pip |
|源代码管理  |git                                      |
|数据库       |[SqlServer 模块](https://www.powershellgallery.com/packages/SqlServer)<br> [sqlcmd 实用工具](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|测试工具      |Pester                                   |

## <a name="language-support"></a>语言支持

|语言|**版本**|
|------------|-----------|
|.NET        |4.6        |
|Node.js     |6.10       |
|PowerShell  |5.1 和 [6.0 (beta)](https://github.com/PowerShell/powershell/releases)       |
|Python      |2.7        |

## <a name="next-steps"></a>后续步骤

[使用 PowerShell in Cloud Shell（预览版）快速入门](quickstart-powershell.md)

[了解 Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
