---
title: 在 Azure Log Analytics 中使用适用于 IIS 的 blob 存储和适用于事件的表存储 | Microsoft 文档
description: Log Analytics 可以读取将诊断写入到表存储或将 IIS 日志写入到 Blob 存储的 Azure 服务的日志。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: bf444752-ecc1-4306-9489-c29cb37d6045
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 8f923cc081ea652c8e32d4109225044c70c8767d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128735"
---
# <a name="use-azure-blob-storage-for-iis-and-azure-table-storage-for-events-with-log-analytics"></a>将适用于 IIS 的 Azure Blob 存储和适用于事件的 Azure 表存储与 Log Analytics 配合使用

Log Analytics 可以读取将诊断写入到表存储或将 IIS 日志写入到 Blob 存储的以下服务的日志：

* Service Fabric 群集（预览版）
* 虚拟机
* Web/辅助角色

必须启用 Azure 诊断之后，Log Analytics 才可以收集这些资源的数据。

诊断已启用后，可以使用 Azure 门户或 PowerShell 配置 Log Analytics 收集日志。

Azure 诊断是用于从 Azure 中运行的辅助角色、Web 角色或虚拟机收集诊断数据的 Azure 扩展。 该数据存储在 Azure 存储帐户中，可以由 Log Analytics 进行收集。

为了使 Log Analytics 能够收集这些 Azure 诊断日志，这些日志必须位于以下位置：

| 日志类型 | 资源类型 | 位置 |
| --- | --- | --- |
| IIS 日志 |虚拟机 <br> Web 角色 <br> 辅助角色 |wad-iis-logfiles（Blob 存储） |
| Syslog |虚拟机 |LinuxsyslogVer2v0（表存储） |
| Service Fabric 操作事件 |Service Fabric 节点 |WADServiceFabricSystemEventTable |
| Service Fabric Reliable Actor 事件 |Service Fabric 节点 |WADServiceFabricReliableActorEventTable |
| Service Fabric Reliable Service 事件 |Service Fabric 节点 |WADServiceFabricReliableServiceEventTable |
| Windows 事件日志 |Service Fabric 节点 <br> 虚拟机 <br> Web 角色 <br> 辅助角色 |WADWindowsEventLogsTable（表存储） |
| Windows ETW 日志 |Service Fabric 节点 <br> 虚拟机 <br> Web 角色 <br> 辅助角色 |WADETWEventTable（表存储） |

> [!NOTE]
> 当前不支持 Azure 网站中的 IIS 日志。
>
>

对于虚拟机，可以选择将 [Log Analytics 代理](log-analytics-azure-vm-extension.md)安装到虚拟机来支持其他见解。 除了能够分析 IIS 日志和事件日志之外，还可以执行其他分析，包括配置更改跟踪、SQL 评估和更新评估。

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>在虚拟机中为事件日志和 IIS 日志收集启用 Azure 诊断
通过以下过程，使用 Microsoft Azure 门户在虚拟机中为事件日志和 IIS 日志收集启用 Azure 诊断。

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>使用 Azure 门户在虚拟机中启用 Azure 诊断
1. 创建虚拟机时安装 VM 代理。 如果虚拟机已存在，请验证 VM 代理是否已安装。

   * 在 Azure 门户中，导航到虚拟机、选择“可选配置”、选择“诊断”，然后将“状态”设置为“开”。

     完成后，VM 已安装 Azure 诊断扩展，并且该扩展正在运行。 此扩展将负责收集诊断数据。
2. 在现有 VM 中启用监视，并配置事件日志记录。 可以启用 VM 级别的诊断。 若要启用诊断并配置事件日志记录，请执行以下步骤：

   1. 选择 VM。
   2. 单击“监视”。
   3. 单击“诊断”。
   4. 将“状态”设置为“开”。
   5. 选择想要收集的每个诊断日志。
   6. 单击“确定”。

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>在 Web 角色中为 IIS 日志和事件收集启用 Azure 诊断
有关启用 Azure 诊断的常规步骤，请参阅[如何在云服务中启用诊断](../cloud-services/cloud-services-dotnet-diagnostics.md)。 下面的说明使用此信息并进行自定义以用于 Log Analytics。

在 Azure 诊断已启用的情况下：

* 默认存储 IIS 日志，日志数据按 scheduledTransferPeriod 传输间隔进行传输。
* 默认情况下，不会传输 Windows 事件日志。

### <a name="to-enable-diagnostics"></a>启用诊断
若要启用 Windows 事件日志，或要更改 scheduledTransferPeriod，可使用 XML 配置文件 (diagnostics.wadcfg) 配置 Azure 诊断，如[步骤 4：创建诊断配置文件并安装扩展](../cloud-services/cloud-services-dotnet-diagnostics.md)中所示

以下示例配置文件从应用程序日志和系统日志中收集 IIS 日志和所有事件：

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

确保 ConfigurationSettings 指定了存储帐户，如以下示例中所示：

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

可以在 Azure 门户中的存储帐户仪表板的“管理访问密钥”下找到 **AccountName** 和 **AccountKey** 值。 连接字符串的协议必须为 **https**。

更新的诊断配置应用到云服务并且该服务正在将诊断写入到 Azure 存储后，可以配置 Log Analytics。

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>使用 Azure 门户从 Azure 存储中收集日志
可以使用 Azure 门户将 Log Analytics 配置为收集以下 Azure 服务的日志：

* Service Fabric 群集
* 虚拟机
* Web/辅助角色

在 Azure 门户中，导航到 Log Analytics 工作区，并执行以下任务：

1. 单击“存储帐户日志”
2. 单击“添加”任务
3. 选择包含诊断日志的存储帐户
   * 此帐户可以是经典存储帐户或 Azure 资源管理器存储帐户
4. 选择要收集的日志的数据类型
   * 选项包括 IIS 日志、事件、Syslog (Linux)、ETW 日志、Service Fabric 事件
5. 将基于数据类型自动填充源的值，不能更改
6. 单击“确定”保存配置

对于其他存储帐户和想要 Log Analytics 收集的数据类型，请重复执行步骤 2 到步骤 6。

在大约 30 分钟过后，能够在 Log Analytics 中看到存储帐户中的数据。 在应用了配置后，只能看到写入到存储中的数据。 Log Analytics 不会从存储帐户中读取预先存在的数据。

> [!NOTE]
> 该门户不会验证源是否存在于存储帐户中，或者是否正在写入新数据。
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>使用 PowerShell 在虚拟机中为事件日志和 IIS 日志收集启用 Azure 诊断
通过[将 Log Analytics 配置为编制 Azure 诊断索引](log-analytics-powershell-workspace-configuration.md#configuring-log-analytics-to-index-azure-diagnostics)中的步骤，将 PowerShell 用于从写入表存储的 Azure 诊断读取。

使用 Azure PowerShell 可以更精确地指定要写入 Azure 存储的事件。
有关详细信息，请参阅[在 Azure 虚拟机中启用诊断](../virtual-machines-dotnet-diagnostics.md)。

可以使用以下 PowerShell 脚本启用和更新 Azure 诊断。
还可以将此脚本与自定义日志记录配置结合使用。
修改脚本以便设置存储帐户、服务名称和虚拟机名称。
脚本将 cmdlet 用于经典虚拟机。

查看以下脚本示例、复制它、根据需要修改它、将该示例保存为 PowerShell 脚本文件，然后运行该脚本。

```
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>后续步骤
* 针对支持的 Azure 服务[收集 Azure 服务的日志和指标](log-analytics-azure-storage.md)。
* [启用解决方案](log-analytics-add-solutions.md)深入分析数据。
* [使用搜索查询](log-analytics-log-searches.md)分析数据。
