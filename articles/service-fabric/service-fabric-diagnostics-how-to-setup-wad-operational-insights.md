<properties
   pageTitle="如何使用 Azure 诊断和 Azure Operational Insights 收集日志 | Microsoft Azure"
   description="本文介绍如何将 Azure 诊断和 Azure Operational Insights 设置为从在 Azure 中运行的 Service Fabric 群集收集日志。"
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.date="10/20/2015"
   wacn.date=""/>


# 使用 Azure 诊断和 Operational Insights 从 Service Fabric 群集收集日志

当你运行 Azure Service Fabric 群集时，最好是从一个中心位置的所有节点中收集日志。从日志放在中心位置可轻松分析和排查群集或该群集中运行的应用程序与服务的问题。上载和收集日志的方式之一是使用可将日志上载到 Azure 存储空间的 Azure 诊断扩展。

Microsoft Operations Management Suite 中的 Azure Operational Insights 是一种 SaaS 解决方案，可用于轻松分析和搜索日志。以下步骤说明如何在群集的 VM 上设置 Azure 诊断扩展，以便将日志上载到集中存储，然后设置 Operational Insights 来提取日志，以便可以在 Operational Insights 门户中查看日志。

Operational Insights 根据存储日志的存储表的名称，识别从 Service Fabric 群集上载的各种日志源。这意味着，Azure 诊断扩展必须配置为将日志上载到名称与 Operational Insights 搜索目标匹配的存储表。本文档中的配置设置示例将显示存储表的名称。

## 建议的阅读材料
* [Azure 诊断](/documentation/articles/cloud-services-dotnet-diagnostics)（与 Azure 云服务相关，但包含有用的信息和示例）


## 先决条件
以下工具将用来运行本文档中的某些操作：
* [Azure PowerShell](/documentation/articles/powershell-install-configure)
* [Azure Resource Manager 客户端](https://github.com/projectkudu/ARMClient)

## 可以收集的不同日志源
1. **Service Fabric 日志：**由平台发送到标准 ETW 和 EventSource 通道。日志有以下几种类型：
  - 操作事件：Service Fabric 平台执行的操作的日志。示例包括创建应用程序和服务、节点状态更改和升级信息。
  - [执行组件编程模型事件](/documentation/articles/service-fabric-reliable-actors-diagnostics)
  - [Reliable Services 编程模型事件](/documentation/articles/service-fabric-reliable-services-diagnostics)
2. **应用程序事件：**从服务代码发出，使用 Visual Studio 模板提供的 EventSource 帮助器类写出的事件。有关如何从应用程序写入日志的详细信息，请参阅[这篇有关监视和诊断本地计算机安装中的服务的文章](/documentation/articles/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally)。


## 将诊断扩展部署到 Service Fabric 群集以收集和上载日志
收集日志的第一个步骤是将诊断扩展部署在 Service Fabric 群集的每个 VM 上。诊断扩展将收集每个 VM 上的日志，并将它们上载到指定的存储帐户。根据使用的是 Azure 门户还是 Azure Resource Manager，以及是在创建群集时部署的还是针对已存在的群集部署的，步骤稍有不同。让我们看看每个方案的步骤。

### 在通过门户创建群集过程中部署诊断扩展
为了在创建群集时将诊断部署到群集的 VM，我们将使用下图所示的诊断设置。默认为“打开”。
![门户中有关创建群集的 Azure 诊断设置](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/portal-cluster-creation-diagnostics-setting.png)

### 使用 Azure Resource Manager 在创建群集过程中部署诊断扩展
若要使用 Resource Manager 创建群集，需要在创建群集之前，将诊断配置 JSON 添加到整个 Resource Manager 模板。我们将在 Resource Manager 模板示例中提供包含五个 VM 的群集 Resource Manager 模板，并在演示 Resource Manager 模板示例的过程中添加诊断配置。你可以在 Azure 示例库中的以下位置找到该示例：[包含五节点群集的诊断 Resource Manager 模板示例](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype-wad)。

若要查看 Resource Manager 模板中的诊断设置，请搜索“WadCfg”。 若要使用此模板创建群集，只要按上述链接提供的“部署到 Azure”按钮即可。
或者，也可以下载 Resource Manager 示例，进行更改，然后在 Azure PowerShell 窗口中输入 `New-AzureResourceGroupDeployment` 命令，使用修改后的模板创建群集。请参阅以下信息获取需要传入命令的参数。

此外，在调用此部署命令之前，可能需要进行一些设置，包括添加 Azure 帐户 (`Add-AzureAccount`)、选择订阅 (`Select-AzureSubscription`)、切换到 Resource Manager 模式 (`Switch-AzureMode AzureResourceManager`)，以及创建资源组（如果尚未创建）(`New-AzureResourceGroup`)。

```powershell

New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploywadarm"></a>将诊断扩展部署到现有群集
如果现有的群集上未部署诊断，可以使用以下步骤来添加。
使用 JSON WadConfigUpdate.json 和 WadConfigUpdateParams.json 创建两个文件。

##### WadConfigUpdate.json

```json

{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",

  "parameters": {
        "vmNamePrefix": {
      "type": "string"
    },
        "applicationDiagnosticsStorageAccountName": {
      "type": "string"
    },
        "vmCount": {
            "type": "int"
    }
  },

  "resources": [
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(parameters('vmNamePrefix'),copyIndex(0),'/Microsoft.Insights.VMDiagnosticsSettings')]",
            "location": "[resourceGroup().location]",
      "properties": {
        "type": "IaaSDiagnostics",
                "typeHandlerVersion": "1.5",
        "publisher": "Microsoft.Azure.Diagnostics",
                "autoUpgradeMinorVersion": true,
        "settings": {
                    "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                "EtwProviders": {
                    "EtwEventSourceProviderConfiguration": [
                        {
                            "provider": "Microsoft-ServiceFabric-Actors",
                            "scheduledTransferKeywordFilter": "1",
                                        "scheduledTransferPeriod": "PT5M",
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableActorEventTable" }
                        },
                        {
                            "provider": "Microsoft-ServiceFabric-Services",
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableServiceEventTable" },
                                        "scheduledTransferPeriod": "PT5M"
                        }
                    ],
                    "EtwManifestProviderConfiguration": [
                        {
                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                        "scheduledTransferLogLevelFilter": "Information",
                                        "scheduledTransferPeriod": "PT5M",
                            "DefaultEvents": { "eventDestination": "ServiceFabricSystemEventTable" }
                        }
                    ]
                }
            }
    },
                    "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountNamee')]"
                },
                "protectedSettings": {
                    "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                    "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                    "storageAccountEndPoint": "https://core.chinacloudapi.cn/"
                }
            },
            "copy": {
                "name": "vmExtensionLoop",
                "count": "[parameters('vmCount')]"
            }
        }
    ],

    "outputs": {
    }
}
```

##### WadConfigUpdateParams.json
将 vmNamePrefix 替换为创建群集时为 VM 名称选择的前缀。然后，将 vmStorageAccountName 编辑为要从 VM 将日志上载到的存储帐户。

```json

{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmNamePrefix": {
            "value": "VM"
        },
        "applicationDiagnosticsStorageAccountName": {
            "value": "testdiagacc"
        },
        "vmCount": {
            "value": 5
        }
    }
}
```

如上所述创建 JSON 文件之后，请根据环境的具体情况进行更改。然后调用以下命令，并传入 Service Fabric 群集的资源组名称。成功运行此命令后，诊断将部署在所有 VM 上，并开始将群集中的日志上载到指定 Azure 存储帐户中的表。

此外，在调用此部署命令之前，可能需要进行一些设置，包括添加 Azure 帐户 (`Add-AzureAccount`)、选择适当的订阅 (`Select-AzureSubscription`) 以及切换到 Resource Manager 模式 (`Switch-AzureMode AzureResourceManager`)。

```ps

New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToWADConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

## 设置 Operational Insights 以查看和搜索群集日志
诊断已在群集上设置并开始将日志上载到存储帐户后，接下来请设置 Operational Insights，以便可以通过 Operational Insights 门户查看、搜索和查询所有群集日志。

### 创建 Operational Insights 工作区
若要查看创建 Operational Insights 工作区的步骤，请参阅以下文章。请注意，其中描述了创建工作区的两种不同方式。请选择 Azure 门户和基于订阅的方法。在本文件后面的部分中，你需要 Operational Insights 工作区的名称。使用用于创建所有群集资源（包括存储帐户）的同一个订阅创建 Operational Insights 工作区。

[Operational Insights 登记](https://technet.microsoft.com/zh-cn/library/mt484118.aspx)

### 配置 Operational Insights 工作区以显示群集日志
如上所述创建 Operational Insights 工作区之后，接下来请配置工作区，以便从 Azure 存储空间表中提取诊断扩展从群集上载的日志。目前无法通过 Operational Insights 门户来进行此配置，而只能通过 PowerShell 命令完成。运行以下 PowerShell 脚本：

```powershell

    <#
    This script will configure an Operations Management Suite workspace (aka Operational Insights workspace) to read Diagnostics from an Azure Storage account.

    It will enable all supported data types (currently Windows Event Logs, Syslog, Service Fabric Events, ETW Events and IIS Logs).

    It supports both classic and Resource Manager storage accounts.

    If you have more than one OMS workspace you will be prompted for the workspace to configure.

    If you have more than one storage account you will be prompted for which storage account to configure.
    #>

Add-AzureAccount

Switch-AzureMode -Name AzureResourceManager

$validTables = "WADServiceFabric*EventTable", "WADETWEventTable"

function Select-Workspace {

    $workspace = ""

    $allWorkspaces = Get-AzureOperationalInsightsWorkspace  

    switch ($allWorkspaces.Count) {
        0 {Write-Error "No Operations Management Suite workspaces found"}
        1 {return $allWorkspaces}
        default {
            $uiPrompt = "Enter the number corresponding to the workspace you want to configure.`n"

            $count = 1
            foreach ($workspace in $allWorkspaces) {
                $uiPrompt += "$count. " + $workspace.Name + " (" + $workspace.CustomerId + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt) - 1
            $workspace = $allWorkspaces[$answer]
        }  
    }
    return $workspace
}

function Select-StorageAccount {

    $storage = ""

    $allStorageAccounts = (get-azureresource -ResourceType Microsoft.ClassicStorage/storageAccounts) + (get-azureresource -ResourceType Microsoft.Storage/storageAccounts)

    switch ($allStorageAccounts.Count) {
        0 {Write-Error "No storage accounts found"}
        1 {$storage = $allStorageAccounts}
        default {

            $uiPrompt = "Enter the number corresponding to the storage account with diagnostics.`n"

            $count = 1
            foreach ($storageAccount in $allStorageAccounts) {
                $uiPrompt += "$count. " + $storageAccount.Name + " (" + $storageAccount.Location + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt)

            $storage = $allStorageAccounts[$answer - 1]
        }
    }
    return $storage
}

$workspace = Select-Workspace
$storageAccount = Select-StorageAccount

$insightsName = $storageAccount.Name + $workspace.Name

$existingConfig = ""

try
{
    $existingConfig = Get-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -ErrorAction Stop
}
catch [Hyak.Common.CloudException]
{
    # HTTP Not Found is returned if the storage insight doesn't exist
}

if ($existingConfig) {
    Set-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -Tables $validTables -Containers $validContainers

} else {
    if ($storageAccount.ResourceType -eq "Microsoft.ClassicStorage/storageAccounts") {
        Switch-AzureMode -Name AzureServiceManagement
        $key = (Get-AzureStorageKey -StorageAccountName $storageAccount.Name).Primary
        Switch-AzureMode -Name AzureResourceManager
    } else {
        $key = (Get-AzureStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.Name).Key1
    }
    New-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -StorageAccountResourceId $storageAccount.ResourceId -StorageAccountKey $key -Tables $validTables -Containers $validContainers
}
```

配置 Operational Insights 工作区以从存储帐户的 Azure 表中读取数据后，你应该登录到门户，然后转到 Operational Insights 资源的“存储”选项卡。应会显示类似于下面的屏幕：
![Azure 门户中的 Operational Insights 存储配置](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/oi-connected-tables-list.png)

### 在 Operational Insights 中搜索和查看日志
将 Operational Insights 工作区配置为从指定的存储帐户读取日志之后，可能需要长达 10 分钟的时间，日志才会出现在 Operational Insights UI 中。为了确保有新的日志生成，最好将 Service Fabric 应用程序部署到群集，因为它将从 Service Fabric 平台生成操作事件。

1. 若要查看日志，请在 Operational Insights 门户的主页上选择“日志搜索”。
![Operational Insights 日志搜索选项](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/log-search-button-oi.png)

2. 在日志搜索页中，使用查询 **Type=ServiceFabricOperationalEvent** 应可看到来自群集的操作日志，如下所示。若要查看所有执行组件编程模型日志，请查询 **Type=ServiceFabricReliableActorEvent**。若要查看 Reliable Services 编程模型的所有日志，请键入 **Type=ServiceFabricReliableServiceEvent**。
![Operational Insights 日志查询和视图](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/view-logs-oi.png)

### 帮助排查问题的示例查询
下面是几个方案以及可用来进行故障排除的查询示例：

1. **了解 Service Fabric 是否针对特定的服务调用 RunAsync：**如果需要排除服务是否在启动时损坏，你可能需要这样做。为此，请使用类似于下面的查询进行搜索，但要将服务和应用程序名称替换为已部署的名称，然后看看是否返回了任何结果：

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateless1" AND "RunAsync has been invoked"
    ```

2. **如果运行有状态服务，且想要检查它是否引发 Service Fabric 已标记为失败的任何异常：**使用类似以下的查询来找出那些事件：

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateful1" AND TaskName=StatefulRunAsyncFailure
    ```

3. **若要查找所有已部署的应用程序和服务中由执行组件方法引发的任何异常的相应事件：**可以使用类似于下面的查询：

    ```
    Type=ServiceFabricReliableActorEvent AND TaskName=ActorMethodThrewException
    ```

## 更新诊断以从新的 EventSource 通道收集并上载日志
若要更新诊断从新的 EventSource 通道（表示将要部署的新应用程序）收集日志，只需要执行[上述部分](#deploywadarm)中相同的步骤即可，其中描述了现有群集的诊断设置。

在通过 Resource Manager 命令应用配置更新之前，需要更新 WadConfigUpdate.json 中的 EtwEventSourceProviderConfiguration 节，以添加新的 EventSources 条目。用于上载的表相同 (ETWEventTable)，因为这是为 Operational Insights 配置的、用来读取应用程序 ETW 事件的表。


## 后续步骤
查看针对 [Reliable Actors](/documentation/articles/service-fabric-reliable-actors-diagnostics) 和 [Reliable Services](/documentation/articles/service-fabric-reliable-services-diagnostics) 发出的诊断事件，以更详细地了解排查问题时应该调查哪些事件。

<!---HONumber=Mooncake_0314_2016-->