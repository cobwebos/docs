---
title: 从 GitHub 下载 Azure Stack 工具 | Microsoft Docs
description: 了解如何下载使用 Azure Stack 所需的工具。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 28F360AD-789A-488D-965F-FC6E6CCF3329
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: a5bc23ee6f986da80630371bafcd8ec80dde3577
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
---
# <a name="download-azure-stack-tools-from-github"></a>从 GitHub 下载 Azure Stack 工具

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

AzureStack-Tools 是托管 PowerShell 模块的 GitHub 存储库，可用于管理资源并将其部署到 Azure Stack。

## <a name="download-targets"></a>下载目标

您可以下载并使用这些 PowerShell 模块到 Azure 堆栈开发工具包中，或使用 VPN 连接到基于 Windows 的外部客户端。

## <a name="how-to-get-the-tools"></a>如何获取工具

若要获取这些工具，克隆 AzureStack 工具 GitHub 存储库或通过运行以下脚本，下载 AzureStack 工具文件夹：

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

AzureStack-Tools 存储库包含支持以下 Azure Stack 功能的 PowerShell 模块：

| 功能 | 说明 | 谁可以使用此模块？ |
| --- | --- | --- |
| [云功能](azure-stack-validate-templates.md) | 使用此模块可获取云的云功能。 例如，可以使用此模块来获取 Azure Stack 和 Azure 云的 API 版本、Azure 资源管理器资源、VM 扩展等云功能。 | 云管理员和用户。 |
| [Azure Stack 的资源管理器策略](azure-stack-policy-module.md) | 使用此模块可以配置版本和服务可用性与 Azure Stack 相同的 Azure 订阅或 Azure 资源组。 | 云管理员和用户 |
| [连接到 Azure Stack](azure-stack-connect-azure-stack.md) | 使用此模块可通过 PowerShell 连接到 Azure Stack 实例，并配置与 Azure Stack 的 VPN 连接。 | 云管理员和用户 |
| [模板验证程序](azure-stack-validate-templates.md) | 使用此模块可以验证是否可将现有或新的模板部署到 Azure Stack。 | 云管理员和用户 |

## <a name="next-steps"></a>后续步骤

* [配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)
* [通过 VPN 连接到 Azure Stack 开发工具包](azure-stack-connect-azure-stack.md)
