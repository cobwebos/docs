---
title: 监视 Azure VM 的性能 - Azure Application Insights
description: 针对 Azure VM 和 Azure 虚拟机规模集进行应用程序性能监视 对加载和响应时间、依赖项信息绘制图表，并针对性能设置警报。
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: d75e14dccef565f0029d06583e74d5693726dd99
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "77661322"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>在 Azure 虚拟机和 Azure 虚拟机规模集上部署 Azure Monitor Application Insights 代理

现在比以往更容易在基于 .NET 的 Web 应用程序上启用监视功能，这些应用程序运行在 [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)和 [Azure 虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/)上。 获取使用 Application Insights 的所有权益，不需修改代码。

本文逐步讲解如何通过 Application Insights 代理启用 Application Insights 监视，并提供有关如何自动完成大规模部署过程的初步指导。

> [!IMPORTANT]
> 用于 .NET 的 Azure Application Insights 代理目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，我们不建议将其用于生产工作负荷。 有些功能可能不受支持，有些功能可能受到限制。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="enable-application-insights"></a>启用 Application Insights

可通过两种方法为 Azure 虚拟机和 Azure 虚拟机规模集托管的应用程序启用应用程序监视：

* 通过 Application Insights 代理执行的“无代码”**** 方法
    * 这是启用监视的最简单方法，无需完成任何高级配置。 这种监视通常称为“运行时”监视。

    * 对于 Azure 虚拟机和 Azure 虚拟机规模集，建议至少启用此级别的监视。 然后，可以根据具体情况评估是否需要手动检测。

    * Application Insights 代理自动收集与 .NET SDK 相同的、现成的依赖项信号。 若要了解详细信息，请参阅[依赖项自动收集](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies#net)。
        > [!NOTE]
        > 目前仅支持 .Net IIS 托管的应用程序。 请使用 SDK 检测托管在 Azure 虚拟机和虚拟机规模集上的 ASP.NET Core、Java 和 Node.js 应用程序。

* **** 通过 SDK 执行的“基于代码”方法

    * 此方法的可自定义性要高得多，但需要[添加 Application Insights SDK NuGet 包中的一个依赖项](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)。 使用此方法还需要自行管理对最新版本的包的更新。

    * 如果需要发出自定义 API 调用来跟踪基于代理的监视在默认情况下不会捕获的事件/依赖项，则需要使用此方法。 有关详细信息，请查看 [自定义事件和指标的 API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) 一文。

> [!NOTE]
> 如果同时检测到了基于代理的监视和基于手动 SDK 的检测，则只会遵循手动检测设置， 这是为了防止发送重复数据。 若要了解详细信息，请参阅下面的[故障排除部分](#troubleshooting)。

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>使用 PowerShell 在 Azure 虚拟机上管理用于 .NET 应用程序的 Application Insights 代理

> [!NOTE]
> 安装 Application Insights 代理之前，需要一个连接字符串。 [创建新的 Application Insights 资源](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)，或者从现有的 application Insights 资源复制连接字符串。

> [!NOTE]
> 不熟悉 PowerShell？ 请查看[入门指南](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-2.5.0)。

安装或更新用作 Azure 虚拟机扩展的 Application Insights 代理
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
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
> 可以使用 PowerShell 循环跨多个虚拟机大规模安装或更新用作扩展的 Application Insights 代理。

从 Azure 虚拟机卸载 Application Insights 代理扩展
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

查询 Azure 虚拟机的 Application Insights 代理扩展状态
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

获取 Azure 虚拟机的已安装扩展的列表
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
> 通过单击与用于部署 Application Insights 代理扩展的连接字符串关联的 Application Insights 资源中的实时指标流来验证安装。 如果从多个虚拟机发送数据，请在“服务器名称”下选择目标 Azure 虚拟机。 可能需要等待长达一分钟的时间数据才会开始流动。

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>使用 PowerShell 在 Azure 虚拟机规模集上管理用于 .NET 应用程序的 Application Insights 代理

安装或更新用作 Azure 虚拟机规模集扩展的 Application Insights 代理
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
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

查询 Azure 虚拟机规模集的应用程序监视扩展状态
```powershell
# Not supported by extensions framework
```

获取 Azure 虚拟机规模集的已安装扩展的列表
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>疑难解答

请查找在 Azure 虚拟机和虚拟机规模集上运行的 .NET 应用程序的 Application Insights 监视代理扩展的故障排除提示。

> [!NOTE]
> 仅支持通过基于 SDK 的手动检测在 Azure 虚拟机和 Azure 虚拟机规模集中使用 .NET Core、Java 和 Node.js 应用程序，因此，以下步骤不适用于这些方案。

扩展执行输出将记录到在以下目录中发现的文件：
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>后续步骤
* 了解如何[将应用程序部署到 Azure 虚拟机规模集](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)。
* [设置可用性 Web 测试](monitor-web-app-availability.md)，以便在终结点关闭时发出警报。
