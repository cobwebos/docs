---
title: 从 GitHub 下载 Azure Stack 工具 | Microsoft Docs
description: 了解如何下载使用 Azure Stack 所需的工具。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.author: mabrigg
ms.openlocfilehash: 9c1e4abe50b06db58a4ca05a99e1ae4a531b2294
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187445"
---
# <a name="download-azure-stack-tools-from-github"></a>从 GitHub 下载 Azure Stack 工具

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

AzureStack-Tools 是托管 PowerShell 模块的 GitHub 存储库，可用于管理资源并将其部署到 Azure Stack。

## <a name="download-targets"></a>下载目标

可以将这些 PowerShell 模块下载到 Azure Stack 开发工具包或使用 VPN 连接的基于 Windows 的外部客户端，并加以使用。

## <a name="how-to-get-the-tools"></a>如何获取工具

若要获取这些工具，请克隆 AzureStack-Tools GitHub 存储库，或运行以下脚本来下载 AzureStack-Tools 文件夹：

```PowerShell
# Change directory to the root directory
cd \

# Download the tools archive
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
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

AzureStack-Tools 存储库包含支持以下 Azure Stack 功能的 PowerShell 模块：

| 功能 | 说明 | 谁可以使用此模块？ |
| --- | --- | --- |
| [云功能](https://github.com/Azure/AzureStack-Tools/tree/master/CloudCapabilities) | 使用此模块可获取云的云功能。 例如，可以使用此模块来获取 Azure Stack 和 Azure 云的 API 版本、Azure 资源管理器资源、VM 扩展等云功能。 | 云管理员和用户。 |
| [Azure Stack 的资源管理器策略](azure-stack-policy-module.md) | 使用此模块可以配置版本和服务可用性与 Azure Stack 相同的 Azure 订阅或 Azure 资源组。 | 云管理员和用户 |
| [连接到 Azure Stack](azure-stack-connect-azure-stack.md) | 使用此模块可通过 PowerShell 连接到 Azure Stack 实例，并配置与 Azure Stack 的 VPN 连接。 | 云管理员和用户 |
| [模板验证程序](azure-stack-validate-templates.md) | 使用此模块可以验证是否可将现有或新的模板部署到 Azure Stack。 | 云管理员和用户 |

## <a name="next-steps"></a>后续步骤

- [配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)
- [通过 VPN 连接到 Azure Stack 开发工具包](azure-stack-connect-azure-stack.md)
