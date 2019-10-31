---
title: 监视 Azure VM 和 Azure 虚拟机规模集上托管的应用程序性能 |Microsoft Docs
description: Azure VM 和 Azure 虚拟机规模集的应用程序性能监视。 图表加载和响应时间、依赖关系信息，并设置性能警报。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/26/2019
ms.openlocfilehash: 248dfb83c26d3f49fb492272ee3bd87d1e34fefa
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161471"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>在 Azure 虚拟机和 Azure 虚拟机规模集上部署 Azure Monitor Application Insights 代理

现在，在[azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)和[azure 虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/)上运行的基于 .net 的 web 应用程序启用监视现在比以往更容易。 无需修改代码即可获取使用 Application Insights 的所有权益。

本文逐步讲解如何使用 Application Insights 代理启用 Application Insights 监视，并为大规模部署的自动化过程提供了初步指导。

> [!IMPORTANT]
> 适用于 .NET 的 Azure 应用程序 Insights 代理目前为公共预览版。
> 此预览版本在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持，某些功能可能具有受限制的功能。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="enable-application-insights"></a>启用 Application Insights

可通过两种方式为 Azure 虚拟机和 Azure 虚拟机规模集托管的应用程序启用应用程序监视：

* 通过 Application Insights 代理**无代码置备**
    * 此方法是最容易启用的，无需高级配置。 它通常称为 "运行时" 监视。

    * 对于 Azure 虚拟机和 Azure 虚拟机规模集，我们建议你至少启用此级别的监视。 之后，你可以根据具体的情况评估是否需要手动检测。

    * Application Insights 代理会自动收集与 .NET SDK 相同的依赖信号。 若要了解详细信息，请参阅[依赖项自动收集](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies#net)。
        > [!NOTE]
        > 目前仅支持 .Net IIS 托管的应用程序。 使用 SDK 来检测 Azure 虚拟机和虚拟机规模集上托管的 ASP.NET Core、Java 和 node.js 应用程序。

* **基于代码的**via SDK

    * 此方法的可自定义性更高，但它需要[在 APPLICATION INSIGHTS SDK NuGet 包上添加依赖项](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)。 此方法还意味着您必须自行管理包的最新版本的更新。

    * 如果需要进行自定义 API 调用以跟踪默认情况下未与基于代理的监视一起捕获的事件/依赖关系，则需要使用此方法。 有关详细信息，请参阅 API，了解[自定义事件和指标](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics)。

> [!NOTE]
> 如果检测到基于代理的监视和基于手动 SDK 的检测，则只会遵守手动检测设置。 这是为了防止发送重复的数据。 若要了解详细信息，请参阅下面的[故障排除部分](#troubleshooting)。

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>使用 PowerShell 管理 Azure 虚拟机上的 .NET 应用程序 Application Insights 代理

> [!NOTE]
> 安装 Application Insights 代理之前，需要一个检测密钥。 [创建新的 Application Insights 资源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)，或者从现有的 application Insights 资源复制检测密钥。

> [!NOTE]
> 新到 powershell？ 查看[入门指南](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-2.5.0)。

作为 Azure 虚拟机的扩展安装或更新 Application Insights 代理
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "instrumentationSettings" : {
            "instrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> 你可以使用 Powershell 循环在多个虚拟机之间进行扩展安装或更新 Application Insights 代理。

从 Azure 虚拟机中卸载 Application Insights 代理扩展
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Azure 虚拟机的查询 Application Insights 代理扩展状态
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

获取已安装的 Azure 虚拟机扩展列表
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
你还可以在门户的 " [Azure 虚拟机" 边栏选项卡](https://docs.microsoft.com/azure/virtual-machines/extensions/overview)中查看已安装的扩展。

> [!NOTE]
> 通过单击与用于部署 Application Insights 代理扩展的检测密钥关联的 Application Insights 资源中的实时指标流来验证安装。 如果要从多个虚拟机发送数据，请在 "服务器名称" 下选择目标 Azure 虚拟机。 数据开始流动可能需要长达一分钟的时间。

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>使用 powershell 管理 Azure 虚拟机规模集上的 .NET 应用程序 Application Insights 代理

作为 Azure 虚拟机规模集的扩展安装或更新 Application Insights 代理
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "instrumentationSettings"= @{
            "instrumentationKey"= "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"; # Application Insights Instrumentation Key, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

从 Azure 虚拟机规模集卸载应用程序监视扩展
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Azure 虚拟机规模集的查询应用程序监视扩展状态
```powershell
# Not supported by extensions framework
```

获取 Azure 虚拟机规模集的已安装扩展列表
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>故障排除

查找适用于在 Azure 虚拟机和虚拟机规模集上运行的 .NET 应用程序的 Application Insights 监视代理扩展的故障排除提示。

> [!NOTE]
> 只有 Azure 虚拟机和 Azure 虚拟机规模集支持 .NET Core、Java 和 node.js 应用程序，才能通过手动基于 SDK 的检测，因此以下步骤不适用于这些方案。

扩展执行输出将记录到在以下目录中发现的文件：
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>后续步骤
* 了解如何将[应用程序部署到 Azure 虚拟机规模集](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)。
* 设置要在终结点关闭时发出警报的[可用性 web 测试](monitor-web-app-availability.md)。
