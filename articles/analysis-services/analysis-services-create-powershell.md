---
title: "使用 PowerShell 创建 Azure Analysis Services 服务器 | Microsoft Docs"
description: "了解如何使用 PowerShell 创建 Azure Analysis Services 服务器"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/01/2017
ms.author: owend
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 43700de27ca643bd699159b86fe80c2e45e4acc7
ms.contentlocale: zh-cn
ms.lasthandoff: 08/18/2017

---

# <a name="create-an-azure-analysis-services-server-by-using-powershell"></a>使用 PowerShell 创建 Azure Analysis Services 服务器

本快速入门介绍如何从命令行使用 PowerShell，以便在 Azure 订阅的 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)中创建 Azure Analysis Services 服务器。

本任务需要 Azure PowerShell 模块 4.0 或更高版本。 若要查找版本，请运行 ` Get-Module -ListAvailable AzureRM`。 若要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 

> [!NOTE]
> 创建服务器可能会导致新的计费服务。 有关详细信息，请参阅 [Analysis Services 定价](https://azure.microsoft.com/pricing/details/analysis-services/)。

## <a name="prerequisites"></a>先决条件
若要完成本快速入门，你需要：

* **Azure 订阅**：访问 [Azure 免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p/)以创建帐户。
* Azure Active Directory：订阅必须与 Azure Active Directory 租户相关联，且该目录中必须有一个帐户。 若要了解详细信息，请参阅[身份验证和用户权限](analysis-services-manage-users.md)。

## <a name="import-azurermanalysisservices-module"></a>导入 AzureRm.AnalysisServices 模块
若要在订阅中创建一台服务器，请使用 [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) 组件模块。 将 AzureRm.AnalysisServices 模块加载到 PowerShell 会话中。

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="sign-in-to-azure"></a>登录 Azure

使用 [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) 命令登录到 Azure 订阅。 按屏幕指令操作。

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>创建资源组
 
[Azure 资源组](../azure-resource-manager/resource-group-overview.md)是在其中以组的形式部署和管理 Azure 资源的逻辑容器。 创建服务器时，必须在订阅中指定资源组。 如果还没有资源组，可以使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 命令新建一个。 以下示例在美国西部区域创建一个名为 `myResourceGroup` 的资源组。

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "West US"
```

## <a name="create-a-server"></a>创建服务器

使用 [New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) 命令创建新的服务器。 以下示例在美国西部区域的 myResourceGroup 中的 D1 层创建名为 myServer 的服务器，并指定 philipc@adventureworks.com 为服务器管理员。

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myServer" -Location West US -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>清理资源

可以使用 [Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) 命令将服务器从订阅中删除。 若要继续完成本系列的其他快速入门和教程，请勿删除服务器。 以下示例删除在上一步创建的服务器。


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myServer" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>后续步骤
[使用 PowerShell 管理 Azure Analysis Services](analysis-services-powershell.md)   
[从 SSDT 部署模型](analysis-services-deploy.md)   
[在 Azure 门户中创建模型](analysis-services-create-model-portal.md)
