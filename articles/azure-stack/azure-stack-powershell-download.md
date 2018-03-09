---
title: "从 GitHub 下载 Azure 堆栈工具 |Microsoft 文档"
description: "了解如何下载所需的使用 Azure 堆栈的工具。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: E4DF77FA-F468-42B5-B44F-F10ED8049171
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.openlocfilehash: 219fd8e4e164df8c3002044719a90a7be56a9edf
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="download-azure-stack-tools-from-github"></a>从 GitHub 下载 Azure 堆栈工具

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

**AzureStack 工具**是承载 PowerShell 模块，用于管理和将资源部署到 Azure 堆栈的 GitHub 存储库。 如果你打算建立 VPN 连接，你可以下载这些 PowerShell 模块，到 Azure 堆栈开发工具包中，或基于 Windows 的外部客户端。 若要获取这些工具，克隆的 GitHub 存储库或下载**AzureStack 工具**通过运行以下脚本的文件夹：

```PowerShell
# Change directory to the root directory. 
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```

## <a name="functionality-provided-by-the-modules"></a>模块提供的功能

**AzureStack 工具**存储库包含 PowerShell 模块，为 Azure 堆栈支持以下功能：  

| 功能 | 说明 | 哪些用户可以使用此模块？ |
| --- | --- | --- |
| [云功能](user/azure-stack-validate-templates.md) | 使用此模块用于获得云的云功能。 例如，通过使用此模块，你可以获取云功能，例如 API 版本和 Azure 资源管理器资源。 通过使用此模块，还可以获取的 VM 扩展 Azure 堆栈和 Azure 云。 | 云操作员和用户 |
| [Azure 堆栈计算管理](azure-stack-add-vm-image.md) | 此模块用于添加或删除从 Azure 堆栈应用商店 VM 映像。 | 云操作员 |
| [Azure 堆栈的基础结构管理](https://github.com/Azure/AzureStack-Tools/blob/master/Infrastructure/README.md) | 使用此模块来管理 Azure 堆栈基础结构 Vm、 警报、 更新和等等。 |  云操作员|
| [Azure 堆栈的资源管理器策略](user/azure-stack-policy-module.md) | 使用此模块将使用作为 Azure 堆栈的相同的版本控制和服务可用性的 Azure 订阅或 Azure 资源组。 | 云操作员和用户 |
| [注册 Azure](azure-stack-register.md) | 此模块用于向 Azure 注册你开发工具包实例。 注册之后，你可以从 Azure 下载应用商店项，并在 Azure 堆栈中使用它们。 | 云操作员 |
| [Azure 堆栈部署](azure-stack-run-powershell-script.md) | 使用此模块准备 Azure 堆栈主计算机来部署和使用 Azure 堆栈虚拟硬盘 (VHD) 映像来重新部署。 | 云操作员|
| [连接到 Azure 的堆栈](azure-stack-connect-powershell.md) | 若要连接到 Azure 堆栈实例通过 PowerShell 和配置 VPN 连接到 Azure 堆栈，请使用此模块。 | 云操作员和用户 |
| [Azure 堆栈服务管理](azure-stack-create-offer.md) | 此模块用于跨计算、 Azure 存储空间、 网络和密钥保管库服务使用不受限制的配额创建默认租户服务。   | 云操作员|
| [模板验证程序](user/azure-stack-validate-templates.md) | 使用此模块可以验证是否的现有或新的模板可部署到 Azure 堆栈。 | 云操作员和用户|


## <a name="next-steps"></a>后续步骤
* [配置 Azure 堆栈用户 PowerShell 环境](user/azure-stack-powershell-configure-user.md)   
* [通过 VPN 连接到 Azure 的堆栈开发工具包](azure-stack-connect-azure-stack.md)  
