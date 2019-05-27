---
title: 使用 Application Insights Profiler 探查在 Azure VM 上运行的 Web 应用 | Microsoft Docs
description: 使用 Application Insights Profiler 探查 Azure VM 上的 Web 应用。
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 4cca65e2be44d2c846cd4034f0a9d7e8c7d9af28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "66130863"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>使用 Application Insights Profiler 探查在 Azure 虚拟机或虚拟机规模集上运行的 Web 应用

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Application Insights Profiler 也可以部署在以下服务上：
* [Azure 应用服务](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure 云服务](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>在虚拟机或虚拟机规模集上部署 Profiler
本文介绍如何在 Azure 虚拟机 (VM) 或 Azure 虚拟机规模集上运行 Application Insights Profiler。 Profiler 与适用于 VM 的 Azure 诊断扩展一同安装。 请将该扩展配置为运行 Profiler，并将 Application Insights SDK 内置到应用程序中。

1. 将 Application Insights SDK 添加到 [ASP.NET 应用程序](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net)或常规 [.NET 应用程序](windows-services.md?toc=/azure/azure-monitor/toc.json)。  
   若要查看请求的探查结果，必须将请求遥测数据发送到 Application Insights。

1. 在 VM 上安装 Azure 诊断扩展。 有关完整的资源管理器模板示例，请参阅：  
   * [虚拟机](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [虚拟机规模集](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     关键部分是 WadCfg 中的 ApplicationInsightsProfilerSink。 若要告知 Azure 诊断启用探查器，以将数据发送到 iKey，请在此部分添加另一个接收器。
    
     ```json
     "SinksConfig": {
       "Sink": [
         {
           "name": "ApplicationInsightsSink",
           "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
         },
         {
           "name": "MyApplicationInsightsProfilerSink",
           "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
         }
       ]
     },
     ```

1. 部署修改后的环境部署定义。  

   应用修改通常涉及到完整的模板部署或者通过 PowerShell cmdlet 或 Visual Studio 进行的基于云服务的发布。  

   以下 PowerShell 命令是另一种方法对于触摸仅 Azure 诊断扩展的现有虚拟机。 将先前提到的 ProfilerSink 添加到由 Get AzVMDiagnosticsExtension 命令返回的配置。 然后将更新的配置传递给集 AzVMDiagnosticsExtension 命令。

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. 如果目标应用程序通过 [IIS](https://www.microsoft.com/web/downloads/platform.aspx) 运行，请启用 `IIS Http Tracing` Windows 功能。

   a. 与环境建立远程访问连接，然后使用 [添加 Windows 功能]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) 窗口。 或者，以管理员身份在 PowerShell 中运行以下命令：  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. 如果无法建立远程访问连接，可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 运行以下命令：  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. 部署应用程序。

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>设置 Profiler 接收器使用 Azure 资源浏览器
我们还没有从门户中设置 Application Insights Profiler 接收器的方法。 而不是使用 powershell 如上文所述，可以使用 Azure 资源浏览器设置接收器。 但请注意，如果重新部署 VM，接收器将丢失。 你将需要更新部署 VM 后，如果要保留这些设置时使用的配置。

1. 检查通过查看安装为虚拟机的扩展插件安装了 Windows Azure 诊断扩展。  

    ![检查是否安装了 WAD 扩展][wadextension]

1. VM 到 VM 诊断扩展。 展开你的资源组、 Microsoft.Compute virtualMachines、 虚拟机名称和扩展。  

    ![导航到 Azure 资源浏览器中的 WAD 配置][azureresourceexplorer]

1. 将 Application Insights Profiler 接收器添加到下 WadCfg SinksConfig 节点。 如果还没有 SinksConfig 部分，您可能需要添加一个。 请务必在你的设置中指定正确的 Application Insights iKey。 你将需要在右上角中将资源管理器模式切换为读/写和按蓝色的编辑按钮。

    ![添加 Application Insights Profiler 接收器][resourceexplorersinksconfig]

1. 完成后编辑的配置来说，按 Put。 如果 put 操作成功，请将屏幕中间显示绿色复选标记。

    ![发送 put 请求以应用更改][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>Profiler 是否可以在本地服务器上运行？
我们没有计划对本地服务器支持 Application Insights Profiler。

## <a name="next-steps"></a>后续步骤

- 生成到应用程序的流量（例如，启动[可用性测试](monitor-web-app-availability.md)）。 然后等待 10 到 15 分钟，这样跟踪就会开始发送到 Application Insights 实例。
- 请参阅 Azure 门户中的 [Profiler 跟踪](profiler-overview.md?toc=/azure/azure-monitor/toc.json)。
- 排查 Profiler 问题时如需帮助，请参阅 [Profiler 故障排除](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)。

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
