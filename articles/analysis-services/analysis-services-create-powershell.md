---
title: 快速入门 - 使用 PowerShell 创建 Azure Analysis Services 服务器 | Microsoft Docs
description: 了解如何使用 PowerShell 创建 Azure Analysis Services 服务器
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9149f15d0503b9a39ac67d9c3f6fc1ddde7e03bd
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952760"
---
# <a name="quickstart-create-a-server---powershell"></a>快速入门：创建服务器 - PowerShell

本快速入门介绍如何从命令行使用 PowerShell，以便在 Azure 订阅中创建 Azure Analysis Services 服务器。

## <a name="prerequisites"></a>先决条件

- **Azure 订阅**：访问 [Azure 免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p/)以创建帐户。
- **Azure Active Directory**：订阅必须与 Azure Active Directory 租户相关联，且该目录中必须有一个帐户。 若要了解详细信息，请参阅[身份验证和用户权限](analysis-services-manage-users.md)。
- **Azure PowerShell 模块 4.0 版或更高版本**。 若要查找版本，请运行 ` Get-Module -ListAvailable AzureRM`。 若要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

## <a name="import-azurermanalysisservices-module"></a>导入 AzureRm.AnalysisServices 模块

若要在订阅中创建一台服务器，请使用 [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) 组件模块。 将 AzureRm.AnalysisServices 模块加载到 PowerShell 会话中。

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="log-in-to-azure"></a>登录 Azure

使用 [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) 命令登录到 Azure 订阅。 按屏幕指令操作。

```powershell
Connect-AzureRmAccount
```

## <a name="create-a-resource-group"></a>创建资源组

[Azure 资源组](../azure-resource-manager/resource-group-overview.md)是在其中以组的形式部署和管理 Azure 资源的逻辑容器。 创建服务器时，必须在订阅中指定资源组。 如果还没有资源组，可以使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 命令新建一个。 以下示例在美国西部区域创建一个名为 `myResourceGroup` 的资源组。

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>创建服务器

使用 [New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) 命令创建新的服务器。 以下示例在美国西部区域的 myResourceGroup 中的 D1（免费）层创建名为 myServer 的服务器，并指定 philipc@adventureworks.com 为服务器管理员。

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>清理资源

可以使用 [Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver) 命令将服务器从订阅中删除。 若要继续完成本系列的其他快速入门和教程，请勿删除服务器。 以下示例删除在上一步创建的服务器。


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 PowerShell 在 Azure 订阅中创建服务器。 创建服务器后，可以通过配置（可选）的服务器防火墙对其进行保护。 还可以直接从门户将基本示例数据模型添加到服务器。 拥有一个示例模型有助于了解如何配置模型数据库角色和测试客户端连接。 若要了解更多信息，请继续学习有关添加示例模型的教程。

> [!div class="nextstepaction"]
> [快速入门：配置服务器防火墙 - 门户](analysis-services-qs-firewall.md)      
> [!div class="nextstepaction"]
> [教程：将示例模型添加到服务器](analysis-services-create-sample-model.md)