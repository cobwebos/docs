---
title: 将经典 Windows VM 指标发送到 Azure Monitor 指标数据库
description: 将来宾 OS 指标发送到适用于 Windows 虚拟机（经典）的 Azure Monitor 数据存储
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: ''
ms.openlocfilehash: d911a6b65ba0d63bca09437f6451019addcbc0b3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977619"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metrics-database-for-a-windows-virtual-machine-classic"></a>将来宾 OS 指标发送到 Windows 虚拟机的 Azure Monitor 指标数据库（经典）

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor [诊断扩展](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)（称为“WAD”或“诊断”）支持从作为虚拟机、云服务或 Service Fabric 群集的一部分运行的来宾操作系统（来宾 OS）中收集指标和日志。 该扩展可将遥测数据发送到[许多不同的位置](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)。

本文介绍将 Windows 虚拟机（经典）的来宾 OS 性能指标发送到 Azure Monitor 指标数据库的过程。 自诊断版本 1.11 版起，可将指标直接写入已收集标准平台指标的 Azure Monitor 指标存储。 

将它们存储在此位置可以访问在处理平台指标时所执行的相同操作。 操作包括近实时警报、图表绘制、路由、从 REST API 访问，等等。 在过去，诊断扩展将数据写入 Azure 存储而不是 Azure Monitor 数据存储。 

本文概述的过程仅适用于运行 Windows 操作系统的经典虚拟机。

## <a name="prerequisites"></a>必备组件

- 你必须是 Azure 订阅的[服务管理员或共同管理员](../../cost-management-billing/manage/add-change-subscription-administrator.md)。 

- 你的订阅必须已注册到 [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services)。 

- 需要安装 [Azure PowerShell](/powershell/azure) 或 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)。

- VM 资源必须位于[支持自定义指标的区域](metrics-custom-overview.md#supported-regions)中。

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>创建经典虚拟机和存储帐户

1. 使用 Azure 门户创建经典 VM。
   ![创建经典 VM](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. 创建此 VM 时，请选择创建新经典存储帐户的选项。 我们会在后面的步骤中使用此存储帐户。

1. 在 Azure 门户中，转到“存储帐户”资源边栏选项卡。 选择“密钥”，并记下存储帐户名称和存储帐户密钥。 在后续步骤中需要使用此信息。
   ![存储访问密钥](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>创建服务主体

使用[创建服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)中的说明在 Azure Active Directory 租户中创建一个服务主体。 在完成此过程时记下以下内容： 
- 为此应用创建新的客户端机密。
- 请保存密钥和客户端 ID，以便在后面的步骤中使用。

为此应用授予对资源的“监视指标发布者”权限，你希望发出该资源的指标。 可以使用资源组或整个订阅。  

> [!NOTE]
> 诊断扩展使用服务主体向 Azure Monitor 证明身份，并发出经典 VM 的指标。

## <a name="author-diagnostics-extension-configuration"></a>创作诊断扩展配置

1. 准备诊断扩展配置文件。 此文件决定了诊断扩展会针对经典 VM 收集哪些日志和性能计数器。 下面是一个示例：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
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
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
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
1. 在诊断文件的“SinksConfig”节中定义一个新的 Azure Monitor 接收器，如下所示：

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide the resource ID of your classic VM </ResourceId>
                <Region>The region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. 在配置文件中列出要收集的性能计数器的部分，将性能计数器路由到 Azure Monitor 接收器“AzMonSink”。

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. 在专用配置中定义 Azure Monitor 帐户。 然后添加用于发出指标的服务主体信息。

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. 在本地保存此文件。

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>将诊断扩展部署到云服务

1. 启动 PowerShell 并登录。

    ```powershell
    Login-AzAccount
    ```

1. 一开始将上下文设置为经典 VM。

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. 设置通过 VM 创建的经典存储帐户的上下文。

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  使用以下命令将诊断文件路径设置为一个变量：

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  在配置 Azure Monitor 接收器的情况下，使用诊断文件准备对经典 VM 进行更新。

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  运行以下命令，部署对 VM 的更新：

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> 在安装诊断扩展的过程中，还必须提供一个存储帐户。 在诊断配置文件中指定的任何日志或性能计数器将写入到指定的存储帐户中。

## <a name="plot-the-metrics-in-the-azure-portal"></a>在 Azure 门户中绘制指标

1.  转到 Azure 门户。 

1.  在左侧菜单中，选择“监视”。

1.  在“监视”边栏选项卡上选择“指标”。

    ![导航指标](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. 在资源下拉菜单中，选择你的经典 VM。

1. 在命名空间下拉菜单中，选择“azure.vm.windows.guest”。

1. 在指标下拉菜单中，选择“内存\已提交的使用字节数”。
   ![绘制指标图表](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>后续步骤
- 详细了解[自定义指标](metrics-custom-overview.md)。

