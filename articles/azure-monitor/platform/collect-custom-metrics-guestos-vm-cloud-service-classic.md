---
title: 将经典云服务指标发送到 Azure Monitor 指标数据库
description: 介绍将 Azure 经典云服务的来宾 OS 性能指标发送到 Azure Monitor 指标存储的过程。
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 3b390ffa20cf3cf79b8fb6311ad05b2978bd5d24
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77655782"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>将来宾 OS 指标发送到 Azure Monitor 指标存储经典云服务 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

使用 Azure Monitor [诊断扩展](diagnostics-extension-overview.md)，可以从作为虚拟机、云服务或 Service Fabric 群集的一部分运行的来宾操作系统（来宾 OS）中收集指标和日志。 该扩展可将遥测数据发送到[许多不同的位置](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)。

本文介绍将 Azure 经典云服务的来宾 OS 性能指标发送到 Azure Monitor 指标存储的过程。 自诊断版本 1.11 版起，可将指标直接写入已收集标准平台指标的 Azure Monitor 指标存储。 

将它们存储在此位置可以访问可用于平台指标的相同操作。 操作包括近实时警报、图表绘制、路由、从 REST API 访问，等等。  在过去，诊断扩展将数据写入 Azure 存储而不是 Azure Monitor 数据存储。  

本文中所述的过程仅适用于 Azure 云服务上的性能计数器。 它不适用于其他自定义指标。 

## <a name="prerequisites"></a>必备条件

- 你必须是 Azure 订阅的[服务管理员或共同管理员](../../cost-management-billing/manage/add-change-subscription-administrator.md)。 

- 你的订阅必须已注册到 [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services)。 

- 需要安装 [Azure PowerShell](/powershell/azure) 或 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)。

- 云服务必须位于[支持自定义指标的区域](metrics-custom-overview.md#supported-regions)中。

## <a name="provision-a-cloud-service-and-storage-account"></a>预配云服务和存储帐户 

1. 创建并部署经典云服务 可在 [Azure 云服务和 ASP.NET 入门](../../cloud-services/cloud-services-dotnet-get-started.md)中找到经典云服务应用程序和部署示例。 

2. 可以使用现有存储帐户，也可以部署新的存储帐户。 如果存储帐户与创建的经典云服务在同一区域中，那就最好了。 在 Azure 门户中，转到“存储帐户”资源边栏选项卡并选择“密钥”。 记下存储帐户名称和存储帐户密钥。 在后续步骤中需要使用此信息。

   ![存储帐户密钥](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>创建服务主体 

遵照[使用门户创建可访问资源的 Azure Active Directory 应用程序和服务主体](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)中的说明，在 Azure Active Directory 租户中创建服务主体。 在完成此过程时请注意以下几点： 

- 可以输入任何 URL 作为登录 URL。  
- 为此应用创建新的客户端机密。  
- 请保存密钥和客户端 ID，以便在后面的步骤中使用。  

对于在前面步骤中创建的应用，请为其授予你希望发布其指标的资源的“监视指标发布者”权限。 如果你计划使用此应用为许多资源发布自定义指标，则可以在资源组或订阅级别授予这些权限。  

> [!NOTE]
> 诊断扩展使用服务主体向 Azure Monitor 证明身份，并发布云服务的指标。

## <a name="author-diagnostics-extension-configuration"></a>创作诊断扩展配置 

准备诊断扩展配置文件。 此文件决定了诊断扩展会针对云服务收集哪些日志和性能计数器。 下面是一个示例诊断配置文件：  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
        </PerformanceCounters> 
        <WindowsEventLog scheduledTransferPeriod="PT1M"> 
          <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" /> 
          <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" /> 
        </WindowsEventLog> 
        <CrashDumps> 
          <CrashDumpConfiguration processName="WaIISHost.exe" /> 
          <CrashDumpConfiguration processName="WaWorkerHost.exe" /> 
          <CrashDumpConfiguration processName="w3wp.exe" /> 
        </CrashDumps> 
        <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" /> 
      </DiagnosticMonitorConfiguration> 
      <SinksConfig> 
      </SinksConfig> 
    </WadCfg> 
    <StorageAccount /> 
  </PublicConfig> 
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <StorageAccount name="" endpoint="" /> 
</PrivateConfig> 
  <IsEnabled>true</IsEnabled> 
</DiagnosticsConfiguration> 
```

在诊断文件的“SinksConfig”节中定义一个新的 Azure Monitor 接收器： 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

在配置文件中将收集性能计数器列表的节中，添加 Azure Monitor 接收器。 此条目可确保指定的所有性能计数器都作为指标路由到 Azure Monitor。 可根据需要添加或删除性能计数器。 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

最后，在专用配置中，添加一个 *Azure Monitor Account* 节。 输入前面创建的服务主体客户端 ID 和机密。 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```

将此诊断文件保存在本地。  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>将诊断扩展部署到云服务 

启动 PowerShell 并登录到 Azure。 

```powershell
Login-AzAccount 
```

使用以下命令存储前面创建的存储帐户的详细信息。 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

类似地，使用以下命令将诊断文件路径设置为一个变量：

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

使用以下命令通过配置了 Azure Monitor 接收器的诊断文件将诊断扩展部署到云服务：  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> 在安装诊断扩展的过程中，还必须提供一个存储帐户。 在诊断配置文件中指定的任何日志或性能计数器将写入到指定的存储帐户中。  

## <a name="plot-metrics-in-the-azure-portal"></a>在 Azure 门户中绘制指标 

1. 转到 Azure 门户。 

   ![指标 Azure 门户](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. 在左侧菜单中，选择“监视”。

3. 在“监视”边栏选项卡中，选择“指标预览”选项卡。

4. 在资源下拉菜单中，选择你的经典云服务。

5. 在命名空间下拉菜单中，选择“azure.vm.windows.guest”。 

6. 在指标下拉菜单中，选择“内存\已提交的使用字节数”。 

可以使用维度筛选和拆分功能查看特定角色或角色实例使用的总内存。 

 ![指标 Azure 门户](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>后续步骤

- 详细了解[自定义指标](metrics-custom-overview.md)。

