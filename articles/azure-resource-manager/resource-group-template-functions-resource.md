---
title: Azure 资源管理器模板函数 - 资源 | Microsoft Docs
description: 介绍可在 Azure 资源管理器模板中使用的用于检索资源相关值的函数。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/02/2019
ms.author: tomfitz
ms.openlocfilehash: a4a579d8540645a736b0922b973d5b9da2773c23
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774882"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>用于 Azure 资源管理器模板的资源函数

Resource Manager 提供以下用于获取资源值的函数：

* [list*](#list)
* [providers](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [subscription](#subscription)

若要从参数、变量或当前部署获取值，请参阅 [Deployment value functions](resource-group-template-functions-deployment.md)（部署值函数）。

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>list*

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

此函数的语法因列表操作的名称而异。 每个实现都为支持列表操作的资源类型返回值。 操作名称必须以 `list` 开头。 一些常见用法是 `listKeys` 和 `listSecrets`。 

### <a name="parameters"></a>Parameters

| 参数 | 必填 | type | 描述 |
|:--- |:--- |:--- |:--- |
| resourceName 或 resourceIdentifier |是 |string |资源的唯一标识符。 |
| apiVersion |是 |string |资源运行时状态的 API 版本。 通常情况下，格式为 **yyyy-mm-dd**。 |
| functionValues |否 |object | 具有函数值的对象。 仅为支持接收具有参数值的对象的函数提供此对象，例如存储帐户上的 listAccountSas。 本文中演示了传递函数值的示例。 | 

### <a name="implementations"></a>实现形式

下表中显示 list* 的可能用途。

| 资源类型 | 函数名 |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.AppConfiguration/configurationStores | ListKeys |
| Microsoft.Automation/automationAccounts | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.BotService/botServices/channels | listChannelWithKeys |
| Microsoft.Cache/Redis | [listKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listPolicies](/rest/api/containerregistry/registries/listpolicies) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registries/tasks | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listKeys](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domains | [listKeys](/rest/api/eventgrid/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [listKeys](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.LabServices/users | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/users | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versions/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | listKeys |
| Microsoft.MachineLearningServices/workspaces | listKeys |
| Microsoft.Maps/accounts | [listKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [listKeys](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/webapps/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

若要确定哪些资源类型具有列表操作，请使用以下选项：

* 查看资源提供程序的 [REST API 操作](/rest/api/)，并查找列表操作。 例如，存储帐户具有 [listKeys 操作](/rest/api/storagerp/storageaccounts)。
* 使用 [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell cmdlet。 以下示例获取存储帐户的所有列表操作：

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* 使用以下 Azure CLI 命令以仅筛选列表操作：

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>返回值

返回的对象因使用的列表函数而异。 例如，用于存储帐户的 listKeys 返回以下格式：

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

其他列表函数具有不同的返回格式。 要查看函数的格式，请将其包含在 outputs 节中，如示例模板所示。

### <a name="remarks"></a>备注

使用资源名称或 [resourceId 函数](#resourceid)来指定资源。 在部署被引用资源的同一模板中使用列表函数时，请使用资源名称。

如果在有条件部署的资源中使用 **list** 函数，则会对该函数进行评估，即使资源尚未部署。 如果 **list** 函数引用某个不存在的资源，则会出现错误。 使用 **if** 函数确保仅在部署资源时才评估函数。 请查看示例模板的 [if 函数](resource-group-template-functions-logical.md#if)，该模板将 if 和 list 用于进行条件部署的资源。

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json)演示如何从 outputs 节中的存储帐户返回主密钥和辅助密钥。 它还为存储帐户返回 SAS 令牌。 

若要获取 SAS 令牌，请针对到期时间传递对象。 到期时间必须是将来的时间。 此示例旨在演示如何使用列表函数。 通常情况下，在资源值中使用 SAS 令牌，而不是将其作为输出值返回。 输出值存储在部署历史记录中并不安全。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
```

## <a name="providers"></a>providers

`providers(providerNamespace, [resourceType])`

返回有关资源提供程序及其支持的资源类型的信息。 如果未提供资源类型，则该函数将返回资源提供程序支持的所有类型。

### <a name="parameters"></a>Parameters

| 参数 | 必填 | type | 描述 |
|:--- |:--- |:--- |:--- |
| providerNamespace |是 |string |提供程序的命名空间 |
| resourceType |否 |string |指定的命名空间中的资源类型。 |

### <a name="return-value"></a>返回值

将使用以下格式返回支持的每个类型： 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

不保证返回值的数组排序。

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json)演示如何使用 provider 函数：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

对于 Microsoft.Web 资源提供程序和站点资源类型，上面的示例返回以下格式的对象：

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>reference

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

返回表示资源的运行时状态的对象。

### <a name="parameters"></a>Parameters

| 参数 | 必填 | 类型 | 描述 |
|:--- |:--- |:--- |:--- |
| resourceName 或 resourceIdentifier |是 |string |资源的名称或唯一标识符。 当引用当前模板中的资源时，请仅提供资源名称作为参数。 引用以前部署的资源时, 请提供资源 ID。 |
| apiVersion |否 |string |指定的资源的 API 版本。 如果资源不是在同一模板中预配的，请包含此参数。 通常情况下，格式为 **yyyy-mm-dd**。 有关资源的有效 API 版本, 请参阅[模板参考](/azure/templates/)。 |
| 'Full' |否 |string |一个值，指定是否要返回完整资源对象。 如果未指定 `'Full'`，仅返回资源的属性对象。 完整对象包括资源 ID 和位置等值。 |

### <a name="return-value"></a>返回值

每种资源类型返回 reference 函数的不同属性。 该函数不返回单个预定义的格式。 同样，返回的值因是否指定了完整对象而有所不同。 若要查看资源类型的属性，请返回 outputs 节中的对象，如示例所示。

### <a name="remarks"></a>备注

reference 函数检索以前部署的资源或在当前模板中部署的资源的运行时状态。 本文展示了这两种方案的示例。

通常情况下，可使用 reference 函数返回对象的特定值，例如 Blob 终结点 URI 或完全限定的域名。

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

需要不属于属性架构的资源值时，请使用 `'Full'`。 例如，若要设置密钥保管库访问策略，请获取虚拟机的标识属性。

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
        "objectId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

### <a name="valid-uses"></a>有效使用

reference 函数只能用在资源定义的 properties 中以及模板或部署的 outputs 节中。 与[属性迭代](resource-group-create-multiple.md#property-iteration)一起使用时，可以使用 `input` 的 reference 函数，因为表达式已分配给资源属性。 不能将其与 `count` 一起使用，因为必须在解析 reference 函数之前确定计数。

不能在[嵌套模板](resource-group-linked-templates.md#nested-template)的输出中使用 reference 函数来返回已在嵌套模板中部署的资源。 而是使用[链接的模板](resource-group-linked-templates.md#external-template-and-external-parameters)。

如果在有条件部署的资源中使用 **reference** 函数，则会对该函数进行评估，即使资源尚未部署。  如果 **reference** 函数引用某个不存在的资源，则会出现错误。 使用 **if** 函数确保仅在部署资源时才评估函数。 请查看示例模板的 [if 函数](resource-group-template-functions-logical.md#if)，该模板将 if 和 reference 用于进行条件部署的资源。

### <a name="implicit-dependency"></a>隐式依赖关系

如果在同一模板内预配了被引用资源且通过其名称（而非资源 ID）引用该资源，则使用 reference 函数会隐式声明一个资源依赖于另一个资源。 也不需要同时使用 dependsOn 属性。 只有当引用的资源已完成部署后，才会对函数求值。

### <a name="resource-name-or-identifier"></a>资源名称或标识符

引用同一模板中部署的资源时, 请提供该资源的名称。

```json
"value": "[reference(parameters('storageAccountName'))]"
```

引用未部署在同一模板中的资源时, 请提供资源 ID。

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

若要避免对所引用的资源有歧义, 可以提供完全限定的资源名称。

```json
"value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')))]"
```

向资源构造完全限定的引用时，类型和名称的分段组合顺序并不是这两者的简单串联。 相反，在命名空间后，需采用“类型/名称”对从最不具体到最具体的序列：

**{resource provider-namespace}/{parent-resource-type}/{parent-resource-name} [/{child-resource-type}/{child-resource-name}]**

例如：

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` 正确，`Microsoft.Compute/virtualMachines/extensions/myVM/myExt` 不正确

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json)部署一个资源并引用该资源。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
``` 

上面的示例返回两个对象。 属性对象采用以下格式：

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

完整对象采用以下格式：

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json)引用的存储帐户未在此模板中部署。 同一订阅内已存在该存储帐户。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageResourceGroup": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

返回表示当前资源组的对象。 

### <a name="return-value"></a>返回值

返回的对象采用以下格式：

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

### <a name="remarks"></a>备注

`resourceGroup()` 函数不能用于[在订阅级别部署的](deploy-to-subscription.md)模板中。 它只能用于部署到资源组的模板中。

resourceGroup 函数的一个常见用途是在与资源组相同的位置中创建资源。 以下示例使用资源组位置来分配网站的位置。

```json
"resources": [
   {
      "apiVersion": "2016-08-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

还可以使用 resourceGroup 函数将资源组中的标记应用于资源。 有关详细信息, 请参阅[从资源组应用标记](resource-group-using-tags.md#apply-tags-from-resource-group)。

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json)返回资源组的属性。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

上面的示例返回以下格式的对象：

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

返回资源的唯一标识符。 如果资源名称不确定或未设置在相同的模板内，请使用此函数。 

### <a name="parameters"></a>Parameters

| 参数 | 必填 | type | 描述 |
|:--- |:--- |:--- |:--- |
| subscriptionId |否 |字符串（GUID 格式） |默认值为当前订阅。 如果需要检索另一个订阅中的资源，请指定此值。 |
| resourceGroupName |否 |string |默认值为当前资源组。 如果需要检索另一个资源组中的资源，请指定此值。 |
| resourceType |是 |string |资源类型，包括资源提供程序命名空间。 |
| resourceName1 |是 |string |资源的名称。 |
| resourceName2 |否 |string |下一个资源名称段（如果资源是嵌套的）。 |

### <a name="return-value"></a>返回值

将使用以下格式返回标识符：

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>备注

与[订阅级部署](deploy-to-subscription.md)一起使用时，`resourceId()` 函数只能检索在该级别部署的资源的 ID。 例如，你可以获取策略定义或角色定义的 ID，但不能获取存储帐户的 ID。 对于到资源组的部署，反过来也成立。 你无法获取在订阅级别部署的资源的资源 ID。

指定的参数值取决于资源是否与当前部署位于同一订阅和资源组。 若要在同一订阅和资源组中获取存储帐户的资源 ID，请使用：

```json
"[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]"
```

若要在相同订阅但不同的资源组中获取存储帐户的资源 ID，请使用：

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

若要在不同的订阅和资源组中获取存储帐户的资源 ID，请使用：

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

若要在不同的资源组中获取数据库的资源 ID，请使用：

```json
"[resourceId('otherResourceGroup', 'Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]"
```

在订阅范围内部署时，若要获取订阅级资源的资源 ID，请使用：

```json
"[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
```

通常，在替代资源组中使用存储帐户或虚拟网络时，需要使用此函数。 以下示例演示了如何轻松使用外部资源组中的资源：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json)返回资源组中存储帐户的资源 ID：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

上面具有默认值的示例的输出为：

| 姓名 | 类型 | ReplTest1 |
| ---- | ---- | ----- |
| sameRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | String | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>subscription

`subscription()`

返回有关当前部署的订阅的详细信息。 

### <a name="return-value"></a>返回值

该函数返回以下格式：

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json)显示了在 outputs 节中调用的 subscription 函数。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

## <a name="next-steps"></a>后续步骤

* 有关 Azure 资源管理器模板中各部分的说明，请参阅[创作 Azure 资源管理器模板](resource-group-authoring-templates.md)。
* 要合并多个模板，请参阅[将链接的模板与 Azure 资源管理器配合使用](resource-group-linked-templates.md)。
* 若要在创建资源类型时迭代指定的次数，请参阅[在 Azure 资源管理器中创建多个资源实例](resource-group-create-multiple.md)。
* 要查看如何部署已创建的模板，请参阅[使用 Azure 资源管理器模板部署应用程序](resource-group-template-deploy.md)。

