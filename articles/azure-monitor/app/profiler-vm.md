---
title: 在 Azure VM 上分析 web 应用-Application Insights Profiler
description: 使用 Application Insights Profiler 探查 Azure VM 上的 Web 应用。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 046128fda385486f5a92d215e349760483c5dfa4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432365"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>使用 Application Insights Profiler 探查在 Azure 虚拟机或虚拟机规模集上运行的 Web 应用

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Application Insights Profiler 也可以部署在以下服务上：
* [Azure 应用服务](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure 云服务](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>在虚拟机或虚拟机规模集上部署 Profiler
本文介绍如何在 Azure 虚拟机 (VM) 或 Azure 虚拟机规模集上运行 Application Insights Profiler。 Profiler 与适用于 VM 的 Azure 诊断扩展一同安装。 请将该扩展配置为运行 Profiler，并将 Application Insights SDK 内置到应用程序中。

1. 将 Application Insights SDK 添加到[ASP.NET 应用程序](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net)。

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

   对于只触及 Azure 诊断扩展的现有虚拟机，以下 PowerShell 命令是一种替代方法。 将前面提到的 ProfilerSink 添加到 AzVMDiagnosticsExtension 命令返回的配置中。 然后，将更新的配置传递到 AzVMDiagnosticsExtension 命令。

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. 如果目标应用程序通过 [IIS](https://www.microsoft.com/web/downloads/platform.aspx) 运行，请启用 `IIS Http Tracing` Windows 功能。

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 与环境建立远程访问连接，然后使用 [添加 Windows 功能]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) 窗口。 或者，以管理员身份在 PowerShell 中运行以下命令：  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b.保留“数据库类型”设置，即设置为“共享”。 如果无法建立远程访问连接，可以使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 运行以下命令：  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. 部署应用程序。

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>使用 Azure 资源浏览器设置探查器接收器
我们尚无法从门户设置 Application Insights Profiler 接收器。 你可以使用 Azure 资源浏览器来设置接收器，而不是如上所述使用 powershell，如上文所述。 但请注意，如果重新部署 VM，则接收器会丢失。 你需要更新部署 VM 时使用的配置以保留此设置。

1. 通过查看为虚拟机安装的扩展来检查 Windows Azure 诊断扩展是否已安装。  

    ![检查是否安装了 WAD 扩展][wadextension]

2. 查找 VM 的 VM 诊断扩展。 转到[https://resources.azure.com](https://resources.azure.com)。 展开资源组 "virtualMachines"、"虚拟机名称" 和 "扩展"。  

    ![导航到 Azure 资源浏览器中的 WAD 配置][azureresourceexplorer]

3. 将 Application Insights Profiler 接收器添加到 Diagnostics.wadcfg 下的 SinksConfig 节点。 如果还没有 SinksConfig 节，可能需要添加一个。 请确保在设置中指定正确的 Application Insights iKey。 你需要将浏览器模式切换为读/写，并按蓝色的 "编辑" 按钮。

    ![添加 Application Insights Profiler 接收器][resourceexplorersinksconfig]

4. 编辑完配置后，按 "Put"。 如果 put 成功，屏幕中间会出现绿色复选标记。

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
