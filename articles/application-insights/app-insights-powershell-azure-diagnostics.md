---
title: 使用 PowerShell 将 Azure 诊断发送到 Application Insights | Microsoft Docs
description: 自动配置 Azure 诊断，通过管道发送到 Application Insights。
services: application-insights
documentationcenter: .net
author: sbtron
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/17/2015
ms.author: awills

---
# 使用 PowerShell 将 Azure 诊断发送到 Application Insights
可以将 [Microsoft Azure](https://azure.com) [配置为](app-insights-azure-diagnostics.md)向 [Visual Studio Application Insights](app-insights-overview.md) 发送 Azure 诊断。该诊断与 Azure Cloud Service 和 Azure VM 有关。它们是对使用 Application Insights SDK 从应用内发送的遥测的补充。作为在 Azure 中自动处理新建资源过程的一部分，你可以使用 PowerShell 配置诊断。

## 在部署云服务过程中启用诊断扩展
`New-AzureDeployment` cmdlet 具有参数 `ExtensionConfiguration`，它会接收一组诊断配置。可使用 `New-AzureServiceDiagnosticsExtensionConfig` cmdlet 创建这些诊断配置。例如：

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## 在现有的云服务上启用诊断扩展
在现有服务上，使用 `Set-AzureServiceDiagnosticsExtension`。

```ps

    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## 获取当前诊断扩展配置
```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## 删除诊断扩展
```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

如果你已使用不带 Role 参数的 `Set-AzureServiceDiagnosticsExtension` 或 `New-AzureServiceDiagnosticsExtensionConfig` 启用诊断扩展，则可以使用不带 Role 参数的 `Remove-AzureServiceDiagnosticsExtension` 删除该扩展。如果启用扩展时使用了 Role 参数，则删除扩展时也必须使用该参数。

若要从单个角色中删除诊断扩展，请使用以下命令：

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## 另请参阅
* [使用 Application Insights 监视 Azure Cloud Service 应用](app-insights-cloudservices.md)
* [将 Azure 诊断发送到 Application Insights](app-insights-azure-diagnostics.md)
* [自动配置警报](app-insights-powershell-alerts.md)

<!---HONumber=AcomDC_0921_2016-->