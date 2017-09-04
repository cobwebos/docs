---
title: "在云服务资源上启用 Azure Application Insights Profiler | Microsoft Docs"
description: "了解如何在由 Azure 云服务资源托管的 ASP.NET 应用程序上设置探查器。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: c2cae6129386260f2bf35f75d44fa001f7541d40
ms.contentlocale: zh-cn
ms.lasthandoff: 08/28/2017

---

# <a name="enable-application-insights-profiler-on-an-azure-cloud-services-resource"></a>在 Azure 云服务资源上启用 Azure Application Insights Profiler

本演练演示如何在 Azure 云服务资源托管的 ASP.NET 应用程序上启用 Azure Application Insights Profiler。 示例包括对 Azure 虚拟机、虚拟机规模集和 Azure Service Fabric 的支持。 所有示例依赖于支持 Azure 资源管理器部署模型的模板。 若要详细了解部署模型，请查看 [Azure 资源管理器与经典部署：了解部署模型和资源状态](/azure-resource-manager/resource-manager-deployment-model)。

## <a name="overview"></a>概述

下图演示了如何针对 Azure 云服务资源运行探查器。 其中使用 Azure 虚拟机作为示例。

![概述](./media/enable-profiler-compute/overview.png) 若要收集要处理的信息并在 Azure 门户上显示，则必须安装适用于 Azure 云服务资源的诊断代理组件。 本演练的余下内容提供有关如何安装和配置诊断代理以启用 Application Insights Profiler 的指导。

## <a name="prerequisites-for-the-walkthrough"></a>演练先决条件

* 在 VM ([WindowsVirtualMachine.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)) 或规模集 ([WindowsVirtualMachineScaleSet.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)) 上安装探查器代理的部署资源管理器模板。

* 已启用一个 Application Insights 实例用于探查。 有关说明，请参阅[启用探查器](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler)。

* 安装在目标 Azure 云服务资源上的 .NET Framework 4.6.1 或更高版本。

## <a name="create-a-resource-group-in-your-azure-subscription"></a>在 Azure 订阅中创建资源组
以下示例演示如何使用 PowerShell 脚本创建资源组：

```
New-AzureRmResourceGroup -Name "Replace_With_Resource_Group_Name" -Location "Replace_With_Resource_Group_Location"
```

## <a name="create-an-application-insights-resource-in-the-resource-group"></a>在资源组中创建 Application Insights 资源
在“Application Insights”边栏选项卡上，输入资源信息，如本示例所示： 

![“Application Insights”边栏选项卡](./media/enable-profiler-compute/createai.png)

## <a name="apply-an-application-insights-instrumentation-key-in-the-azure-resource-manager-template"></a>在 Azure 资源管理器模板中应用 Application Insights 检测密钥

1. 如果尚未下载该模板，请在 [GitHub](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json) 中下载。

2. 查找 Application Insights 密钥。
   
   ![密钥位置](./media/enable-profiler-compute/copyaikey.png)

3. 替换模板值。
   
   ![模板中被替换的值](./media/enable-profiler-compute/copyaikeytotemplate.png)

## <a name="create-an-azure-vm-to-host-the-web-application"></a>创建 Azure VM 以托管 Web 应用程序
1. 创建安全字符串用于保存密码。

   ```
   $password = ConvertTo-SecureString -String "Replace_With_Your_Password" -AsPlainText -Force
   ```

2. 部署 Azure 资源管理器模板。

   在 PowerShell 控制台中，将目录切换到包含所述资源管理器模板的文件夹。 若要部署该模板，请运行以下命令：

   ```
   New-AzureRmResourceGroupDeployment -ResourceGroupName "Replace_With_Resource_Group_Name" -TemplateFile .\WindowsVirtualMachine.json -adminUsername "Replace_With_your_user_name" -adminPassword $password -dnsNameForPublicIP "Replace_WIth_your_DNS_Name" -Verbose
   ```

成功运行脚本后，资源组中应会出现一个名为 **MyWindowsVM** 的 VM。

## <a name="configure-web-deploy-on-the-vm"></a>在 VM 上配置 Web 部署
确保 VM 上已启用“Web 部署”，以便可以从 Visual Studio 发布 Web 应用程序。

若要通过 WebPI 在 VM 上手动安装 Web 部署，请参阅[在 IIS 8.0 或更高版本上安装和配置 Web 部署](https://docs.microsoft.com/en-us/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)。 有关如何使用 Azure 资源管理器模板自动安装 Web 部署的示例，请参阅[创建、配置 Web 应用程序并将其部署到 Azure VM](https://azure.microsoft.com/en-us/resources/templates/201-web-app-vm-dsc/)。

若要部署 ASP.NET MVC 应用程序，请转到“服务器管理器”，选择“添加角色和功能” > “Web 服务器(IIS)” > “Web 服务器” > “应用程序开发”，并在服务器上启用 ASP.NET 4.5。

![添加 ASP.NET](./media/enable-profiler-compute/addaspnet45.png)

## <a name="install-the-azure-application-insights-sdk-for-your-project"></a>为项目安装 Azure Application Insights SDK
1. 在 Visual Studio 中打开 ASP.NET Web 应用程序。

2. 右键单击项目，选择“添加” > “连接的服务”。

3. 选择“Application Insights”。

4. 遵照页面上的说明操作。 选择之前创建的 Application Insights 资源。

5. 选择“注册”按钮。


## <a name="publish-the-project-to-an-azure-vm"></a>将项目发布到 Azure VM
可通过多种方式将应用程序发布到 Azure VM。 其中一种方式是使用 Visual Studio 2017。

1. 右键单击项目，选择“发布”。

2. 选择“Microsoft Azure 虚拟机”作为发布目标并遵循相应的步骤。

   ![Publish-FromVS](./media/enable-profiler-compute/publishtoVM.png)

3. 针对应用程序运行负载测试。 Application Insights 实例门户网页中应会显示结果。


## <a name="enable-the-profiler"></a>启用探查器
1. 转到 Application Insights 的“性能”边栏选项卡并选择“配置”。
   
   ![“配置”图标](./media/enable-profiler-compute/enableprofiler1.png)
 
2. 选择“启用探查器”。
   
   ![“启用探查器”图标](./media/enable-profiler-compute/enableprofiler2.png)

## <a name="add-a-performance-test-to-your-application"></a>对应用程序添加性能测试
通过这些步骤就可以收集一些要在 Application Insights Profiler 中显示的样本数据：

1. 浏览到前面创建的 Application Insights 资源。 

2. 转到“可用性”边栏选项卡，添加一项可将 Web 请求发送到应用程序 URL 的性能测试。 

   ![添加性能测试](./media/enable-profiler-compute/AvailabilityTest.png)

## <a name="view-your-performance-data"></a>查看性能数据

1. 等待 10-15 分钟，让探查器收集和分析数据。 

2. 转到 Application Insights 资源中的“性能”边栏选项卡，并查看应用程序在承受负载时的表现。

   ![查看性能](./media/enable-profiler-compute/aiperformance.png)

3. 选择“示例”下面的图标，打开“跟踪视图”边栏选项卡。

   ![打开“跟踪视图”边栏选项卡](./media/enable-profiler-compute/traceview.png)


## <a name="work-with-an-existing-template"></a>使用现有模板

1. 在部署模板中找到 Azure 诊断资源声明。
   
   如没有声明，可创建一个与如下示例类似的声明。 可以从 [Azure 资源浏览器网站](https://resources.azure.com)更新模板。

2. 将发布服务器由 `Microsoft.Azure.Diagnostics` 更改为 `AIP.Diagnostics.Test`。

3. 对于 `typeHandlerVersion`，请使用 `0.0`。

4. 请确保将 `autoUpgradeMinorVersion` 设置为 `true`。

5. 在 `WadCfg` 设置对象中添加新的 `ApplicationInsightsProfiler` 接收器实例，如以下示例所示：

```
"resources": [
        {
          "type": "extensions",
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "apiVersion": "2016-03-30",
          "properties": {
            "publisher": "AIP.Diagnostics.Test",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "WadCfg": {
                "SinksConfig": {
                  "Sink": [
                    {
                      "name": "Give a descriptive short name. E.g.: MyApplicationInsightsProfilerSink",
                      "ApplicationInsightsProfiler": "Enter the Application Insights instance instrumentation key guid here"
                    }
                  ]
                },
                "DiagnosticMonitorConfiguration": {
                    ...
                }
                ...
              }
              ...
            }
            ...
          }
          ...
]
```

## <a name="enable-the-profiler-on-virtual-machine-scale-sets"></a>在虚拟机规模集上启用探查器
下载 [WindowsVirtualMachineScaleSet.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json) 模板以了解如何启用探查器。 将 VM 模板中的相同更改应用到虚拟机规模集的诊断扩展资源。

请确保规模集中的每个实例具有 Internet 访问权限。 探查器代理可将收集的样本发送到 Application Insights 以进行显示和分析。

## <a name="enable-the-profiler-on-service-fabric-applications"></a>在 Service Fabric 应用程序中启用探查器
1. 预配 Service Fabric 群集，使 Azure 诊断扩展安装了探查器代理。

2. 在项目中安装 Application Insights SDK 并配置 Application Insights 密钥。

3. 添加应用程序代码以检测遥测。

### <a name="provision-the-service-fabric-cluster-to-have-the-azure-diagnostics-extension-that-installs-the-profiler-agent"></a>预配 Service Fabric 群集，使 Azure 诊断扩展安装了探查器代理
Service Fabric 群集可能是安全的，也可能是不安全的。 可将一个网关群集设置为不安全的群集，以便不需要证书即可访问。 托管业务逻辑和数据的群集应是安全的。 可以在安全和不安全的 Service Fabric 群集上启用探查器。 本演练使用不安全的群集作为示例来说明需要做出哪些更改才能启用探查器。 可通过相同的方式预配安全群集。

1. 下载 [ServiceFabricCluster.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)。 与在 VM 和虚拟机规模集上操作一样，将 `Application_Insights_Key` 替换为自己的 Application Insights密钥：

   ```
   "publisher": "AIP.Diagnostics.Test",
                 "settings": {
                   "WadCfg": {
                     "SinksConfig": {
                       "Sink": [
                         {
                           "name": "MyApplicationInsightsProfilerSinkVMSS",
                           "ApplicationInsightsProfiler": "[Application_Insights_Key]"
                         }
                       ]
                     },
   ```

2. 使用 PowerShell 脚本部署模板：

   ```
   Login-AzureRmAccount
   New-AzureRmResourceGroup -Name [Your_Resource_Group_Name] -Location [Your_Resource_Group_Location] -Verbose -Force
   New-AzureRmResourceGroupDeployment -Name [Choose_An_Arbitrary_Name] -ResourceGroupName [Your_Resource_Group_Name] -TemplateFile [Path_To_Your_Template]

   ```

### <a name="install-the-application-insights-sdk-in-the-project-and-configure-the-application-insights-key"></a>在项目中安装 Application Insights SDK 并配置 Application Insights 密钥
通过 [NuGet 包](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)安装 Application Insights SDK。 确保安装稳定版本（2.3 或更高版本）。 

有关在项目中配置 Application Insights 的信息，请参阅[将 Service Fabric 用于 Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)。

### <a name="add-application-code-to-instrument-telemetry"></a>添加应用程序代码以检测遥测
1. 对于想要检测的任何代码片段，请在其两侧添加 using 语句。 

   在以下示例中，`RunAsync` 方法执行一些操作，启动遥测后，`telemetryClient` 类捕获遥测数据。 事件的名称需在整个应用程序中保持唯一。

   ```
   protected override async Task RunAsync(CancellationToken cancellationToken)
       {
           // TODO: Replace the following sample code with your own logic
           //       or remove this RunAsync override if it's not needed in your service.

           while (true)
           {
               using( var operation = telemetryClient.StartOperation<RequestTelemetry>("[Insert_Event_Unique_Name]"))
               {
                   cancellationToken.ThrowIfCancellationRequested();

                   ++this.iterations;

                   ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", this.iterations);

                   await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
               }

           }
       }
   ```

2. 将应用程序部署到 Service Fabric 群集。 等待应用运行 10 分钟。 为获得更好的效果，可以在该应用上运行负载测试。 转到 Application Insights 门户的“性能”边栏选项卡，应会看到显示的探查跟踪示例。

<!---
Commenting out these sections for now
## Enable the Profiler on Cloud Services applications
[TODO]
## Enable the Profiler on classic Azure Virtual Machines
[TODO]
## Enable the Profiler on on-premise servers
[TODO]
--->

## <a name="next-steps"></a>后续步骤

- 在[探查器故障排除](app-insights-profiler.md#troubleshooting)中获得有关排查探查器问题的帮助。

- 在 [Application Insights Profiler](app-insights-profiler.md) 中详细了解探查器。

