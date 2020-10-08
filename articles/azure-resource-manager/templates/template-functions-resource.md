---
title: 模板函数 - 资源
description: 介绍可在 Azure Resource Manager 模板中用于检索资源相关值的函数。
ms.topic: conceptual
ms.date: 09/03/2020
ms.openlocfilehash: dd040715cc8fb1339c6054c53007dbcd08e2cbdb
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91816799"
---
# <a name="resource-functions-for-arm-templates"></a>ARM 模板的资源函数

资源管理器提供了以下函数，用于获取 Azure 资源管理器 (ARM) 模板中的资源值：

* [extensionResourceId](#extensionresourceid)
* [list*](#list)
* [pickZones](#pickzones)
* [providers](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [subscription](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

若要从参数、变量或当前部署获取值，请参阅 [Deployment value functions](template-functions-deployment.md)（部署值函数）。

## <a name="extensionresourceid"></a>extensionResourceId

`extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)`

返回某个[扩展资源](../management/extension-resource-types.md)的资源 ID，该资源属于适用于其他资源的资源类型，是对其功能的补充。

### <a name="parameters"></a>参数

| 参数 | 必须 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| ResourceId |是 |字符串 |扩展资源应用到的资源的资源 ID。 |
| resourceType |是 |字符串 |资源类型，包括资源提供程序命名空间。 |
| resourceName1 |是 |字符串 |资源的名称。 |
| resourceName2 |否 |字符串 |下一个资源名称段（如果需要）。 |

如果资源类型包含更多段，则继续添加资源名称作为参数。

### <a name="return-value"></a>返回值

此函数返回的资源 ID 的基本格式为：

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

作用域段因扩展的资源而异。

当扩展资源应用到某个**资源**时，资源 ID 以下述格式返回：

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

当扩展资源应用到某个**资源组**时，格式为：

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

当扩展资源应用到某个**订阅**时，格式为：

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

当扩展资源应用到某个**管理组**时，格式为：

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>extensionResourceId 示例

以下示例返回资源组锁的资源 ID。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "lockName":{
            "type": "string"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "lockResourceId": {
            "type": "string",
            "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
        }
    }
}
```

部署到管理组的自定义策略定义是作为扩展资源实现的。 若要创建和分配策略，请将以下模板部署到管理组。

:::code language="json" source="~/quickstart-templates/managementgroup-deployments/mg-policy/azuredeploy.json":::

内置策略定义是租户级别的资源。 有关部署内置策略定义的示例，请参阅 [tenantResourceId](#tenantresourceid)。

<a id="listkeys"></a>
<a id="list"></a>

## <a name="list"></a>list*

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

此函数的语法因列表操作的名称而异。 每个实现都为支持列表操作的资源类型返回值。 操作名称必须以 `list` 开头。 一些常见用法是 `listKeys`、`listKeyValue` 和 `listSecrets`。

### <a name="parameters"></a>parameters

| 参数 | 必须 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| resourceName 或 resourceIdentifier |是 |字符串 |资源的唯一标识符。 |
| apiVersion |是 |字符串 |资源运行时状态的 API 版本。 通常采用 **yyyy-mm-dd**格式。 |
| functionValues |否 |object | 具有函数值的对象。 仅为支持接收具有参数值的对象的函数提供此对象，例如存储帐户上的 listAccountSas。 本文中演示了传递函数值的示例。 |

### <a name="valid-uses"></a>有效使用

列表函数可以在资源定义的属性中使用。 请勿使用在模板的 outputs 节中公开敏感信息的列表函数。 输出值存储在部署历史记录中，可能会被恶意用户检索到。

与[属性迭代](copy-properties.md)一起使用时，可以使用 `input` 的 list 函数，因为表达式已分配给资源属性。 不能将它们与 `count` 一起使用，因为必须在解析 list 函数之前确定计数。

### <a name="implementations"></a>实现形式

下表中显示 list* 的可能用途。

| 资源类型 | 函数名称 |
| ------------- | ------------- |
| 加载项/supportProviders | listsupportplaninfo |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.ApiManagement/service/authorizationServers | [listSecrets](/rest/api/apimanagement/2019-12-01/authorizationserver/listsecrets) |
| Microsoft.ApiManagement/service/gateways | [listKeys](/rest/api/apimanagement/2019-12-01/gateway/listkeys) |
| Microsoft.ApiManagement/service/identityProviders | [listSecrets](/rest/api/apimanagement/2019-12-01/identityprovider/listsecrets) |
| Microsoft.ApiManagement/service/namedValues | [listValue](/rest/api/apimanagement/2019-12-01/namedvalue/listvalue) |
| Microsoft.ApiManagement/service/openidConnectProviders | [listSecrets](/rest/api/apimanagement/2019-12-01/openidconnectprovider/listsecrets) |
| Microsoft.AppConfiguration/configurationStores | [ListKeys](/rest/api/appconfiguration/configurationstores/listkeys) |
| Microsoft.AppPlatform/Spring | [listTestKeys](/rest/api/azurespringclould/services/listtestkeys) |
| Microsoft.Automation/automationAccounts | [listKeys](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| BotService/botServices/通道 | [listChannelWithKeys](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/botservice/resource-manager/Microsoft.BotService/stable/2020-06-02/botservice.json#L553) |
| Microsoft.Cache/redis | [listKeys](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listKeys](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/agentpools | listQueueStatus |
| Microsoft.ContainerRegistry/registries/buildTasks | listSourceRepositoryProperties |
| Microsoft.ContainerRegistry/registries/buildTasks/steps | listBuildArguments |
| Microsoft.ContainerRegistry/registries/taskruns | listDetails |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registries/tasks | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterMonitoringUserCredential](/rest/api/aks/managedclusters/listclustermonitoringusercredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/accounts/shares | [listSynchronizations](/rest/api/datashare/shares/listsynchronizations) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSynchronizations](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listkeys](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [listkeys](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listKeys](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/databaseaccounts/listkeys) |
| Microsoft.DocumentDB/databaseAccounts/notebookWorkspaces | [listConnectionInfo](/rest/api/cosmos-db-resource-provider/2020-04-01/notebookworkspaces/listconnectioninfo) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domains | [listKeys](/rest/api/eventgrid/version2020-06-01/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [listKeys](/rest/api/eventgrid/version2020-06-01/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys](/rest/api/eventhub) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Kusto/Clusters/Databases | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
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
| Microsoft.Logic/workflows/runs/actions | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/repetitions | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft.Logic/workflows/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versions/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | [listKeys](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft.MachineLearningServices/workspaces/computes | [listNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/workspaces | [listKeys](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft.Maps/accounts | [listKeys](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [list](/rest/api/loganalytics/workspaces/list) |
| Microsoft.OperationalInsights/workspaces | listKeys |
| Microsoft.PolicyInsights/remediations | [listDeployments](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| RedHatOpenShift/openShiftClusters | [listCredentials](/rest/api/openshift/openshiftclusters/listcredentials) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys](/rest/api/servicebus/stable/namespaces%20-%20authorization%20rules/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys](/rest/api/servicebus/stable/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys](/rest/api/servicebus/stable/queues%20-%20authorization%20rules/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys](/rest/api/servicebus/stable/topics%20–%20authorization%20rules/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Synapse/工作区/integrationRuntimes | [listAuthKeys](/rest/api/synapse/integrationruntimeauthkeys/list) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| microsoft.web/sites/backups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/functions | [listkeys](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

若要确定哪些资源类型具有列表操作，请使用以下选项：

* 查看资源提供程序的 [REST API 操作](/rest/api/)，并查找列表操作。 例如，存储帐户具有 [listKeys 操作](/rest/api/storagerp/storageaccounts)。
* 使用 [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell cmdlet。 以下示例获取存储帐户的所有列表操作：

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* 使用以下 Azure CLI 命令，仅筛选列表操作：

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

其他列表函数具有不同的返回格式。 若要查看函数的格式，请将其包含在 outputs 节中，如示例模板所示。

### <a name="remarks"></a>备注

使用资源名称或 [resourceId 函数](#resourceid)来指定资源。 在部署被引用资源的同一模板中使用列表函数时，请使用资源名称。

如果在有条件部署的资源中使用 **list** 函数，则会对该函数进行评估，即使资源尚未部署。 如果 list 函数引用的资源不存在，系统会显示错误。 使用 **if** 函数确保仅在部署资源时才评估函数。 请参阅 [if 函数](template-functions-logical.md#if)以获取使用 if 和 list 以及有条件部署的资源的示例模板。

### <a name="list-example"></a>List 示例

以下示例在为[部署脚本](deployment-script-template.md)设置值时使用了 listKeys。

```json
"storageAccountSettings": {
    "storageAccountName": "[variables('storageAccountName')]",
    "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
}
```

下一个示例演示采用参数的列表函数。 在本例中，函数为 listAccountSas。 请为到期时间传递一个对象。 到期时间必须是将来的时间。

```json
"parameters": {
    "accountSasProperties": {
        "type": "object",
        "defaultValue": {
            "signedServices": "b",
            "signedPermission": "r",
            "signedExpiry": "2020-08-20T11:00:00Z",
            "signedResourceTypes": "s"
        }
    }
},
...
"sasToken": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties')).accountSasToken]"
```

## <a name="pickzones"></a>pickZones

`pickZones(providerNamespace, resourceType, location, [numberOfZones], [offset])`

确定资源类型是否支持区域的区域。

### <a name="parameters"></a>参数

| 参数 | 必须 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| providerNamespace | 是 | 字符串 | 用于检查区域支持的资源类型的资源提供程序命名空间。 |
| resourceType | 是 | 字符串 | 要检查区域支持的资源类型。 |
| location | 是 | 字符串 | 要检查区域支持的区域。 |
| numberOfZones | 否 | integer | 要返回的逻辑区域数。 默认值为 1。 该数字必须是介于1和3之间的正整数。  对于单分区资源，请使用1。 对于多分区资源，该值必须小于或等于受支持区域的数目。 |
| offset | 否 | integer | 起始逻辑区域的偏移量。 如果 offset 加 numberOfZones 超过支持的区域数量，则函数将返回错误。 |

### <a name="return-value"></a>返回值

具有受支持区域的数组。 当使用 offset 和 numberOfZones 的默认值时，支持区域的资源类型和区域返回以下数组：

```json
[
    "1"
]
```

当 `numberOfZones` 参数设置为3时，它将返回：

```json
[
    "1",
    "2",
    "3"
]
```

如果资源类型或区域不支持区域，则返回空数组。

```json
[
]
```

### <a name="pickzones-example"></a>pickZones 示例

以下模板显示了使用 pickZones 函数的三个结果。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "supported": {
            "type": "array",
            "value": "[pickZones('Microsoft.Compute', 'virtualMachines', 'westus2')]"
        },
        "notSupportedRegion": {
            "type": "array",
            "value": "[pickZones('Microsoft.Compute', 'virtualMachines', 'northcentralus')]"
        },
        "notSupportedType": {
            "type": "array",
            "value": "[pickZones('Microsoft.Cdn', 'profiles', 'westus2')]"
        }
    }
}
```

前面示例的输出返回三个数组。

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| 受支持 | 数组 | ["1"] |
| notSupportedRegion | 数组 | [] |
| notSupportedType | 数组 | [] |

你可以使用 pickZones 的响应来确定是否为区域提供 null，或将虚拟机分配给不同的区域。 下面的示例基于区域的可用性为区域设置一个值。

```json
"zones": {
    "value": "[if(not(empty(pickZones('Microsoft.Compute', 'virtualMachines', 'westus2'))), string(add(mod(copyIndex(),3),1)), json('null'))]"
},
```

## <a name="providers"></a>providers

`providers(providerNamespace, [resourceType])`

返回有关资源提供程序及其支持的资源类型的信息。 如果未提供资源类型，则该函数将返回资源提供程序支持的所有类型。

### <a name="parameters"></a>参数

| 参数 | 必须 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| providerNamespace |是 |字符串 |提供程序的命名空间 |
| resourceType |否 |字符串 |指定的命名空间中的资源类型。 |

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

### <a name="providers-example"></a>Provider 示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json)演示如何使用 provider 函数：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

对于 Microsoft.Web 资源提供程序和站点资源类型，上面的示例返回以下格式的对象 ：

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

### <a name="parameters"></a>参数

| 参数 | 必须 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| resourceName 或 resourceIdentifier |是 |字符串 |资源的名称或唯一标识符。 当引用当前模板中的资源时，请仅提供资源名称作为参数。 当引用以前部署的资源或者资源名称不明确时，请提供资源 ID。 |
| apiVersion |否 |字符串 |指定的资源的 API 版本。 如果资源不是在同一模板中预配的，则需要此参数。 通常情况下，格式为 **yyyy-mm-dd**。 如需查看适用于你的资源的有效 API 版本，请参阅[模板参考](/azure/templates/)。 |
| 'Full' |否 |字符串 |一个值，指定是否要返回完整资源对象。 如果未指定 `'Full'`，仅返回资源的属性对象。 完整对象包括资源 ID 和位置等值。 |

### <a name="return-value"></a>返回值

每种资源类型返回 reference 函数的不同属性。 该函数不返回单个预定义的格式。 另外，返回的值因 `'Full'` 参数的值而异。 若要查看资源类型的属性，请返回 outputs 节中的对象，如示例所示。

### <a name="remarks"></a>备注

reference 函数检索以前部署的资源或在当前模板中部署的资源的运行时状态。 本文展示了这两种方案的示例。

通常情况下，可以使用 reference 函数返回对象的特定值，例如 blob 终结点 URI 或完全限定的域名。

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

需要不属于属性架构的资源值时，请使用 `'Full'`。 例如，若要设置密钥保管库访问策略，请获取虚拟机的标识属性。

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
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

reference 函数只能用在资源定义的 properties 中以及模板或部署的 outputs 节中。 与[属性迭代](copy-properties.md)一起使用时，可以将 reference 函数用于 `input`，因为该表达式是分配给资源属性的。

不能使用引用函数在复制循环中设置 `count` 属性的值。 可用于在循环中设置其他属性。 count 属性的引用会被阻止，因为必须在解析引用函数之前确定该属性。

若要在嵌套模板的输出部分中使用 reference 函数或任何 list* 函数，必须将 ```expressionEvaluationOptions``` 设置为使用[内层作用域](linked-templates.md#expression-evaluation-scope-in-nested-templates)计算或使用链接的而不是嵌套的模板。

如果在有条件部署的资源中使用 **reference** 函数，则会对该函数进行评估，即使资源尚未部署。  如果 reference 函数引用的资源不存在，系统会显示错误。 使用 **if** 函数确保仅在部署资源时才评估函数。 请参阅 [if 函数](template-functions-logical.md#if)以获取使用 if 和 reference 以及有条件部署的资源的示例模板。

### <a name="implicit-dependency"></a>隐式依赖项

如果在同一模板内预配了被引用资源且通过其名称（而非资源 ID）引用该资源，则使用 reference 函数会隐式声明一个资源依赖于另一个资源。 也不需要同时使用 dependsOn 属性。 只有当引用的资源已完成部署后，才会对函数求值。

### <a name="resource-name-or-identifier"></a>资源名称或标识符

若要引用在同一模板中部署的资源，请提供资源的名称。

```json
"value": "[reference(parameters('storageAccountName'))]"
```

引用没有部署在同一模板中的资源时，请提供资源 ID 和 `apiVersion`。

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

若要避免所引用的资源不明确，可以提供完全限定的资源标识符。

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

向资源构造完全限定的引用时，类型和名称的分段组合顺序并不是这两者的简单串联。 而是，在命名空间后面，使用*类型/名称*对的序列（从最不具体到最具体）：

**{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]**

例如：

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` 正确，`Microsoft.Compute/virtualMachines/extensions/myVM/myExt` 不正确

若要简化任何资源 ID 的创建，请使用本文档中所述的 `resourceId()` 函数，而不是 `concat()` 函数。

### <a name="get-managed-identity"></a>获取托管标识

[Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)是为某些资源隐式创建的[扩展资源类型](../management/extension-resource-types.md)。 由于模板中未显式定义托管标识，因此必须引用该标识所应用到的资源。 使用 `Full` 获取所有属性，包括隐式创建的标识。

模式为：

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>, <API-version>, 'Full').Identity.propertyName]"`

例如，若要获取应用于虚拟机的托管标识的主体 ID，请使用：

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

或者，若要获取应用于虚拟机规模集的托管标识的租户 ID，请使用：

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

### <a name="reference-example"></a>Reference 示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json)部署一个资源并引用该资源。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

仅对包含由其他服务托管的资源的资源组返回 managedBy 属性。 对于托管应用程序、Databricks 和 AKS，该属性的值为管理资源的资源 ID。

### <a name="remarks"></a>备注

`resourceGroup()` 函数不能用于[在订阅级别部署的](deploy-to-subscription.md)模板中。 它只能用于部署到资源组的模板中。 可以在以资源组为目标的[链接模板或嵌套模板（具有内部范围）](linked-templates.md)中使用 `resourceGroup()` 函数，即使父模板部署到订阅，也是如此。 在这种情况下，链接模板或嵌套模板将在资源组级别进行部署。 若要详细了解如何在订阅级别部署中将资源组作为目标，请参阅[将 Azure 资源部署到多个订阅或资源组](cross-scope-deployment.md)。

resourceGroup 函数的一个常见用途是在与资源组相同的位置中创建资源。 以下示例使用资源组位置作为默认参数值。

```json
"parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
}
```

还可以使用 resourceGroup 函数将资源组中的标记应用于资源。 有关详细信息，请参阅[应用资源组中的标记](../management/tag-resources.md#apply-tags-from-resource-group)。

使用嵌套模板部署到多个资源组时，可以指定评估 resourceGroup 函数的范围。 有关详细信息，[将 Azure 资源部署到多个订阅或资源组](cross-scope-deployment.md)。

### <a name="resource-group-example"></a>资源组示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json)返回资源组的属性。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

上述示例返回采用以下格式的对象：

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>ResourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)`

返回资源的唯一标识符。 如果资源名称不确定或未设置在相同的模板内，请使用此函数。 返回的标识符的格式因部署是在资源组、订阅、管理组还是租户的范围内进行而不同。

### <a name="parameters"></a>参数

| 参数 | 必须 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| subscriptionId |否 |字符串（GUID 格式） |默认值为当前订阅。 如果需要检索另一个订阅中的资源，请指定此值。 仅在资源组或订阅的范围内部署时才提供此值。 |
| resourceGroupName |否 |字符串 |默认值为当前资源组。 如果需要检索另一个资源组中的资源，请指定此值。 仅在资源组的范围内部署时才提供此值。 |
| resourceType |是 |字符串 |资源类型，包括资源提供程序命名空间。 |
| resourceName1 |是 |字符串 |资源的名称。 |
| resourceName2 |否 |字符串 |下一个资源名称段（如果需要）。 |

如果资源类型包含更多段，则继续添加资源名称作为参数。

### <a name="return-value"></a>返回值

在资源组的范围内部署模板时，将以以下格式返回资源 ID：

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

可以对其他部署范围使用 resourceId 函数，但 ID 的格式会发生更改。

如果在部署到订阅时使用 resourceId，则会按以下格式返回资源 ID：

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

如果在部署到管理组或租户时使用 resourceId，则会按以下格式返回资源 ID：

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

为避免混淆，建议你在使用部署到订阅、管理组或租户的资源时不使用 resourceId， 而改用针对范围设计的 ID 函数。

对于[订阅级别的资源](deploy-to-subscription.md)，请使用 [subscriptionResourceId](#subscriptionresourceid) 函数。

对于[管理组级别的资源](deploy-to-management-group.md)，请使用 [extensionResourceId](#extensionresourceid) 函数来引用作为管理组的扩展实现的资源。 例如，部署到管理组的自定义策略定义是管理组的扩展。 请使用 [tenantResourceId](#tenantresourceid) 函数来引用已部署到租户但在你的管理组中可用的资源。 例如，内置策略定义是作为租户级别的资源实现的。

对于[租户级别的资源](deploy-to-tenant.md)，请使用 [tenantResourceId](#tenantresourceid) 函数。 请对内置策略定义使用 tenantResourceId，因为内置策略定义是在租户级别实现的。

### <a name="remarks"></a>备注

提供的参数数目各不相同，具体取决于资源是父资源还是子资源，以及资源是否位于同一订阅或资源组中。

若要获取同一订阅和资源组中父资源的资源 ID，请提供资源的类型和名称。

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

若要获取子资源的资源 ID，请注意资源类型中段的数目。 请提供资源类型的每个段的资源名称。 段的名称对应于针对层次结构的该部分存在的资源。

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

对于属于同一订阅但属于不同资源组的资源，若要获取其资源 ID，请提供资源组名称。

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

若要获取位于不同订阅和资源组中的资源的资源 ID，请提供订阅 ID 和资源组名称。

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

通常，在替代资源组中使用存储帐户或虚拟网络时，需要使用此函数。 以下示例演示了如何轻松使用外部资源组中的资源：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

### <a name="resource-id-example"></a>资源 ID 示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json)返回资源组中存储帐户的资源 ID：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

上述示例中使用默认值的输出为：

| 名称 | 类型 | 值 |
| ---- | ---- | ----- |
| sameRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | String | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | String | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | String | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>订阅

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

### <a name="remarks"></a>备注

使用嵌套模板部署到多个订阅时，可以指定评估 subscription 函数的范围。 有关详细信息，[将 Azure 资源部署到多个订阅或资源组](cross-scope-deployment.md)。

### <a name="subscription-example"></a>订阅示例

以下[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json)显示了在 outputs 节中调用的 subscription 函数。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

## <a name="subscriptionresourceid"></a>subscriptionResourceId

`subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)`

返回在订阅级别部署的资源的唯一标识符。

### <a name="parameters"></a>参数

| 参数 | 必须 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| subscriptionId |否 |字符串（GUID 格式） |默认值为当前订阅。 如果需要检索另一个订阅中的资源，请指定此值。 |
| resourceType |是 |字符串 |资源类型，包括资源提供程序命名空间。 |
| resourceName1 |是 |字符串 |资源的名称。 |
| resourceName2 |否 |字符串 |下一个资源名称段（如果需要）。 |

如果资源类型包含更多段，则继续添加资源名称作为参数。

### <a name="return-value"></a>返回值

使用以下格式返回标识符：

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>备注

我们使用此函数获取[部署到订阅](deploy-to-subscription.md)而不是资源组的资源的资源 ID。 返回的 ID 不同于 [resourceId](#resourceid) 函数返回的值，区别在于不包含资源组值。

### <a name="subscriptionresourceid-example"></a>subscriptionResourceID 示例

以下模板分配内置角色。 可以将它部署到资源组或订阅。 它使用 subscriptionResourceId 函数获取内置角色的资源 ID。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="tenantresourceid"></a>tenantResourceId

`tenantResourceId(resourceType, resourceName1, [resourceName2], ...)`

返回在租户级别部署的资源的唯一标识符。

### <a name="parameters"></a>参数

| 参数 | 必须 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| resourceType |是 |字符串 |资源类型，包括资源提供程序命名空间。 |
| resourceName1 |是 |字符串 |资源的名称。 |
| resourceName2 |否 |字符串 |下一个资源名称段（如果需要）。 |

如果资源类型包含更多段，则继续添加资源名称作为参数。

### <a name="return-value"></a>返回值

使用以下格式返回标识符：

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>备注

我们使用此函数获取部署到租户的资源的资源 ID。 返回的 ID 不同于其他资源 ID 函数返回的值，区别在于不包含资源组值或订阅值。

### <a name="tenantresourceid-example"></a>tenantResourceId 示例

内置策略定义是租户级别的资源。 若要部署引用内置策略定义的策略分配，请使用 tenantResourceId 函数。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyAssignmentName": {
      "type": "string",
      "defaultValue": "[guid(parameters('policyDefinitionID'), resourceGroup().name)]",
      "metadata": {
        "description": "Specifies the name of the policy assignment, can be used defined or an idempotent name as the defaultValue provides."
      }
    },
    "policyDefinitionID": {
      "type": "string",
      "defaultValue": "0a914e76-4921-4c19-b460-a2d36003525a",
      "metadata": {
        "description": "Specifies the ID of the policy definition or policy set definition being assigned."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "name": "[parameters('policyAssignmentName')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "scope": "[subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)]",
        "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
      }
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

* 有关 Azure 资源管理器模板中各部分的说明，请参阅[创作 Azure 资源管理器模板](template-syntax.md)。
* 若要合并多个模板，请参阅[将链接的模板与 Azure 资源管理器配合使用](linked-templates.md)。
* 若要在创建资源类型时迭代指定的次数，请参阅[在 Azure 资源管理器中创建多个资源实例](copy-resources.md)。
* 要查看如何部署已创建的模板，请参阅[使用 Azure 资源管理器模板部署应用程序](deploy-powershell.md)。

