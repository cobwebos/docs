---
title: 管理 Azure Stack 中的 API 版本配置文件 | Microsoft Docs
description: 了解 Azure Stack 中的 API 版本配置文件。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 8A336052-8520-41D2-AF6F-0CCE23F727B4
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 452ed1de0588b380747edaa44dd0cc3805c51392
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>管理 Azure Stack 中的 API 版本配置文件

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

API 配置文件指定 Azure 资源提供程序和 Azure REST 终结点的 API 版本。 在使用 API 配置文件的不同语言中，你可以创建自定义客户端。 每个客户端使用的 API 配置文件来为 Azure 堆栈联系相应的资源提供程序和 API 版本。 

你可以创建应用程序处理的 Azure 资源提供程序，而无需理清完全哪一个版本的每个资源提供程序 API 是与 Azure 堆栈兼容。 只需将对齐到配置文件; 应用程序SDK 将恢复为正确的 API 版本。


本主题将帮助你：
 - 理解 Azure 堆栈 API 配置文件。
 - 如何 API 配置文件可用于开发您的解决方案。
 - 若要查看有关特定于代码的指南的位置。

## <a name="summary-of-api-profiles"></a>API 配置文件的摘要

- API 配置文件用于表示一组的 Azure 资源提供程序和其 API 版本。
- 为开发人员能够跨多个 Azure 云创建模板创建 API 配置文件。 它们旨在满足对兼容且稳定的接口的需要。
- 配置文件将发布四次一年。
- 三个配置文件命名约定是：
    - **latest**  
        在 Azure 中发布最新的 API 版本。
    - **yyyy-mm-dd-hybrid**  
    发布节奏举行，此版本侧重于一致性和稳定性多个云。
    - **yyyy-mm-dd-profile**  
    位于之间最佳的稳定性和最新功能。

## <a name="azure-resource-manager-api-profiles"></a>Azure 资源管理器 API 配置文件

Azure 堆栈不使用在全球 Azure 中找到的 API 版本的最新版本。 在创建你自己的解决方案，你需要在与 Azure 堆栈兼容的 Azure 中查找每个资源提供程序的 API 版本。

而是比研究每个资源提供程序和支持的 Azure 堆栈的特定版本，你可以使用 API 配置文件。 配置文件指定一组资源提供程序和 API 版本。 在 SDK 中或使用 SDK，生成工具将恢复为配置文件中指定的目标 api 版本。 API 配置文件，你可以指定适用于整个模板的配置文件版本，然后在运行时，Azure 资源管理器选择正确版本的资源。

API 配置文件工作的工具可以使用 Azure 资源管理器，例如 PowerShell、 Azure CLI、 SDK 和 Microsoft Visual Studio 中提供的代码。 工具和 Sdk 可以使用配置文件读取的模块和库来生成应用程序时包含哪个版本。

例如，如果使用 PowerShell 创建存储帐户使用**Microsoft.Storage**支持 api 版本 2016年-03-30 和 api 版本 2015年-12-01 中使用 Microsoft.Compute 资源提供程序的 VM 的资源提供程序你需要查找 PowerShell 模块支持 2016年-03-30 存储空间的而且哪个模块支持 2015年-02-01 计算并安装它们。 相反，你可以使用配置文件。 使用 cmdlet * * 安装配置文件 * profilename * * *，和 PowerShell 加载正确版本的模块。

同样，当使用 Python SDK 来构建基于 Python 应用程序，你可以指定配置文件。 SDK 加载的资源提供程序已在你的脚本中指定正确的模块。

作为开发人员，你可以专注于编写你的解决方案。 而不是研究哪些 api 版本、 资源提供程序，以及哪些云协同，使用配置文件，并知道你的代码将在支持该配置文件的所有云之间工作。

## <a name="api-profile-code-samples"></a>API 配置文件的代码示例

你可以找到可帮助你通过使用配置文件与具有 Azure 堆栈首选语言集成你的解决方案的代码示例。 目前，你可以找到指南和示例的以下语言：

- **PowerShell**  
你可以使用**AzureRM.Bootstrapper**可通过 PowerShell 库，以获取使用 API 版本配置文件所需的 PowerShell cmdlet 的模块。  
有关信息，请参阅[使用 API 版本适用于 PowerShell 的配置文件](azure-stack-version-profiles-powershell.md)。
- **Azure CLI 2.0**  
你可以更新你的环境配置为使用 Azure 堆栈的特定 API 版本配置文件。  
有关信息请参阅[使用 API 版本配置文件为使用 Azure CLI 2.0](azure-stack-version-profiles-azurecli2.md)。
- **GO**  
在转到 SDK 中，配置文件是具有来自不同服务的不同版本的不同资源类型的组合。 配置文件均位于配置文件 / 路径中其版本**YYYY-月-日**格式。  
有关信息请参阅[使用 API 版本转到配置文件](azure-stack-version-profiles-go.md)。

## <a name="next-steps"></a>后续步骤
* [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)
* [配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)
* [查看有关资源提供程序 API 版本支持的配置文件详细信息](azure-stack-profiles-azure-resource-manager-versions.md)。
