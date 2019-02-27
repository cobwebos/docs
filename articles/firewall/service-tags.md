---
title: Azure 防火墙服务标记概述
description: 本文是 Azure 防火墙服务标记的概述。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 2/21/2019
ms.author: victorh
ms.openlocfilehash: 9ecc5d3779ac6632f4a5c05914cbb0e756c79e91
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56458214"
---
# <a name="azure-firewall-service-tags"></a>Azure 防火墙服务标记

服务标记表示一组 IP 地址前缀，帮助最大程度地降低安全规则创建过程的复杂性。 无法创建自己的服务标记，也无法指定要将哪些 IP 地址包含在标记中。 Microsoft 会管理服务标记包含的地址前缀，并会在地址发生更改时自动更新服务标记。

Azure 防火墙服务标记可用于网络规则目标字段。 它们可用于代替特定的 IP 地址。

> [!NOTE]
> 服务标记正逐步添加到部分区域中，并且不久以后将在所有区域中都可使用。

## <a name="supported-service-tags"></a>支持的服务标记

以下服务标记可用于 Azure 防火墙网络规则中：

* **AzureCloud**（仅限资源管理器）：此标记表示 Azure 的 IP 地址空间，包括所有[数据中心公共 IP 地址](https://www.microsoft.com/download/details.aspx?id=41653)。 如果指定 AzureCloud 作为值，则会允许或拒绝发往 Azure 公共 IP 地址的流量。 如果仅希望允许访问某个特定[区域](https://azure.microsoft.com/regions)中的 AzureCloud，可以指定该区域。 例如，如果希望只允许访问美国东部区域中的 Azure AzureCloud，可以指定 AzureCloud.EastUS 作为服务标记。 
* **AzureTrafficManager**（仅限资源管理器）：此标记表示 Azure 流量管理器探测 IP 地址的 IP 地址空间。 有关流量管理器探测 IP 地址的详细信息，请参阅 [Azure 流量管理器常见问题解答](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)。 
* **Storage**（仅限资源管理器）：此标记表示 Azure 存储服务的 IP 地址空间。 如果指定 *Storage* 作为值，则会允许或拒绝发往存储的流量。 如果只想允许对某个特定[区域](https://azure.microsoft.com/regions)中的存储进行访问，可以指定该区域。 例如，如果希望只允许访问美国东部区域中的 Azure 存储，可以指定 *Storage.EastUS* 作为服务标记。 标记表示服务而不是服务的特定实例。 例如，标记可表示 Azure 存储服务，但不能表示特定的 Azure 存储帐户。
* **Sql**（仅限资源管理器）：此标记表示 Azure SQL 数据库和 Azure SQL 数据仓库服务的地址前缀。 如果指定 *Sql* 作为值，则会允许或拒绝发往 Sql 的流量。 如果只想允许对某个特定[区域](https://azure.microsoft.com/regions)中的 Sql 进行访问，可以指定该区域。 例如，如果希望只允许访问美国东部区域中的 Azure SQL 数据库，可以指定 *Sql.EastUS* 作为服务标记。 标记表示服务而不是服务的特定实例。 例如，标记可表示 Azure SQL 数据库服务，但不能表示特定的 SQL 数据库或服务器。
* **AzureCosmosDB**（仅限资源管理器）：此标记表示 Azure Cosmos 数据库服务的地址前缀。 如果指定 *AzureCosmosDB* 作为值，则会允许或拒绝发往 AzureCosmosDB 的流量。 如果仅希望允许访问某特定[区域](https://azure.microsoft.com/regions)中的 AzureCosmosDB，则可以使用 AzureCosmosDB.[区域名称] 格式指定该区域。
* **AzureKeyVault**（仅限资源管理器）：此标记表示 Azure KeyVault 服务的地址前缀。 如果指定 *AzureKeyVault* 作为值，则会允许或拒绝发往 AzureKeyVault 的流量。 如果仅希望允许访问某特定[区域](https://azure.microsoft.com/regions)中的 AzureKeyVault，则可以使用 AzureKeyVault.[区域名称] 格式指定该区域。
* **EventHub**（仅限资源管理器）：此标记表示 Azure EventHub 服务的地址前缀。 如果指定 EventHub 作为值，则会允许或拒绝发往 EventHub 的流量。 如果仅希望允许访问某特定[区域](https://azure.microsoft.com/regions)中的 EventHub，则可以使用 EventHub.[区域名称] 格式指定该区域。 
* **ServiceBus**（仅限资源管理器）：此标记表示 Azure ServiceBus 服务的地址前缀。 如果指定 ServiceBus 作为值，则会允许或拒绝发往 ServiceBus 的流量。 如果仅希望允许访问某特定[区域](https://azure.microsoft.com/regions)中的 ServiceBus，则可以使用 ServiceBus.[区域名称] 格式指定该区域。
* **MicrosoftContainerRegistry**（仅限资源管理器）：此标记表示 Microsoft 容器注册表服务的地址前缀。 如果指定 MicrosoftContainerRegistry 作为值，则会允许或拒绝发往 MicrosoftContainerRegistry 的流量。 如果仅希望允许访问某特定[区域](https://azure.microsoft.com/regions)中的 MicrosoftContainerRegistry，则可使用 MicrosoftContainerRegistry.[区域名称] 格式指定该区域。
* **AzureContainerRegistry**（仅限资源管理器）：此标记表示 Azure 容器注册表服务的地址前缀。 如果指定 AzureContainerRegistry 作为值，则会允许或拒绝发往 AzureContainerRegistry 的流量。 如果仅希望允许访问某特定[区域](https://azure.microsoft.com/regions)中的 AzureContainerRegistry，则可使用 AzureContainerRegistry.[区域名称] 格式指定该区域。 
* **AppService**（仅限资源管理器）：此标记表示 Azure AppService 服务的地址前缀。 如果指定 AppService 作为值，则会允许或拒绝发往 AppService 的流量。 如果仅希望允许访问某特定[区域](https://azure.microsoft.com/regions)中的 AppService，则可使用 AppService.[区域] 格式指定该区域。 
* **AppServiceManagement**（仅限资源管理器）：此标记表示 Azure AppService 管理服务的地址前缀。 如果指定 AppServiceManagement 作为值，则会允许或拒绝发往 AppServiceManagement 的流量。 
* **ApiManagement**（仅限资源管理器）：此标记表示 Azure API 管理服务的地址前缀。 如果指定 ApiManagement 作为值，则会允许或拒绝发往 ApiManagement 的流量。  
* **AzureConnectors**（仅限资源管理器）：此标记表示 Azure 连接器服务的地址前缀。 如果指定 AzureConnectors 作为值，则会允许或拒绝发往 AzureConnectors 的流量。 如果仅希望允许访问某特定[区域](https://azure.microsoft.com/regions)中的 AzureConnectors，则可使用 AzureConnectors.[区域名称] 格式指定该区域。
* **AzureDataLake**（仅限资源管理器）：此标记表示 Azure Data Lake 服务的地址前缀。 如果指定 AzureDataLake 作为值，则会允许或拒绝发往 AzureDataLake 的流量。
* **AzureActiveDirectory**（仅限资源管理器）：此标记表示 AzureActiveDirectory 服务的地址前缀。 如果指定 AzureActiveDirectory 作为值，则会允许或拒绝发往 AzureActiveDirectory 的流量。
* **AzureMonitor**（仅限资源管理器）：此标记表示 AzureMonitor 服务的地址前缀。 如果指定 AzureMonitor 作为值，则会允许或拒绝发往 AzureMonitor 的流量。
* **ServiceFabric**（仅限资源管理器）：此标记表示 ServiceFabric 服务的地址前缀。 如果指定 ServiceFabric 作为值，则会允许或拒绝发往 ServiceFabric 的流量。
* **AzureMachineLearning**（仅限资源管理器）：此标记表示 AzureMachineLearning 服务的地址前缀。 如果指定 AzureMachineLearning 作为值，则会允许或拒绝发往 AzureMachineLearning 的流量。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 防火墙规则，请参阅 [Azure 防火墙规则处理逻辑](rule-processing.md)。