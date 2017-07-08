---
title: "适用于 Azure Web 应用的基于 Azure Resource Manager 的 PowerShell 命令 | Microsoft Docs"
description: "了解如何使用基于 Azure Resource Manager 的新 PowerShell 命令来管理 Azure Web 应用。"
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: 4231fbba-61e5-4f92-b958-531c066fb87f
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: aelnably
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 8d574f051a327ba0409e6f25a5886af673d3d5e0
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017


---
# <a name="using-azure-resource-manager-based-powershell-to-manage-azure-web-apps"></a>使用基于 Azure Resource Manager 的 PowerShell 来管理 Azure Web 应用
> [!div class="op_single_selector"]
> * [Azure CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
> * [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)
> 
> 

Microsoft Azure PowerShell 版本 1.0.0 中添加了新命令，可让用户使用基于 Azure Resource Manager 的 PowerShell 命令来管理 Web 应用。

若要了解如何管理资源组，请参阅[将 Azure PowerShell 与 Azure Resource Manager 搭配使用](../powershell-azure-resource-manager.md)。 

若要了解 PowerShell cmdlet 的完整参数和选项列表，请参阅 [Web 应用基于 Azure Resource Manager 的 PowerShell Cmdlet 的完整 Cmdlet 参考](https://msdn.microsoft.com/library/mt619237.aspx)

## <a name="managing-app-service-plans"></a>管理应用服务计划
### <a name="create-an-app-service-plan"></a>创建应用服务计划
若要创建应用服务计划，请使用 **New-AzureRmAppServicePlan** cmdlet。

以下是各种参数的说明：

* **Name**：应用服务计划的名称。
* **Location**：服务计划位置。
* **ResourceGroupName**：包含新创建的应用服务计划的资源组。
* **Tier**：所需的定价层（默认值是“免费”，其他选项包括“共享”、“基本”、“标准”和“高级”）。
* **WorkerSize**：辅助角色大小（如果 Tier 参数指定为“基本”、“标准”或“高级”，则默认值为“小”。 其他选项包括“中”和“大”。）
* **NumberofWorkers**：应用服务计划中的辅助角色数目（默认值为 1）。 

使用此 cmdlet 的示例：

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### <a name="create-an-app-service-plan-in-an-app-service-environment"></a>在应用服务环境中创建应用服务计划
若要在应用服务环境中创建应用服务计划，可以将相同的 **New-AzureRmAppServicePlan** 命令与额外的参数搭配使用，以指定 ASE 的名称和 ASE 的资源组名称。

使用此 cmdlet 的示例：

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -AseName constosoASE -AseResourceGroupName contosoASERG -Tier Premium -WorkerSize Large -NumberofWorkers 10

若要详细了解应用服务环境，请查看 [Introduction to App Service Environment](app-service-app-service-environment-intro.md)（应用服务环境简介）

### <a name="list-existing-app-service-plans"></a>列出现有的应用服务计划
若要列出现有的应用服务计划，请使用 **Get-AzureRmAppServicePlan** cmdlet。

若要列出订阅之下的所有应用服务计划，请使用： 

    Get-AzureRmAppServicePlan

若要列出特定资源组之下的所有应用服务计划，请使用：

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

若要获取特定的应用服务计划，请使用：

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### <a name="configure-an-existing-app-service-plan"></a>配置现有的应用服务计划
若要更改现有应用服务计划的设置，请使用 **Set-AzureRmAppServicePlan** cmdlet。 可以更改层、辅助角色大小和辅助角色数目 

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### <a name="scaling-an-app-service-plan"></a>缩放应用服务计划
若要缩放现有的应用服务计划，请使用：

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### <a name="changing-the-worker-size-of-an-app-service-plan"></a>更改应用服务计划的辅助角色大小
若要更改现有应用服务计划中的辅助角色大小，请使用：

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### <a name="changing-the-tier-of-an-app-service-plan"></a>更改应用服务计划的层
若要更改现有应用服务计划的层，请使用：

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### <a name="delete-an-existing-app-service-plan"></a>删除现有的应用服务计划
若要删除现有应用服务计划，需先移动或删除所有分配的 Web 应用， 然后即可使用 **Remove-AzureRmAppServicePlan** cmdlet 删除应用服务计划。

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## <a name="managing-app-service-web-apps"></a>管理应用服务 Web 应用
### <a name="create-a-web-app"></a>创建 Web 应用
若要创建 Web 应用，请使用 **New-AzureRmWebApp** cmdlet。

以下是各种参数的说明：

* **Name**：Web 应用的名称。
* **AppServicePlan**：用于托管 Web 应用的服务计划的名称。
* **ResourceGroupName**：托管应用服务计划的资源组。
* **Location**：Web 应用的位置。

使用此 cmdlet 的示例：

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### <a name="create-a-web-app-in-an-app-service-environment"></a>在应用服务环境中创建 Web 应用
在应用服务环境 (ASE) 中创建 Web 应用。 将相同的 **New-AzureRmWebApp** 命令与额外的参数搭配使用，以指定 ASE 名称和 ASE 所属的资源组名称。

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

若要详细了解应用服务环境，请查看 [Introduction to App Service Environment](app-service-app-service-environment-intro.md)（应用服务环境简介）

### <a name="delete-an-existing-web-app"></a>删除现有的 Web 应用
若要删除现有的 Web 应用，可以使用 **Remove-AzureRmWebApp** cmdlet，并且需要指定 Web 应用名称和资源组名称。

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### <a name="list-existing-web-apps"></a>列出现有的 Web 应用
若要列出现有的 Web 应用，请使用 **Get-AzureRmWebApp** cmdlet。

若要列出订阅之下的所有 Web 应用，请使用：

    Get-AzureRmWebApp

若要列出特定资源组之下的所有 Web 应用，请使用：

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

若要获取特定的 Web 应用，请使用：

    Get-AzureRmWebApp -Name ContosoWebApp

### <a name="configure-an-existing-web-app"></a>配置现有的 Web 应用
若要更改现有 Web 应用的设置和配置，请使用 **Set-AzureRmWebApp** cmdlet。 有关完整的参数列表，请查看 [Cmdlet 参考链接](https://msdn.microsoft.com/library/mt652487.aspx)

示例 (1)：使用此 cmdlet 来更改连接字符串

    $connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

示例 (2)：添加或更改应用设置

    $appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


示例 (3)：将 Web 应用设置为在 64 位模式下运行

    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### <a name="change-the-state-of-an-existing-web-app"></a>更改现有 Web 应用的状态
#### <a name="restart-a-web-app"></a>重新启动 Web 应用
若要重新启动 Web 应用，必须指定 Web 应用的名称和资源组。

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>停止 Web 应用
若要停止 Web 应用，必须指定 Web 应用的名称和资源组。

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>启动 Web 应用
若要启动 Web 应用，必须指定 Web 应用的名称和资源组。

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>管理 Web 应用发布配置文件
每个 Web 应用都有可用于发布应用的发布配置文件，并且可对发布配置文件执行多项操作。

#### <a name="get-publishing-profile"></a>获取发布配置文件
若要获取 Web 应用的发布配置文件，请使用：

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

此命令会将发布配置文件回显至命令行，以及将发布配置文件输出至文本文件。

#### <a name="reset-publishing-profile"></a>重置发布配置文件
若要同时对 Web 应用的 FTP 和 Web 部署重置发布密码，请使用：

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-certificates"></a>管理 Web 应用证书
若要了解如何管理 Web 应用证书，请参阅[使用 PowerShell 创建 SSL 证书绑定](app-service-web-app-powershell-ssl-binding.md)

### <a name="next-steps"></a>后续步骤
* 若要了解 Azure Resource Manager PowerShell 支持，请参阅[将 Azure PowerShell 与 Azure Resource Manager 搭配使用](../powershell-azure-resource-manager.md)。
* 若要了解应用服务环境，请参阅 [Introduction to 应用服务Environment（应用服务环境简介）。](app-service-app-service-environment-intro.md)
* 若要了解如何使用 PowerShell 管理应用服务 SSL 证书，请参阅[使用 PowerShell 创建 SSL 证书绑定](app-service-web-app-powershell-ssl-binding.md)。
* 若要了解适用于 Azure Web 应用的基于 Azure Resource Manager 的 PowerShell cmdlet 的完整列表，请参阅 [Azure Cmdlet Reference of Web Apps Azure Resource Manager PowerShell Cmdlets](https://msdn.microsoft.com/library/mt619237.aspx)（Web 应用 Azure Resource Manager PowerShell Cmdlet 的 Azure Cmdlet 参考）。
* * 若要了解如何使用 CLI 管理应用服务，请参阅 [Using Azure Resource Manager-Based XPlat CLI for Azure Web App](app-service-web-app-azure-resource-manager-xplat-cli.md)（使用基于 Azure Resource Manager 且适用于 Azure Web 应用的 XPlat CLI）。


