---
title: 监视 Azure VM 和 Azure 虚拟机规模集上托管的应用程序性能 |Microsoft Docs
description: Azure VM 和 Azure 虚拟机规模集的应用程序性能监视。 对加载和响应时间、依赖项信息绘制图表，并针对性能设置警报。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: mbullwin
ms.openlocfilehash: f2c6b98fd0be2061e9d8cab5c063cafadf71476a
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597447"
---
# <a name="monitor-application-performance-hosted-on-azure-vm-and-azure-virtual-machine-scale-sets"></a>监视 Azure VM 和 Azure 虚拟机规模集上托管的应用程序性能

现在, 在[Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)和[Azure 虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/)上运行的基于 .net 的 web 应用程序启用监视现在比以往更容易。 无需修改代码即可获取使用 Application Insights 的所有权益。

本文介绍如何使用 ApplicationMonitoringWindows 扩展启用 Application Insights 监视, 并为大规模部署的自动化过程提供初步指导。

> [!IMPORTANT]
> Azure ApplicationMonitoringWindows 扩展目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，我们不建议将其用于生产工作负荷。 有些功能可能不受支持，有些功能可能受到限制。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="enable-application-insights"></a>启用 Application Insights

可通过两种方式为 Azure VM 和 Azure 虚拟机规模集托管的应用程序启用应用程序监视:

* **基于代理的应用程序监视**(ApplicationMonitoringWindows 扩展)。
    * 这是启用监视的最简单方法，无需完成任何高级配置。 这种监视通常称为“运行时”监视。 对于 Azure Vm 和 Azure 虚拟机规模集, 我们建议你至少启用此级别的监视。 之后, 你可以根据具体的情况评估是否需要手动检测。
    * 目前仅支持 .Net IIS 托管的应用程序。

* 安装 Application Insights SDK 以**通过代码手动检测应用程序**。

    * 此方法的可自定义性要高得多，但需要[添加 Application Insights SDK NuGet 包中的一个依赖项](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)。 使用此方法还需要自行管理对最新版本的包的更新。

    * 如果需要发出自定义 API 调用来跟踪基于代理的监视在默认情况下不会捕获的事件/依赖项，则需要使用此方法。 有关详细信息，请查看 [自定义事件和指标的 API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) 一文。

> [!NOTE]
> 如果同时检测到了基于代理的监视和基于手动 SDK 的检测，则只会遵循手动检测设置， 这是为了防止发送重复的数据。 有关详细信息，请查看下面的[故障排除部分](#troubleshooting)。

## <a name="manage-agent-based-monitoring-for-net-applications-on-vm-using-powershell"></a>使用 PowerShell 管理 VM 上基于代理的 .NET 应用程序监视

为 VM 安装或更新应用程序监视扩展
```powershell
$publicCfgJsonString = '
{
  "RedfieldConfiguration": {
    "InstrumentationKeyMap": {
      "Filters": [
        {
          "AppFilter": ".*",
          "MachineFilter": ".*",
          "InstrumentationSettings" : {
            "InstrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "South Central US" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

从 VM 中卸载应用程序监视扩展
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

查询 VM 的应用程序监视扩展状态
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

获取已安装的 VM 扩展列表
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```

## <a name="manage-agent-based-monitoring-for-net-applications-on-azure-virtual-machine-scale-set-using-powershell"></a>使用 powershell 管理 Azure 虚拟机规模集上基于代理的 .NET 应用程序监视

安装或更新 Azure 虚拟机规模集的应用程序监视扩展
```powershell
$publicCfgHashtable =
@{
  "RedfieldConfiguration"= @{
    "InstrumentationKeyMap"= @{
      "Filters"= @(
        @{
          "AppFilter"= ".*";
          "MachineFilter"= ".*";
          "InstrumentationSettings"= @{
            "InstrumentationKey"= "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"; # Application Insights Instrumentation Key, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
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

## <a name="troubleshooting"></a>疑难解答

下面是针对在 Azure VM 和 Azure 虚拟机规模集上运行的 .NET 应用程序进行基于扩展的监视的分步疑难解答指南。

> [!NOTE]
> 仅在 Azure VM 和 Azure 虚拟机规模集上通过基于手动 SDK 的检测支持 .NET Core、Java 和 node.js 应用程序, 因此以下步骤不适用于这些方案。

扩展执行输出将记录到在以下目录中发现的文件：
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>后续步骤
* 了解如何将[应用程序部署到 Azure 虚拟机规模集](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)。
* 设置要在终结点关闭时发出警报的[可用性 web 测试](monitor-web-app-availability.md)。
