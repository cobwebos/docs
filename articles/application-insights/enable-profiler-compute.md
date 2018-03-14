---
title: "为 Azure 云服务资源上托管的应用程序启用 Application Insights Profiler | Microsoft Docs"
description: "了解如何为 Azure 云服务中运行的应用程序设置 Application Insights Profiler。"
services: application-insights
documentationcenter: 
author: ramach-msft
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: 278d8241ddd67b6df64b7280d4a17c6d3152f223
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2018
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-azure-cloud-services"></a>为 Azure VM、Service Fabric 和 Azure 云服务启用 Application Insights Profiler

本文演示如何在 Azure 云服务资源托管的 ASP.NET 应用程序上启用 Azure Application Insights Profiler。

本文中的示例包括对 Azure 虚拟机、虚拟机规模集、Azure Service Fabric 和 Azure 云服务的支持。 所有示例依赖于支持 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)部署模型的模板。  


## <a name="overview"></a>概述

下图显示 Application Insights Profiler 如何处理 Azure 云服务资源上托管的应用程序。 Azure 云服务资源包括虚拟机、规模集、云服务和 Service Fabric 群集。 此图使用 Azure 虚拟机作为示例。  

  ![显示 Application Insights Profiler 如何处理 Azure 云服务资源的图示](./media/enable-profiler-compute/overview.png)

若要完全启用 Profiler，必须更改三个位置中的配置：

* Azure 门户中的 Application Insights 实例窗格。
* 应用程序源代码（例如，ASP.NET Web 应用程序）。
* 环境部署定义源代码（例如，.json 文件中的 Azure 资源管理器模板）。


## <a name="set-up-the-application-insights-instance"></a>设置 Application Insights 实例

1. [新建一个 Application Insights 资源](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-create-new-resource)或选择一个现有 Application Insights 资源。 

2. 转到 Application Insights 资源并复制检测密钥。

   ![检测密钥的位置](./media/enable-profiler-compute/CopyAIKey.png)

3. 若要完成为 Profiler 设置 Application Insights 实例，请完成[启用 Profiler](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler) 中所述的过程。  
    不需要链接 Web 应用，因为步骤特定于应用程序服务资源。 确保在“配置 Profiler”窗格中启用了 Profiler。


## <a name="set-up-the-application-source-code"></a>设置应用程序源代码

### <a name="aspnet-web-applications-azure-cloud-services-web-roles-or-the-service-fabric-aspnet-web-front-end"></a>ASP.NET Web 应用程序、Azure 云服务 Web 角色或 Service Fabric ASP.NET Web 前端
将应用程序设置为在每次执行 `Request` 操作时将遥测数据发送到 Application Insights 实例。  

将 [Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-overview#get-started) 添加到应用程序项目。 请确保 NuGet 包版本如下所示：  
  - 对于 ASP.NET 应用程序：[Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 或更高版本。
  - 对于 ASP.NET Core 应用程序：[Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 或更高版本。
  - 对于其他 .NET 和 .NET Core 应用程序（例如，Service Fabric 无状态服务或云服务辅助角色）：[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 或 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 或更高版本。  

### <a name="azure-cloud-services-worker-roles-or-the-service-fabric-stateless-back-end"></a>Azure 云服务辅助角色或 Service Fabric 无状态后端
除了完成上述步骤外，如果应用程序不是 ASP.NET 或 ASP.NET Core 应用程序（例如，如果应用程序是 Azure 云服务辅助角色或 Service Fabric 无状态 API），请执行以下操作：  

  1. 在应用程序生命周期的早期添加以下代码：  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      有关此全局检测密钥配置的详细信息，请参阅 [Use Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)（结合使用 Service Fabric 和 Application Insights）。  

  2. 对于想要检测的任何代码片段，请在其两侧添加 `StartOperation<RequestTelemetry>` USING 语句，如以下示例所示：

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        不支持在其他 `StartOperation<RequestTelemetry>` 作用域中调用 `StartOperation<RequestTelemetry>`。 可以改为在嵌套作用域中使用 `StartOperation<DependencyTelemetry>`。 例如：  
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```

## <a name="set-up-the-environment-deployment-definition"></a>设置环境部署定义

执行 Profiler 和应用程序的环境可以是虚拟机、虚拟机规模集、Service Fabric 群集或云服务实例。  

### <a name="virtual-machines-scale-sets-or-service-fabric"></a>虚拟机、规模集或 Service Fabric

有关完整示例，请参阅：  
  * [虚拟机](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [虚拟机规模集](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Service Fabric 群集](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

若要设置环境，请执行以下操作：
1. 若要确保使用的是 [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 或更高版本，只需确认部署的 OS 是 `Windows Server 2012 R2` 或更高版本。

2. 在部署模板文件中搜索 [Azure 诊断](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)扩展，然后添加下面的 `SinksConfig` 节作为 `WadCfg` 的子元素。 使用自己的 Application Insights 检测密钥替换 `ApplicationInsightsProfiler` 属性值：  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      若要了解如何将诊断扩展添加到部署模板，请参阅[将监视和诊断与 Windows VM 和 Azure 资源管理器模板配合使用](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。


### <a name="azure-cloud-services"></a>Azure 云服务

1. 若要确保使用的是 [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 或更高版本，只需确认 ServiceConfiguration.\*.cscfg 文件的 `osFamily` 值至少为“5”。

2. 找到应用程序角色的 [Azure 诊断](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) diagnostics.wadcfgx 文件，如下所示：  

   ![诊断配置文件的位置](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

   如果找不到该文件，若要了解如何在 Azure 云服务项目中启用诊断扩展，请参阅[为 Azure 云服务和虚拟机设置诊断](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them)。

3. 添加以下 `SinksConfig` 部分作为 `WadCfg` 的子元素：  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

> [!NOTE]  
> 如果 *diagnostics.wadcfgx* 文件还包含 `ApplicationInsights` 类型的其他接收器，则以下三个检测密钥都必须匹配：  
>  * 应用程序使用的密钥。  
>  * `ApplicationInsights` 接收器使用的密钥。  
>  * `ApplicationInsightsProfiler` 接收器使用的密钥。  
>
> 可以在 ServiceConfiguration.\*.cscfg 文件中找到 `ApplicationInsights` 接收器使用的实际检测密钥值。  
> 在 Visual Studio 15.5 Azure SDK 版本之后，只有应用程序和 `ApplicationInsightsProfiler` 接收器使用的检测密钥需要相互匹配。


## <a name="configure-environment-deployment-and-runtime"></a>配置环境部署和运行时

1. 部署修改后的环境部署定义。  

   通常情况下，为了应用修改，会涉及完整的模板部署或者通过 PowerShell cmdlet 或 Visual Studio 进行的云服务发布。  

   下面将介绍用于现有虚拟机的一种替代方法，该方法仅涉及 Azure 诊断扩展：  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

2. 如果目标应用程序通过 [IIS](https://www.microsoft.com/web/platform/server.aspx) 运行，请通过执行以下操作启用 `IIS Http Tracing` Windows 功能：  

   a. 建立到环境的远程访问，然后使用[添加 Windows 功能]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/)窗口，或以管理员身份在 PowerShell 中运行以下命令：  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. 如果无法建立远程访问，则可使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 运行以下命令：  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-profiler-on-on-premises-servers"></a>在本地服务器上启用 Profiler

在本地服务器上启用 Profiler 也称为以独立模式运行 Application Insights Profiler。 它不依赖于 Azure 诊断扩展修改。

我们没有计划为本地服务器提供正式的 Profiler 支持。 如果有兴趣试验这种情况，可[下载支持代码](https://github.com/ramach-msft/AIProfiler-Standalone)。 我们不负责维护该代码或响应与该代码相关的问题和功能请求。

## <a name="next-steps"></a>后续步骤

- 生成到应用程序的流量（例如，启动[可用性测试](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)）。 然后等待 10 到 15 分钟，这样跟踪就会开始发送到 Application Insights 实例。
- 请参阅 Azure 门户中的 [Profiler 跟踪](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler)。
- 在 [Profiler 故障排除](app-insights-profiler.md#troubleshooting)中获得有关排查 Profiler 问题的帮助。
- 在 [Application Insights Profiler](app-insights-profiler.md) 中详细了解 Profiler。
