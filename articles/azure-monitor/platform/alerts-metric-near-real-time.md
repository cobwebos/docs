---
title: Azure Monitor 中的指标警报支持的资源
description: Azure Monitor 中的指标警报的支持指标和日志的参考
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 3/5/2020
ms.subservice: alerts
ms.openlocfilehash: 62c1e29b668c3ef58b0e26e2749c55b913279425
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545677"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Azure Monitor 中的指标警报支持的资源

Azure Monitor 现在支持[新型指标警报类型](../../azure-monitor/platform/alerts-overview.md)，它比旧式[经典指标警报](../../azure-monitor/platform/alerts-classic.overview.md)具有显著的优势。 指标可用于 [Azure 服务的大型列表](../../azure-monitor/platform/metrics-supported.md)。 新型警报支持资源类型的一个（不断增长的）子集。 本文列出了该子集。

还可以对提取为指标的常用日志数据（在 Log Analytics 工作区中存储）使用新型指标警报。 有关详细信息，请查看[日志的指标警报](../../azure-monitor/platform/alerts-metric-logs.md)。

## <a name="portal-powershell-cli-rest-support"></a>门户、PowerShell、CLI、REST 支持
目前，仅可在 Azure 门户、[REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/) 或[资源管理器模板](../../azure-monitor/platform/alerts-metric-create-templates.md)中创建新型指标警报。 对于使用 PowerShell 和 Azure CLI 2.0 及更高版本配置新型警报的支持即将推出。

## <a name="metrics-and-dimensions-supported"></a>指标和维度支持
新型指标警报支持针对使用维度的指标发出警报。 可以使用维度将指标筛选到适当级别。 所有受支持的指标以及适用的维度都可以从 [Azure Monitor - 指标资源管理器](../../azure-monitor/platform/metrics-charts.md)中进行浏览和可视化。

下面是新型警报支持的 Azure Monitor 指标源的完整列表：

|资源类型  |支持维度 |多资源警报| 可用指标|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | 是| 否 | [API 管理](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.AppPlatform/Spring |否| 是|
|Microsoft.Automation/automationAccounts | 是| 否 | [Automation 帐户](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | 不可用| 否 | [批处理帐户](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|是| 否 |[用于 Redis 的 Azure 缓存](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|微软.经典存储/存储帐户/mmx 经典|否|是|
|微软.经典存储/存储帐户/mmx经典/blob服务|否|是|
|微软.经典存储/存储帐户/mmx经典/文件服务|否|是|
|微软.经典存储/存储帐户/mmx经典/队列服务|否|是|
|微软.经典存储/存储帐户/mmx经典/表服务|否|是| |
|Microsoft.CognitiveServices/accounts| 不可用 | 否 | [认知服务](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |是 | 是 | [虚拟机](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |不可用 | 是 |[虚拟机规模集](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | 是| 否 | [容器组](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | 是 | 否 | [托管群集](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | 是 | 是 | |
|Microsoft.DataFactory/datafactories| 是| 否 | [数据工厂 V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories |是 | 否 |[数据工厂 V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DataShare/accounts |否| 是|
|Microsoft.DBforMySQL/servers |不可用| 否 |[适用于 MySQL 的 DB](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers |不可用 | 否 | [适用于 PostgreSQL 的 DB](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | 不可用 | 否 |[IoT 中心指标](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices| 是 | 否 |[DPS 指标](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft.EventGrid/domains|否|是| |
|Microsoft.EventGrid/topics |是 | 否 |[事件网格主题](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/clusters |是| 否 |[事件中心群集](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/namespaces |是| 否 |[事件中心](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| 否 |否 |[金库](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows |不可用 | 否 |[逻辑应用](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces|是| 否 | [机器学习](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.NetApp/netAppAccounts/capacityPools |是| 否 | [Azure NetApp 容量池](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes |是| 否 | [Azure NetApp 卷](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways|不可用| 否 |  |
|Microsoft.Network/dnsZones | 不可用| 否 | [DNS 区域](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | 不可用 | 否 |[Express Route 线路](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers（仅限标准 SKU）| 是| 否 | [负载均衡器](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|微软.网络/natGateways|否|是|
|微软.网络/私人终端|否|是|
|Microsoft.Network/privateLinkServices|否|是|
|Microsoft.Network/publicipaddresses |不可用 | 否 |[公共 IP 地址](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | 是 | 否 | [流量管理器配置文件](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| 是 | 否 | [Log Analytics 工作区](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Relay/namespaces | 是 | 否 | [中继](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Microsoft.Peering/peeringServices|否|是|
|Microsoft.PowerBIDedicated/capacities | 不可用 | 否 | [能力](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |不可用|否 | [搜索服务](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces |是| 否 |[服务总线](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servers/elasticPools |    否 | 是 |
|Microsoft.Sql/servers/databases    | 否 | 是 |
|Microsoft.Storage/storageAccounts |是 | 否 | [存储帐户](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | 是| 否 | [Blob 服务](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices)、[文件服务](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices)、[队列服务](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices)和[表服务](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |不可用| 否 | [流分析](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|微软.微软.VMWareCloud简单/虚拟机 |是|否 |[CloudSimple 虚拟机](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/hostingEnvironments/multiRolePools | 是 | 否 | [应用服务环境多角色池](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | 是 | 否 | [应用服务环境辅助池](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | 是 | 否 | [应用服务计划](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | 是 | 否 | [应用程序服务](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites)|
|Microsoft.Web/sites/slots | 是 | 否 | [应用服务槽](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>负载架构

> [!NOTE]
> 还可以使用[常见警报架构](https://aka.ms/commonAlertSchemaDocs)，它的优点是可以跨 Azure Monitor 中的所有警报服务提供单个可扩展且统一的警报有效负载，用于 Webhook 集成。 [了解常见的警报架构定义。](https://aka.ms/commonAlertSchemaDefinitions)


使用适当配置的[操作组](../../azure-monitor/platform/action-groups.md)时，POST 操作对于所有准新型指标警报包含以下 JSON 有效负载和架构：

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
            "dimensions": [
              {
                "name": "AccountResourceId",
                "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
              },
              {
                "name": "GeoType",
                "value": "Primary"
              }
            ],
            "operator": "GreaterThan",
            "threshold": "0",
            "timeAggregation": "PT5M",
            "metricValue": 1
          }
        ]
      },
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroupName": "Contoso",
      "resourceName": "diag500",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
      "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
    },
    "properties": {
      "key1": "value1",
      "key2": "value2"
    }
  }
}
```

## <a name="next-steps"></a>后续步骤

* 详细了解新式[警报体验](../../azure-monitor/platform/alerts-overview.md)。
* 了解 [Azure 中的日志警报](../../azure-monitor/platform/alerts-unified-log.md)。
* 了解 [Azure 中的警报](../../azure-monitor/platform/alerts-overview.md)。
