---
title: 将来宾 OS 指标发送到 Azure Monitor 指标存储经典云服务
description: 将来宾 OS 指标发送到 Azure Monitor 指标存储经典云服务
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: be27ff3f8dda3209a011c3ad79d1a7a1f1d259fe
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986908"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-service"></a>将来宾 OS 指标发送到 Azure Monitor 指标存储经典云服务

Azure Monitor [ Windows Azure诊断扩展](azure-diagnostics.md) (WAD) 可以从作为虚拟机、云服务或 Service Fabric 群集的一部分运行的来宾操作系统（来宾 OS）中收集指标和日志。  该扩展可以将遥测发送到之前链接的文章中列出的许多不同位置。  

本文介绍了将 Azure 经典云服务的来宾 OS 性能指标发送到 Azure Monitor 指标存储的过程。 自 WAD 1.11 版起，可以将指标直接写入已收集标准平台指标的 Azure Monitor 指标存储。 将它们存储在此位置可以访问可用于平台指标的相同操作。  操作包括实时警报、图表、路由、对 REST API 的访问等详细信息。  在过去，WAD 扩展已写入到 Azure 存储，但不是在 Azure Monitor 数据存储中。  

本文中所述的过程仅适用于 Azure 云服务上的性能计数器。 它不适用于其他自定义指标。 
   

## <a name="pre-requisites"></a>先决条件

- 你必须是 Azure 订阅的[服务管理员或共同管理员](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) 

- 你的订阅必须注册到 [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- 你需要安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1)，也可以使用 [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 


## <a name="provision-cloud-service-and-storage-account"></a>预配云服务和存储帐户 

1. 创建并部署经典云服务（可以在[此处](../cloud-services/cloud-services-dotnet-get-started.md)找到经典云服务应用程序和部署示例）。 

2. 可以使用现有存储帐户，也可以部署新的存储帐户。 如果存储帐户与你刚才创建的经典云服务在同一区域中，那就最好了。 在 Azure 门户中，导航到“存储帐户”资源边栏选项卡并选择“密钥”。 记下存储帐户名称和存储帐户密钥，在后面的步骤中将使用它们。

   ![存储帐户密钥](./media/metrics-store-custom-guestos-classic-cloud-service/storage-keys.png)



## <a name="create-a-service-principal"></a>创建服务主体 

根据 ../azure/azure-resource-manager/resource-group-create-service-principal-portal 中的说明在你的 Azure Active Directory 租户中创建一个服务主体。 在完成此过程时记下以下内容： 
  - 对于单一登录 URL，可以放入任何 URL。  
  - 为此应用创建新的客户端机密  
  - 保存密钥和客户端 ID，以便在后面的步骤中使用。  

对于在前面的步骤中创建的应用，为其授予你希望发布其指标的资源的“监视指标发布者”权限。 如果你计划使用此应用为许多资源发布自定义指标，则可以在资源组或订阅级别授予这些权限。  

> [!NOTE]
> 诊断扩展使用服务主体向 Azure Monitor 证明身份，并发布云服务的指标。 

## <a name="author-diagnostics-extension-configuration"></a>创作诊断扩展配置 

准备 WAD 诊断扩展配置文件。 此文件决定了诊断扩展会针对云服务收集哪些日志和性能计数器。 下面是一个示例诊断配置文件。  

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

在配置文件中将收集性能计数器列表的节中，添加 Azure Monitor 接收器。 此条目可确保指定的所有性能计数器都作为指标路由到 Azure Monitor。 根据需要任意添加/删除性能计数器。 

```XML
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink"> 
 <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
  … 
</PerformanceCounters> 
```
最后，在专用配置中，添加一个 *Azure Monitor Account* 节。 输入在前面的步骤中创建的服务主体客户端 ID 和机密。 

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

启动 PowerShell 并登录到 Azure 

```PowerShell
Login-AzureRmAccount 
```

使用以下命令将在前面的步骤中创建的存储帐户的详细信息存储在变量中。 

```PowerShell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```
 
类似地，使用以下命令将诊断文件路径设置为一个变量。 

```PowerShell
$diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>” 
```
 
使用以下命令通过配置了 Azure Monitor 接收器的诊断文件将诊断扩展部署到云服务 

```PowerShell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```
 
> [!NOTE] 
> 在安装诊断扩展的过程中，还必须提供一个存储帐户。 在诊断配置文件中指定的任何日志和/或性能计数器将写入到指定的存储帐户中。  

## <a name="plot-metrics-in-the-azure-portal"></a>在 Azure 门户中绘制指标 

导航到 Azure 门户 

 ![指标 Azure 门户](./media/metrics-store-custom-guestos-classic-cloud-service/navigate-metrics.png)

1. 在左侧菜单中，单击“监视” 

1. 在“监视”边栏选项卡中，单击“指标预览”选项卡 

1. 在资源下拉列表中，选择你的经典云服务 

1. 在命名空间下拉列表中，选择“azure.vm.windows.guest” 

1. 在指标下拉列表中，选择“内存\已提交的使用字节数”。 

可以通过使用维度筛选和拆分功能选择查看由特定角色使用的总内存以及由每个角色实例使用的总内存。 

 ![指标 Azure 门户](./media/metrics-store-custom-guestos-classic-cloud-service/metrics-graph.png)

## <a name="next-steps"></a>后续步骤
- 详细了解[自定义指标](metrics-custom-overview.md)。



