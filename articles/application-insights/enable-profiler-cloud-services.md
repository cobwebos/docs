---
title: "为计算资源启用 Azure Application Insights Profiler | Microsoft Docs"
description: "了解如何设置 Profiler"
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
ms.author: sewhee
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: cde0b0bce2e332243e555a72088b8e9eeb680bdb
ms.contentlocale: zh-cn
ms.lasthandoff: 07/28/2017

---

# <a name="how-to-enable-application-insights-profiler-on-azure-compute-resources"></a>如何在 Azure 计算资源上启用 Application Insights Profiler

本演练演示如何在 Azure 计算资源托管的 ASP.NET 应用程序上启用 Application Insights Profiler。 示例包括对虚拟机、虚拟机规模集和 Service Fabric 的支持。 所有示例依赖于支持 Azure 资源管理部署模型的模板。 若要详细了解部署模型，请查看 [Azure 资源管理器与经典部署：了解部署模型和资源状态](/azure-resource-manager/resource-manager-deployment-model)。

## <a name="overview"></a>概述

下图演示了如何针对 Azure 计算资源运行 Profiler。 其中使用 Azure 虚拟机作为示例。

![概述](./media/enable-profiler-compute/overview.png)必须安装适用于 Azure 计算资源的诊断代理组件，才能收集要处理的信息并在 Azure 门户上显示这些信息。 本演练的余下内容提供有关如何安装和配置诊断代理以启用 Application Insights Profiler 的指导。

## <a name="prerequisites-for-the-walkthrough"></a>演练先决条件

* 下载资源管理器部署模板，用于在 VM 或规模集上安装 Profiler 代理。

    [WindowsVirtualMachine.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachine.json) | [WindowsVirtualMachineScaleSet.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachineScaleSet.json)
* 已启用一个 Application Insights 实例用于探查。 请查看 https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler，了解如何执行此操作。
* 已在目标 Azure 计算资源中安装 .NET framework 4.6.1 或更高版本。

## <a name="create-a-resource-group-in-your-azure-subscription"></a>在 Azure 订阅中创建资源组
以下示例演示如何使用 PowerShell 脚本创建资源组：

```
New-AzureRmResourceGroup -Name "Replace_With_Resource_Group_Name" -Location "Replace_With_Resource_Group_Location"
```

## <a name="create-an-application-insights-resource-in-the-resource-group"></a>在资源组中创建 Application Insights 资源

![创建 Application Insights](./media/enable-profiler-compute/createai.png)

## <a name="apply-application-insights-instrumentation-key-in-the-azure-resource-manager-template"></a>在 Azure 资源管理器模板中应用 Application Insights 检测密钥
如果尚未下载该模板，请通过以下链接下载。 [WindowsVirtualMachine.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachine.json)

![查找 AI 密钥](./media/enable-profiler-compute/copyaikey.png)

![替换模板值](./media/enable-profiler-compute/copyaikeytotemplate.png)

## <a name="create-azure-vm-to-host-the-web-application"></a>创建 Azure VM 用于托管 Web 应用程序
* 创建安全字符串用于保存密码
```
$password = ConvertTo-SecureString -String "Replace_With_Your_Password" -AsPlainText -Force
```

* 部署 Azure 资源管理器模板

在 PowerShell 控制台中，将目录切换到包含所述资源管理器模板的文件夹。 若要部署该模板，请运行以下命令：

```
New-AzureRmResourceGroupDeployment -ResourceGroupName "Replace_With_Resource_Group_Name" -TemplateFile .\WindowsVirtualMachine.json -adminUsername "Replace_With_your_user_name" -adminPassword $password -dnsNameForPublicIP "Replace_WIth_your_DNS_Name" -Verbose
```

成功执行脚本后，资源组中应会出现一个名为 *MyWindowsVM* 的 VM。

## <a name="configure-web-deploy-on-the-vm"></a>在 VM 上配置 Web 部署
确保 VM 上已启用“Web 部署”，以便可以从 Visual Studio 发布 Web 应用程序。

可以通过 WebPI 在 VM 上手动安装 Web 部署，具体请参阅：[在 IIS 8.0 或更高版本上安装和配置 Web 部署](https://docs.microsoft.com/en-us/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)

以下示例演示如何使用 Azure 资源管理器模板自动安装 Web 部署，具体请参阅：[创建、配置 Web 应用程序并将其部署到 Azure VM](https://azure.microsoft.com/en-us/resources/templates/201-web-app-vm-dsc/)

若要部署 ASP.NET MVC 应用程序，需要转到“服务器管理器”，选择“添加角色和功能”|“Web 服务器(IIS)”|“Web 服务器”|“应用程序开发”，并在服务器上启用 ASP.NET 4.5。
![添加 ASP.NET](./media/enable-profiler-compute/addaspnet45.png)

## <a name="install-azure-application-insights-sdk-to-your-project"></a>将 Azure Application Insights SDK 安装到项目
* 在 Visual Studio 中打开 ASP.NET Web 应用程序
* 右键单击项目并选择“添加”|“连接的服务”
* 选择“Application Insights”
* 遵照页面上的说明操作。 选择前面创建的 Application Insights 资源
* 单击“注册”按钮


## <a name="publish-the-project-to-azure-vm"></a>将项目发布到 Azure VM
可通过多种方式将应用程序发布到 Azure VM。 其中一种方法是使用 Visual Studio 2017。
若要完成发布过程，请右键单击项目并选择“发布...”。 选择“Azure 虚拟机”作为发布目标并遵循相应的步骤。

![Publish-FromVS](./media/enable-profiler-compute/publishtoVM.png)

针对应用程序运行一些负载测试。 Application Insights 实例门户网页中应会显示结果。


## <a name="enable-the-profiler-in-application-insights"></a>在 Application Insights 中启用 Profiler
转到“Application Insights 性能”边栏选项卡。 单击“配置”图标并启用 Profiler

![启用 Profiler 步骤 1](./media/enable-profiler-compute/enableprofiler1.png)

![启用 Profiler 步骤 2](./media/enable-profiler-compute/enableprofiler2.png)

## <a name="add-an-availability-test-to-your-application"></a>将可用性测试添加到应用程序
浏览到前面创建的 Application Insights 资源。 转到“可用性”边栏选项卡，添加一项可将 Web 请求发送到应用程序 URL 的性能测试。 这样，我们便可以收集一些要在 Application Insights Profiler 中显示的样本数据

![添加性能测试][./media/enable-profiler-compute/add-test.png]

## <a name="view-your-performance-data"></a>查看性能数据

等待 10-15 分钟让 Profiler 收集和分析数据。 然后转到 AI 资源中的“性能”边栏选项卡，并查看应用程序在承受负载时的表现。

![查看性能][./media/enable-profiler-compute/view-aiperformance.png]

单击“示例”下面的图标打开“跟踪视图”边栏选项卡。

![跟踪视图][./media/enable-profiler-compute/traceview.png]


## <a name="work-with-an-existing-template"></a>使用现有模板

1. 在部署模板中找到 Windows Azure 诊断 (WAD) 资源声明。
  * 如果没有该声明，请创建一个声明（在完整示例中了解如何创建）。
  * 可以通过 Azure 资源网站 (https://resources.azure.com) 更新模板。
2. 将发布者从“Microsoft.Azure.Diagnostics”修改为“AIP.Diagnostics.Test”。
3. 为 typeHandlerVersion 使用“0.0”。
4. 确保将 autoUpgradeMinorVersion 设置为 true。
5. 在 WadCfg 设置对象中添加新的 ApplicationInsightsProfiler 接收器实例，如以下示例所示。

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

## <a name="enable-the-profiler-on-virtual-machine-scale-sets"></a>在虚拟机规模集上启用 Profiler
下载 [WindowsVirtualMachineScaleSet.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachineScaleSet.json) 模板以了解如何启用 Profiler。 将 VM 模板中的相同更改应用到虚拟机规模集诊断扩展资源。
确保规模集中的每个实例均可访问 Internet，使 Profiler 代理能够将收集的样本发送到 Application Insights 供分析和显示。

## <a name="enable-the-profiler-on-service-fabric-applications"></a>在 Service Fabric 应用程序中启用 Profiler
目前，在 Service Fabric 应用程序中启用 Profiler 需要做好以下准备：
1. 预配 Service Fabric 群集，使其包含可用于安装 Profiler 代理的 WAD 扩展
2. 在项目中安装 Application Insights SDK 并配置 AI 密钥
3. 添加应用程序代码以检测遥测

## <a name="provision-the-service-fabric-cluster-have-the-wad-extension-that-installs-the-profiler-agent"></a>预配 Service Fabric 群集，使其包含可用于安装 Profiler 代理的 WAD 扩展
Service Fabric 群集可能是安全的，也可能是不安全的。 可将一个网关群集设置为不安全的群集，以便不需要证书即可访问。 托管业务逻辑和数据的群集应是安全的。 可以在安全和不安全的 Service Fabric 群集上启用 Profiler。 本演练使用不安全的群集作为示例来说明需要做出哪些更改才能启用 Profiler。 可通过相同的方式预配安全群集。

下载 [ServiceFabricCluster.json](https://github.com/CFreemanwa/samples/blob/master/ServiceFabricCluster.json)。 与在 VM 和虚拟机规模集上操作一样，将 Application Insights 密钥替换为自己的 AI 密钥：

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

使用 PowerShell 脚本部署模板：
```
Login-AzureRmAccount
New-AzureRmResourceGroup -Name [Your_Resource_Group_Name] -Location [Your_Resource_Group_Location] -Verbose -Force
New-AzureRmResourceGroupDeployment -Name [Choose_An_Arbitrary_Name] -ResourceGroupName [Your_Resource_Group_Name] -TemplateFile [Path_To_Your_Template]

```

## <a name="install-application-insights-sdk-in-the-project-and-configure-ai-key"></a>在项目中安装 Application Insights SDK 并配置 AI 密钥
通过 NuGet 包安装 Application Insights SDK。 确保安装稳定版本 2.3 或更高版本。 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 请参阅[将 Service Fabric 与 Application Insights 配合使用](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)在项目中配置 Application Insights。

## <a name="add-application-code-to-instrument-telemetry"></a>添加应用程序代码以检测遥测
对于想要检测的任何代码片段，请在其两侧添加 using 语句。 在以下示例中，RunAsync 方法执行一些操作，启动遥测后，telemetryClient 类捕获遥测数据。 事件的名称需在整个应用程序中保持唯一。

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

将应用程序部署到 Service Fabric 群集。 等待应用运行 10 分钟。 为获得更好的效果，可以在该应用上运行负载测试。 转到 Application Insights 门户的“性能”边栏选项卡，应会看到显示的探查跟踪示例。

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

- 在 [Profiler 故障排除](app-insights-profiler.md#troubleshooting)中获得有关排查 Profiler 问题的帮助

- 在 [Application Insights Profiler](app-insights-profiler.md) 中详细了解 Profiler。

