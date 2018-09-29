---
title: 了解如何将预留折扣应用于 Azure Cosmos DB | Microsoft Docs
description: 了解如何将预留折扣应用于 Azure Cosmos DB 中的预配吞吐量（RU/秒）。
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: cwatson
ms.reviewer: sngun
ms.openlocfilehash: adcd91a8f1b3368d03f4b634e7aef40104d953e3
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393632"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>了解如何将预留折扣应用于 Azure Cosmos DB

购买 Azure Cosmos DB 保留容量后，预留折扣会自动应用到与预留属性和数量匹配的 Azure Cosmos DB 资源。 预留涵盖为 Azure Cosmos DB 资源预配的吞吐量，它不包含软件、网络、存储或预定义的容器费用。

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>预留折扣应用于 Azure Cosmos DB 帐户

预留折扣以按小时计算的每秒请求单位数（RU/秒）的形式应用于[预配吞吐量](../cosmos-db/request-units.md)。 对于不是整小时运行的 Azure Cosmos DB 资源，预留折扣会自动应用到与预留属性匹配的其他 Cosmos DB 资源。 折扣可以应用到同时运行的 Azure Cosmos DB 资源。 如果与预留属性匹配的 Cosmos DB 资源不是整小时运行，则无法获得该小时的完整预留折扣权益。

* 折扣是分层式的，这意味着有更高请求单位的预留可以提供更高折扣。  
* 预订购买将对所有区域应用折扣，其折扣率相当于区域的按需定价。 有关每个区域的预留折扣率，请参阅本文的[区域的预留折扣](#reservation-discount-per-region)部分。

## <a name="reservation-discount-per-region"></a>各区域的预留折扣
在单个订阅或注册/帐户范围内，预留折扣会应用于按小时计算的 Azure Cosmos DB 吞吐量成本。 预留折扣适用于不同区域的计量使用情况，其折扣率如下：

|计量描述  |区域 |比率  |
|---------|---------|---------|
|Azure Cosmos DB - 100 RU/秒/小时 - 亚太东南部  |  亚太东南部    |   1      |
|Azure Cosmos DB - 100 RU/秒/小时 - 亚太东部 |   亚太东部   |    1     |
|Azure Cosmos DB - 100 RU/秒/小时 - 欧洲北部|  欧洲北部       |    1     |
|Azure Cosmos DB - 100 RU/秒/小时 - 韩国南部|    韩国南部     |     1    |
|Azure Cosmos DB - 100 RU/秒/小时 - 欧洲西部|    欧洲西部     |      1   |
|Azure Cosmos DB - 100 RU/秒/小时 - 韩国中部|   韩国中部    |       1  |
|Azure Cosmos DB - 100 RU/秒/小时 - 英国南部|   英国南部      |     1    |
|Azure Cosmos DB - 100 RU/秒/小时 - 英国西部|   英国西部      |    1     |
|Azure Cosmos DB - 100 RU/秒/小时 - 英国北部 |   英国北部    |     1    |
|Azure Cosmos DB - 100 RU/秒/小时 - 英国南部 2|   英国南部 2      |     1    |
|Azure Cosmos DB - 100 RU/秒/小时 - 美国东部 2|  美国东部 2     |     1    |
|Azure Cosmos DB - 100 RU/秒/小时 - 美国中北部|   美国中北部      |     1    |
|Azure Cosmos DB - 100 RU/秒/小时 - 美国西部|   美国西部      |     1    |
|Azure Cosmos DB - 100 RU/秒/小时 - 美国中部| 美国中部        |     1    |
|Azure Cosmos DB - 100 RU/秒/小时 - 美国西部 2|   美国西部 2      |      1   |
|Azure Cosmos DB - 100 RU/秒/小时 - 美国中西部|   美国中西部      |       1  |
|Azure Cosmos DB - 100 RU/秒/小时 - 美国东部|   美国东部      |  1       |
|Azure Cosmos DB - 100 RU/秒/小时 - 南非北部|     南非北部    |   1      |
|Azure Cosmos DB - 100 RU/秒/小时 - 南非西部 |    南非西部      |    1     |
|Azure Cosmos DB - 100 RU/秒/小时 - 印度南部|    印度南部     |    1.0375    |
|Azure Cosmos DB - 100 RU/秒/小时 - 加拿大东部|   加拿大东部      |    1.1      |
|Azure Cosmos DB - 100 RU/秒/小时 - 日本东部|   日本东部      |    1.125     |
|Azure Cosmos DB - 100 RU/秒/小时 - 日本西部|     日本西部    |   1.125       |
|Azure Cosmos DB - 100 RU/秒/小时 - 印度西部|     印度西部    |    1.1375     |
|Azure Cosmos DB - 100 RU/秒/小时 - 印度中部|    印度中部     |  1.1375       |
|Azure Cosmos DB - 100 RU/秒/小时 - 澳大利亚东部|     澳大利亚东部    |   1.15       |
|Azure Cosmos DB - 100 RU/秒/小时 - 加拿大中部|  加拿大中部       |   1.2       |
|Azure Cosmos DB - 100 RU/秒/小时 - 法国中部|   法国中部      |    1.25      |
|Azure Cosmos DB - 100 RU/秒/小时 - 巴西南部|  巴西南部       |   1.5      |
|Azure Cosmos DB - 100 RU/秒/小时 - 澳大利亚中部|   澳大利亚中部      |   1.5      |
|Azure Cosmos DB - 100 RU/秒/小时 - 澳大利亚中部 2| 澳大利亚中部 2        |    1.5     |
|Azure Cosmos DB - 100 RU/秒/小时 - 法国南部|    法国南部     |    1.625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>展示预留折扣应用方式的方案

请考虑以下预留要求：

* 所需吞吐量：50,000 RU/秒  
* 使用的区域：2。 

在本例中，总按需费用针对这两个地区数量为 500 的 100 RU/秒计量，每小时总 RU/秒的消耗量为 100,000。 

**方案 1**

例如，如果需要在“美国中北部”和“美国西部”区域中部署 Azure Cosmos DB，并且每个区域的吞吐量消耗均为 50,000 RU/秒， 则预购 100,000 RU/秒完全能够平衡你的按需费用。

预留所包含的折扣的计算方式为（吞吐量消耗 * reservation_discount_ratio_for_that_region）。 对于“美国中北部”和“美国西部”区域，预留折扣率为“1”。 因此，总折扣的 RU/秒为 100,000 RU/秒（该值的计算方式为：50,000 * 1 + 50,000 * 1 = 100,000 RU/秒），无需按照常规即用即付费率支付任何额外费用。 

|计量描述 | 区域 |吞吐量消耗（RU/秒） |应用于 RU/秒的预留折扣 |
|---------|---------|---------|---------|
|Azure Cosmos DB - 100 RU/秒/小时 - 美国中北部  |   美国中北部  | 50,000  | 50,000  |
|Azure Cosmos DB - 100 RU/秒/小时 - 美国西部  |  美国西部   |  50,000  |  50,000 |

**方案 2**

例如，如果需要在“澳大利亚中部 2”和“法国南部”区域中部署 Azure Cosmos DB，并且每个区域的吞吐量消耗均为 50,000 RU/秒， 则预购 100,000 RU/秒将在以下情况下适用（假定澳大利亚中部 2 的使用情况为第一次打折）：

|计量描述 | 区域 |吞吐量消耗（RU/秒） |应用于 RU/秒的预留折扣 |
|---------|---------|---------|---------|
|Azure Cosmos DB - 100 RU/秒/小时 - 澳大利亚中部 2  |  澳大利亚中部 2   |  50,000  |  50,000   |
|Azure Cosmos DB - 100 RU/秒/小时 - 法国南部  |  法国南部   |  50,000 |  15,384  |

“澳大利亚中部 2”区域 50,000 个使用单位对应于可计费使用（或规范化使用）的 75,000 RU/秒。 该值的计算方式为（吞吐量消耗 * reservation_discount_ratio_for_that_region），等于可计费或规范化使用的 75,000 RU/秒（该值的计算方式为：50,000 * 1.5 = 75,000 RU/秒）。 

预购 100,000 RU/秒可抵消“澳大利亚中部 2”区域的 75,000 RU/秒，并向“法国南部”区域提供剩余的 25,000 RU/秒。 在剩余的 25,000 RU/秒中，15,384 RU/秒的预留折扣（该值的计算方式为：25000 / 1.625 = 15,384 RU/秒）将应用于“法国南部”区域。 按照正常的即用即付费率收取“法国南部”区域中剩余的 34,616 RU/秒的费用。 

Azure 计费系统将向第一个处理的与预留配置相匹配的实例分配预留计费权益（例如，在本例中为澳大利亚中部 2）。

若要了解 Azure 预留的应用情况并在计费使用情况报告中查看该信息，请参阅[了解 Azure 预留使用情况](../billing/billing-understand-reserved-instance-usage-ea.md)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

* [什么是 Azure 预留？](../billing/billing-save-compute-costs-reservations.md)  
* [通过 Azure Cosmos DB 保留容量预付 Azure Cosmos DB 资源费用](../cosmos-db/cosmos-db-reserved-capacity.md)  
* [通过 Azure SQL 数据库保留容量预付 SQL 数据库计算资源费用](../sql-database/sql-database-reserved-capacity.md)  
* [管理 Azure 预留项](../billing/billing-manage-reserved-vm-instance.md)  
* [了解即用即付订阅的预留使用情况](../billing/billing-understand-reserved-instance-usage.md)  
* [了解企业合约的预留使用情况](../billing/billing-understand-reserved-instance-usage-ea.md)  
* [了解 CSP 订阅的预留使用情况](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员

如果仍有疑问，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

