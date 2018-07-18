---
title: 如何将 Azure 诊断 (.NET) 与云服务配合使用 | Microsoft Docs
description: 使用 Azure 诊断从 Azure 云服务收集数据，以用于调试、衡量性能、监视和流量分析等目的。
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: ''
ms.assetid: 89623a0e-4e78-4b67-a446-7d19a35a44be
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2017
ms.author: adegeo
ms.openlocfilehash: a8d6b16fa363062e06d48bfc5af2ca37697d5cd8
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
ms.locfileid: "29460886"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>在 Azure 云服务中启用 Azure 诊断
有关 Azure 诊断的背景信息，请参阅 [Azure 诊断概述](../azure-diagnostics.md)。

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>如何在辅助角色中启用诊断
本演练介绍如何实现使用 .NET EventSource 类发出遥测数据的 Azure 辅助角色。 Azure Diagnostics 用于收集遥测数据，并将其存储在一个 Azure 存储帐户中。 创建辅助角色时，Visual Studio 将在适用于 .NET 2.4 和更低版本的 Azure SDK 中，自动启用 Diagnostics 1.0 作为解决方案的一部分。 以下说明介绍了创建辅助角色、从解决方案禁用 Diagnostics 1.0，以及在辅助角色中部署 Diagnostics 1.2 或 1.3 的过程。

### <a name="prerequisites"></a>先决条件
本文假定你具有 Azure 订阅，并要将 Visual Studio 与 Azure SDK 配合使用。 如果没有 Azure 订阅，可以注册[免费试用版][Free Trial]。 请确保[安装并配置 Azure PowerShell 0.8.7 版或更高版本][Install and configure Azure PowerShell version 0.8.7 or later]。

### <a name="step-1-create-a-worker-role"></a>步骤 1：创建辅助角色
1. 启动 **Visual Studio**。
2. 从面向 .NET Framework 4.5 的云模板创建 Azure 云服务项目。  将该项目命名为“WadExample”。
3. 选择“辅助角色”并单击“确定”。 随后将创建该项目。
4. 在“解决方案资源管理器”中，双击 WorkerRole1 属性文件。
5. 在“配置”选项卡中，取消选中“启用诊断”，以禁用 Diagnostics 1.0（Azure SDK 2.4 和更低版本）。
6. 生成解决方案以验证无误。

### <a name="step-2-instrument-your-code"></a>步骤 2：检测代码
将 WorkerRole.cs 的内容替换为以下代码。 继承自 [EventSource 类][EventSource Class]的 SampleEventSourceWriter 类实现四个日志记录方法：**SendEnums**、**MessageMethod**、**SetOther** 和 **HighFreq**。 **WriteEvent** 方法的第一个参数定义相关事件的 ID。 Run 方法实现一个无限循环，该循环每隔 10 秒调用 **SampleEventSourceWriter** 类中实现的每个日志记录方法。

```csharp
using Microsoft.WindowsAzure.ServiceRuntime;
using System;
using System.Diagnostics;
using System.Diagnostics.Tracing;
using System.Net;
using System.Threading;

namespace WorkerRole1
{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
}
```


### <a name="step-3-deploy-your-worker-role"></a>步骤 3：部署辅助角色

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. 通过选择解决方案资源管理器中的 **WadExample** 项目，然后在“生成”菜单中选择“发布”，在 Visual Studio 中将辅助角色部署到 Azure。
2. 选择订阅。
3. 在“Microsoft Azure 发布设置”对话框中，选择“新建...”。
4. 在“创建云服务和存储帐户”对话框中输入一个“名称”（例如“WadExample”），然后选择区域或地缘组。
5. 将“环境”设置为“过渡”。
6. 适当地修改任何其他**设置**，并单击“发布”。
7. 完成部署后，在 Azure 门户中验证云服务是否处于“正在运行”状态。

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>步骤 4：创建 Diagnostics 配置文件并安装扩展
1. 通过执行以下 PowerShell 命令来下载公共配置文件架构定义：

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. 右键单击 **WorkerRole1** 项目并选择“添加” -> “新建项...”，将 XML 文件添加到 **WorkerRole1** 项目中。 -> “Visual C# 项” -> “数据” -> “XML 文件”。 将该文件命名为“WadExample.xml”。

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. 将 WadConfig.xsd 与配置文件相关联。 确保 WadExample.xml 编辑器窗口是活动的窗口。 按 **F4** 打开“属性”窗口。 在“属性”窗口中单击“架构”属性。 在“架构”属性中 单击“...”。 单击“添加...” 按钮并导航到 XSD 文件的保存位置，然后选择文件 WadConfig.xsd。 单击“确定”。

4. 将 WadExample.xml 配置文件的内容替换为以下 XML 并保存该文件。 此配置文件定义两个要收集的性能计数器：一个对应于 CPU 使用率，另一个对应于内存使用率。 配置将定义对应于 SampleEventSourceWriter 类中方法的四个事件。

```xml
<?xml version="1.0" encoding="utf-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <WadCfg>
    <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      <PerformanceCounters scheduledTransferPeriod="PT1M">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      </PerformanceCounters>
      <EtwProviders>
        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          <Event id="1" eventDestination="EnumsTable"/>
          <Event id="2" eventDestination="MessageTable"/>
          <Event id="3" eventDestination="SetOtherTable"/>
          <Event id="4" eventDestination="HighFreqTable"/>
          <DefaultEvents eventDestination="DefaultTable" />
        </EtwEventSourceProviderConfiguration>
      </EtwProviders>
    </DiagnosticMonitorConfiguration>
  </WadCfg>
</PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>步骤 5：在辅助角色上安装 Diagnostics
用于在 Web 或辅助角色上管理 Diagnostics 的 PowerShell cmdlet 为：Set-AzureServiceDiagnosticsExtension、Get-AzureServiceDiagnosticsExtension 和 Remove-AzureServiceDiagnosticsExtension。

1. 打开 Azure PowerShell。
2. 执行脚本以在辅助角色上安装 Diagnostics（将 *StorageAccountKey* 替换为 wadexample 存储帐户的存储帐户密钥，并将 *config_path* 替换为 *WadExample.xml* 文件的路径）：

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>步骤 6：查看遥测数据
在 Visual Studio 的“服务器资源管理器”中，导航到 wadexample 存储帐户。 在云服务大约运行 5 分钟后，你应该会看到表 WADEnumsTable、WADHighFreqTable、WADMessageTable、WADPerformanceCountersTable 和 WADSetOtherTable。 双击其中一个表即可查看已收集的遥测数据。

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>配置文件架构
诊断配置文件定义启动诊断代理时用于初始化诊断配置设置的值。 有关有效值和示例，请参阅[最新架构参考](https://msdn.microsoft.com/library/azure/mt634524.aspx)。

## <a name="troubleshooting"></a>故障排除
如果遇到问题，请参阅 [Azure 诊断疑难解答](../azure-diagnostics-troubleshooting.md)，以获取有关常见问题的帮助。

## <a name="next-steps"></a>后续步骤
若要更改你收集的数据、排查问题或者了解有关诊断的一般信息，请参阅[有关 Azure 虚拟机的诊断文章列表](../monitoring-and-diagnostics/azure-diagnostics.md#cloud-services-using-azure-diagnostics)。

[EventSource Class]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: http://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
