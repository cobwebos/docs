---
title: 使用 Log Analytics 收集 Azure PaaS 资源指标 | Microsoft Docs
description: 了解如何使用 PowerShell 启用 Azure PaaS 资源指标收集，以便在 Log Analytics 中保留和分析指标。
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/13/2017
ms.author: magoedte
ms.component: na
ms.openlocfilehash: b44a6627ab12c8a4ad21e7beded7c5fd2c2e1d39
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128456"
---
# <a name="configure-collection-of-azure-paas-resource-metrics-with-log-analytics"></a>使用 Log Analytics 配置 Azure PaaS 资源指标的收集

Azure SQL 和网站（Web 应用）等 Azure 平台即服务 (PaaS) 资源原生可向 Log Analytics 发出性能指标数据。 用户可以使用此脚本针对已在特定资源组中部署的或跨整个订阅部署的 PaaS 资源启用指标日志记录。 

目前，无法通过 Azure 门户为 PaaS 资源启用指标日志记录。 因此需要使用 PowerShell 脚本。 将此本机指标日志记录功能与 Log Analytics 监视结合使用，可以大规模监视 Azure 资源。 

## <a name="prerequisites"></a>先决条件
在继续操作之前，请确认计算机上是否已安装以下 Azure 资源管理器模块：

- AzureRM.Insights
- AzureRM.OperationalInsights
- AzureRM.Resources
- AzureRM.profile

>[!NOTE]
>我们建议对所有 Azure 资源管理器模块使用相同的版本，确保从 PowerShell 运行 Azure 资源管理器命令时能够兼容。
>
若要在计算机上安装最新版本的 Azure 资源管理器模块，请参阅[安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.1#update-azps)。  

## <a name="enable-azure-diagnostics"></a>启用 Azure 诊断  
可以执行 [PowerShell 库](https://www.powershellgallery.com/packages/Enable-AzureRMDiagnostics/2.52/DisplayScript)中提供的脚本 **Enable-AzureRMDiagnostics.ps1**，来为 PaaS 资源配置 Azure 诊断。  该脚本支持以下方案：
  
* 指定与订阅中一个或多个资源组相关的资源  
* 指定与订阅中某个特定资源组相关的资源  
* 将资源重新配置为转发到不同的工作区

仅支持允许使用 Azure 诊断收集指标并将其直接发送到 Log Analytics 的资源。  有关详细列表，请查看[收集要在 Log Analytics 中使用的 Azure 服务日志和指标](log-analytics-azure-storage.md)。 

执行以下步骤，下载并运行该脚本。

1.  在 Windows 开始屏幕中键入 **PowerShell**，在搜索结果中右键单击“PowerShell”。  从菜单中选择“以管理员身份运行”。   
2. 运行以下命令并提供 **Enable-AzureRMDiagnostics.ps1** 脚本文件的存储路径，将此脚本保存在本地。    

    ```
    PS C:\> save-script -Name Enable-AzureRMDiagnostics -Path "C:\users\<username>\desktop\temp"
    ```

3. 运行 `Connect-AzureRmAccount`，创建与 Azure 的连接。   
4. 运行以下脚本 `.\Enable-AzureRmDiagnostics.ps1` 且不指定任何参数，以启用从订阅中的特定资源组收集数据；或者指定 `-ResourceGroup <myResourceGroup>` 参数，指定从特定资源组中的资源收集数据。   
5. 输入正确的值，从列表中选择适当的订阅（如果有多个）。<br><br> ![选择脚本返回的订阅](./media/log-analytics-collect-azurepass-posh/script-select-subscription.png)<br> 否则，系统会自动选择单个可用的订阅。
6. 接下来，脚本会返回订阅中注册的 Log Analytics 工作区列表。  从列表中选择适当的工作区。<br><br> ![选择脚本返回的工作区](./media/log-analytics-collect-azurepass-posh/script-select-workspace.png)<br> 
7. 选择要从中收集数据的 Azure 资源。 例如，如果键入 5，则会针对 SQL Azure 数据库启用数据收集。<br><br> ![选择脚本返回的资源类型](./media/log-analytics-collect-azurepass-posh/script-select-resource.png)<br>
   只能选择支持使用 Azure 诊断收集指标并将其直接发送到 Log Analytics 的资源。  脚本会在 **Metrics** 列下面，针对它在订阅或指定资源组中发现的资源列表显示值 **True**。    
8. 系统会提示确认所做的选择。  输入 **Y** 会针对所定义范围内的所有选定资源启用指标日志记录，在本示例中，为订阅中的所有 SQL 数据库。  

脚本将会针对与选定条件匹配的每个资源运行，并为这些资源启用指标收集。 完成后，会出现一条消息，指出配置已完成。  

配置完成后，Log Analytics 存储库中随即会显示来自 Azure PaaS 资源的数据。  将会创建一条 `AzureMetrics` 类型的记录，可以使用 [Azure SQL Analytics](log-analytics-azure-sql.md) 和 [Azure Web 应用分析](log-analytics-azure-web-apps-analytics.md)管理解决方案来分析这些记录。   

## <a name="update-a-resource-to-send-data-to-another-workspace"></a>更新资源以向另一工作区发送数据
如果某个资源已开始向 Log Analytics 工作区发送数据，后来你决定将该资源重新配置为引用另一工作区，此时，可以结合 `-Update` 参数运行该脚本。  

**示例：** 
`PS C:\users\<username>\Desktop\temp> .\Enable-AzureRMDiagnostics.ps1 -Update`

系统会提示你答复在运行脚本执行初始配置时所提供的相同信息。  

## <a name="next-steps"></a>后续步骤

* 了解[日志搜索](log-analytics-log-searches.md)以便分析从数据源和解决方案中收集的数据。 

* 使用[自定义字段](log-analytics-custom-fields.md)将事件记录分析成单独的字段。

* 查看[创建要在 Log Analytics 中使用的自定义仪表板](log-analytics-dashboards.md)，了解如何以组织容易理解的方式将日志搜索结果可视化。
