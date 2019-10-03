---
title: 在 Azure 流量管理器中启用诊断日志记录
description: 了解如何为你的流量管理器配置文件启用诊断日志记录并访问作为结果创建的日志文件。
services: traffic-manager
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: allensu
ms.openlocfilehash: b2ebeb41e69b7edfd43c38cc3b828069a1b3401a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071229"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>在 Azure 流量管理器中启用诊断日志记录

本文介绍了如何为流量管理器配置文件启用诊断日志记录和访问日志数据。

使用 Azure 流量管理器诊断日志可以深入了解流量管理器配置文件资源的行为。 例如，你可以使用配置文件的日志数据来确定为什么个别探测针对某个端点超时。

## <a name="enable-diagnostic-logging"></a>启用诊断日志记录

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

可以在 [Azure Cloud Shell](https://shell.azure.com/powershell) 中运行以下命令，或者在计算机上运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 如果在计算机上运行 PowerShell，则需要 Azure PowerShell 模块 1.0.0 或更高版本。 可以运行 `Get-Module -ListAvailable Az` 来查找已安装的版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzAccount` 以登录到 Azure。

1. **检索流量管理器配置文件：**

    若要启用诊断日志记录，需要具有流量管理器配置文件的 ID。 使用 [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile) 检索要为其启用诊断日志记录的流量管理器配置文件。 输出包括流量管理器配置文件的 ID 信息。

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **为流量管理器配置文件启用诊断日志记录：**

    通过 [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest) 使用在上一步骤中获取的 ID 为流量管理器配置文件启用诊断日志记录。 以下命令将流量管理器配置文件的详细日志存储到指定的 Azure 存储帐户。 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **验证诊断设置：**

      使用 [Get-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest) 验证流量管理器配置文件的诊断设置。 以下命令显示为某个资源记录的类别。

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      请确保与流量管理器配置文件资源关联的所有日志类别都显示为已启用。 另外，请验证存储帐户是否已正确设置。

## <a name="access-log-files"></a>访问日志文件
1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 在门户中导航到你的 Azure 存储帐户。
2. 在你的 Azure 存储帐户的“概述”  页面上，在“服务”  下选择“Blob”  。
3. 对于“容器”  ，选择“insights-logs-probehealthstatusevents”  ，向下导航到 PT1H.json 文件并单击“下载”  以下载并保存此日志文件的副本。

    ![从 blob 存储访问流量管理器配置文件的日志文件](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>流量管理器日志架构

通过 Azure Monitor 提供的所有诊断日志共享公共顶级架构，且每个服务都能灵活地为其事件发出唯一属性。 对于顶级诊断日志架构，请参阅 [Azure 诊断日志支持的服务、架构和类别](../azure-monitor/platform/tutorial-dashboards.md)。

下表包含特定于 Azure 流量管理器配置文件资源的日志架构。

|||||
|----|----|---|---|
|**字段名称**|**字段类型**|**定义**|**示例**|
|EndpointName|String|记录其运行状况状态的流量管理器终结点的名称。|*myPrimaryEndpoint*|
|状态|String|所探测的流量管理器终结点的运行状况状态。 状态可以是 **Up** 或 **Down**。|**Up**|
|||||

## <a name="next-steps"></a>后续步骤

* 详细了解[流量管理器监视](traffic-manager-monitoring.md)

