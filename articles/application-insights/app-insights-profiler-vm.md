---
title: 使用 Application Insights Profiler 探查在 Azure VM 上运行的 Web 应用 | Microsoft Docs
description: 使用 Application Insights Profiler 探查 Azure VM 上的 Web 应用。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: b98c75c1fb42c6eec2473dee1d6661d86138bd7a
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725669"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-virtual-machine-scale-set-with-application-insights-profiler"></a>使用 Application Insights Profiler 探查在 Azure 虚拟机或虚拟机规模集上运行的 Web 应用
还可以在以下服务上部署 Application Insights Profiler：
* [Azure Web 应用](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [云服务](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-scale-set"></a>在虚拟机或规模集上部署 Profiler
此页面将引导你完成使 Application Insights Profiler 在 Azure VM 或 Azure 虚拟机规模集上运行所需执行的步骤。 Application Insights Profiler 是随 VM 的 Windows Azure 诊断扩展安装的。 需要将扩展配置为运行探查器，并且必须在应用程序中内置 App Insights SDK。

1. 将 Application Insights SDK 添加到你的 [ASP.Net 应用程序](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net)或常规 [.NET 应用程序。](https://docs.microsoft.com/azure/application-insights/app-insights-windows-services?toc=/azure/azure-monitor/toc.json) 必须将请求遥测数据发送到 Application Insights 才能看到你的请求的配置文件。
1. 在 VM 上安装 Windows Azure 诊断扩展。 有关完整的资源管理器模板示例，请参阅：  
    * [虚拟机](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [虚拟机规模集](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
    关键部分是 WadCfg 中的 ApplicationInsightsProfilerSink。 在此部分添加另一个接收器，以便告知 WAD 启用探查器，将数据发送到 iKey。
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

   若要应用修改，通常会涉及完整的模板部署或者通过 PowerShell cmdlet 或 Visual Studio 进行的基于云服务的发布。  

   下面的 powershell 命令是用于现有虚拟机的一种替代方法，该方法仅涉及 Azure 诊断扩展。 只需将上面提到的 ProfilerSink 添加到 Get-AzureRmVMDiagnosticsExtension 命令返回的配置中。 然后将更新的配置传递给 Set-AzureRmVMDiagnosticsExcension 命令。

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. 如果目标应用程序通过 [IIS](https://www.microsoft.com/web/downloads/platform.aspx) 运行，请启用 `IIS Http Tracing` Windows 功能。

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 建立到环境的远程访问，然后使用[添加 Windows 功能]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/)窗口，或以管理员身份在 PowerShell 中运行以下命令：  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. 如果无法建立远程访问，则可使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 运行以下命令：  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. 部署应用程序。

## <a name="can-profiler-run-on-on-premises-servers"></a>Profiler 是否可以在本地服务器上运行？
我们没有计划对本地服务器支持 Application Insights Profiler。

## <a name="next-steps"></a>后续步骤

- 生成到应用程序的流量（例如，启动[可用性测试](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)）。 然后等待 10 到 15 分钟，这样跟踪就会开始发送到 Application Insights 实例。
- 请参阅 Azure 门户中的 [Profiler 跟踪](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json)。
- 在 [Profiler 故障排除](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)中获得有关排查 Profiler 问题的帮助。
