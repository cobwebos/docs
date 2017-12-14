---
title: "从 GitHub 下载 Azure 堆栈工具 |Microsoft 文档"
description: "了解如何下载需要来处理 Azure 堆栈的工具。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 28F360AD-789A-488D-965F-FC6E6CCF3329
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: mabrigg
ms.openlocfilehash: d4f8a8d73f8e2ea321cb6cc1deda2301033b249d
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="download-azure-stack-tools-from-github"></a>从 GitHub 下载 Azure 堆栈工具

AzureStack 工具是承载可用于管理和将资源部署到 Azure 堆栈的 PowerShell 模块的 GitHub 存储库。 你可以下载并使用这些 PowerShell 模块到 Azure 堆栈开发工具包中，或基于 windows 的外部客户端，如果你打算建立 VPN 连接。 若要获取这些工具，克隆的 GitHub 存储库或通过运行以下脚本，下载 AzureStack 工具文件夹：

```PowerShell
# Change directory to the root directory 
cd \

# Download the tools archive
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory
cd AzureStack-Tools-master

```

## <a name="functionalities-provided-by-the-modules"></a>模块提供的功能

AzureStack 工具存储库包含 PowerShell 模块，为 Azure 堆栈支持以下功能：  

| 功能 | 说明 | 哪些用户可以使用此模块？ |
| --- | --- | --- |
| [云功能](azure-stack-validate-templates.md) | 使用此模块用于获得云的云功能。 例如，你可以获取云功能，例如 API 版本、 Azure 资源管理器资源、 VM 扩展等 Azure 堆栈和使用此模块的 Azure 云。 | 云管理员和用户。 |
| [Azure 堆栈的资源管理器策略](azure-stack-policy-module.md) | 使用此模块将使用作为 Azure 堆栈的相同的版本控制和服务可用性的 Azure 订阅或 Azure 资源组。 | 云管理员和用户 |
| [连接到 Azure 的堆栈](azure-stack-connect-azure-stack.md) | 若要连接到 Azure 堆栈实例通过 PowerShell 和配置 VPN 连接到 Azure 堆栈，请使用此模块。 | 云管理员和用户 |
| [模板验证程序](azure-stack-validate-templates.md) | 使用此模块可以验证是否的现有或新的模板可部署到 Azure 堆栈。 | 云管理员和用户 |


## <a name="next-steps"></a>后续步骤
* [配置 Azure 堆栈用户 PowerShell 环境](azure-stack-powershell-configure-user.md)   
* [通过 VPN 连接到 Azure 的堆栈开发工具包](azure-stack-connect-azure-stack.md)  
