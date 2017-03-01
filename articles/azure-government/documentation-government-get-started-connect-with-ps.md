---
title: "Azure 政府与 PowerShell 连接 | Microsoft Docs"
description: "有关使用 PowerShell 在 Azure 政府中管理订阅的信息"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: 47e5e535-baa0-457e-8c41-f9fd65478b38
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: 3a0594fa639a2ac34c53b2263dbf7dd2410cf16a
ms.lasthandoff: 02/21/2017


---



# <a name="connect-to-azure-government-with-powershell"></a>使用 PowerShell 连接到 Azure 政府
若要使用 Azure CLI，需要连接到 Azure 政府，而不是 Azure 公共版。 Azure CLI 可以用于通过脚本管理大型订阅或访问当前在 Azure 门户中不可用的功能。 如果已在 Azure 公共版中使用 PowerShell，它们之间的差别不大。  Azure 政府版的区别如下：

* 连接帐户
* 区域名称

> [!NOTE]
> 如果尚未使用 PowerShell，请查看 [Azure PowerShell 简介](/powershell/azureps-cmdlets-docs)。
> 
> 

当启动 PowerShell 时，需要通过指定环境参数指示 Azure PowerShell 连接到 Azure 政府版。  该参数确保 PowerShell 连接到正确的终结点。  当连接登录到帐户后，会确定终结点的集合。  不同的 API 需要不同版本的环境交换机：

| 连接类型 | 命令 |
| --- | --- |
| [Azure Active Directory（经典部署模型）](https://msdn.microsoft.com/library/dn708504.aspx)命令 |`Add-AzureAccount -Environment AzureUSGovernment` |
| [资源管理](https://msdn.microsoft.com/library/mt125356.aspx)命令 |`Login-AzureRmAccount -EnvironmentName AzureUSGovernment` |
| [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) 命令 |`Connect-MsolService -AzureEnvironment UsGovernment` |
| [Azure Active Directory（Resource Manager 部署模型）](https://msdn.microsoft.com/library/azure/mt757189.aspx)命令 |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` |
| [Azure CLI 命令行](../xplat-cli-install.md) |`azure login –environment "AzureUSGovernment"` |

当使用 New-AzureStorageContext 连接到存储帐户时，也可使用环境交换机并指定 AzureUSGovernment。

### <a name="determining-region"></a>确定区域
连接后，还有一个区别 - 用于定位服务的区域。  每个 Azure 云具有不同的区域。  可以在服务可用性页面上查看其列表。  通常将位置参数中的区域用于命令。

这里会有个 catch。  Azure 政府区域的格式设置与其公用名不同：

| 公用名 | 命令 |
| --- | --- |
| 美国政府弗吉尼亚州 |USGov Virginia |
| 美国政府爱荷华州 |USGov Iowa |

> [!NOTE]
> 使用位置参数时，“US”和“Gov”之间没有空格。
> 
> 

如果想要验证 Azure 政府版中可用区域，可以运行以下命令，并打印当前列表：

    Get-AzureLocation

如果对 Azure 上的可用环境感兴趣，可以运行：

    Get-AzureEnvironment

