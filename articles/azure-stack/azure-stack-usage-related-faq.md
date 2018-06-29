---
title: 用量 API 相关的常见问题解答 | Microsoft Docs
description: Azure Stack 计量器列表、与 Azure 用量 API 的比较、使用时间和报告时间、错误代码。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: ac22ba34bff1d5321c05bc0a0a1b14ca742079a7
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051486"
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Azure Stack 用量 API 的常见问题解答

此文解答有关 Azure Stack 用量 API 的一些常见问题。

## <a name="what-meter-ids-can-i-see"></a>可以查看哪些计量 ID？
系统针对以下资源提供程序报告用量：

**网络**  
  
**计数 ID**: F271A8A388C44D93956A063E1D2FA80B  
**测定仪名称**： 静态 IP 地址使用情况  
**单元**: IP 地址  
**说明**： 使用计数的 IP 地址。 如果以每日粒度调用用量 API，计量器会返回 IP 地址乘以小时数。  
  
**计数 ID**: 9E2739BA86744796B465F64674B822BA  
**测定仪名称**： 动态 IP 地址使用情况  
**单元**: IP 地址  
**说明**： 使用计数的 IP 地址。 如果以每日粒度调用用量 API，计量器会返回 IP 地址乘以小时数。  
  
**存储**  
  
**计数 ID**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**测定仪名称**: TableCapacity  
**单元**: GB\*小时数  
**说明**： 总容量使用的表。  
  
**计数 ID**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**测定仪名称**: PageBlobCapacity  
**单元**: GB\*小时数  
**说明**： 总容量由页 blob。  
  
**计数 ID**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**测定仪名称**: QueueCapacity  
**单元**: GB\*小时数  
**说明**： 总容量使用的队列。  
  
**计数 ID**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**测定仪名称**: BlockBlobCapacity  
**单元**: GB\*小时数  
**说明**： 总容量使用的块 blob。  
  
**计数 ID**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**测定仪名称**: TableTransactions  
**单元**: 000's年请求在 10 中的计数  
**说明**： 表服务请求 （以 10,000 计）。  
  
**计数 ID**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**测定仪名称**: TableDataTransIn  
**单元**： 以 gb 为单位的传入数据  
**说明**： 表服务数据流入以 gb 为单位。  
  
**计数 ID**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**测定仪名称**: TableDataTransOut  
**单元**： 出口以 gb 为单位  
**说明**： 表服务以 gb 为单位的数据传出  
  
**计数 ID**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**测定仪名称**: BlobTransactions  
**单元**: 10，000's年请求计数  
**说明**: Blob 服务请求 （以 10,000 计）。  
  
**计数 ID**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**测定仪名称**: BlobDataTransIn  
**单元**： 以 gb 为单位的传入数据  
**说明**: Blob 服务数据流入以 gb 为单位。  
  
**计数 ID**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**测定仪名称**: BlobDataTransOut  
**单元**： 出口以 gb 为单位  
**说明**: Blob 服务以 gb 为单位的数据流出量。  
  
**计数 ID**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**测定仪名称**: QueueTransactions  
**单元**: 10，000's年请求计数  
**说明**： 队列服务请求 （以 10,000 计）。  
  
**计数 ID**: E518E809-E369-4A45-9274-2017B29FFF25  
**测定仪名称**: QueueDataTransIn  
**单元**： 以 gb 为单位的传入数据  
**说明**： 队列服务数据流入以 gb 为单位。  
  
**计数 ID**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**测定仪名称**: QueueDataTransOut  
**单元**： 出口以 gb 为单位  
**说明**： 队列服务数据流出量，以 gb 为单位  
  
**Sql RP**  
  
**计数 ID**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**测定仪名称**: DatabaseSizeHourSqlMeter  
**单元**: MB\*小时数  
**说明**： 在创建的总 DB 容量。 如果以每日粒度调用用量 API，计量器会返回 MB 乘以小时数。  
  
**MySql RP**  
  
**计数 ID**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**测定仪名称**: DatabaseSizeHourMySqlMeter  
**单元**: MB\*小时数  
**说明**： 在创建的总 DB 容量。 如果以每日粒度调用用量 API，计量器会返回 MB 乘以小时数。  
  
**计算**  
  
**计数 ID**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**测定仪名称**： 基 VM 大小小时数  
**单元**： 虚拟核心小时数  
**说明**： 虚拟内核数乘以 VM 运行的小时数。  
  
**计数 ID**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**测定仪名称**: Windows VM 大小小时数  
**单元**： 虚拟核心小时数  
**说明**： 虚拟内核数乘以 VM 运行的小时数。  
  
**计数 ID**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**测定仪名称**: VM 大小小时数  
**单元**: VM 小时数  
**说明**： 捕获基类和 Windows VM。 不针对核心进行调整。  
  
**Key Vault**  
  
**计数 ID**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**测定仪名称**： 密钥保管库事务  
**单元**: 000's年请求在 10 中的计数  
**说明**： 收到的密钥保管库数据平面的数量的 REST API 请求。  
  
**计数 ID**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**测定仪名称**： 高级密钥事务  
**单元**: 10k 次交易  
**说明**: RSA 3 K/4k ECC 密钥事务。 （预览版）。  
  
*应用程序服务**  
  
**计数 ID**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**测定仪名称**: App Service  
**单元**： 虚拟核心小时数  
**说明**： 用于运行应用程序服务的虚拟内核数。 注意：Microsoft 使用此计量对基于 Azure Stack 的应用服务收费。 云服务提供商可能使用其他应用服务计量（见下）来计算其租户的用量。  
  
**计数 ID**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**测定仪名称**： 函数请求  
**单元**: 10 个请求  
**说明**: （每 10 执行） 的请求执行的总数。 每当函数为响应事件而运行，或者被绑定触发时，计为一次执行。  
  
**计数 ID**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**测定仪名称**： 函数-计算  
**单元**: GB-s  
**说明**： 资源消耗以秒为单位千兆字节 (GB/s)。 **观察到的资源消耗**的计算方式是：将平均内存大小 (GB) 乘以执行函数所需的时间（毫秒）。 在衡量函数使用的内存时，会向上舍入到最接近的 128 MB，最大内存大小为 1,536 MB，而在计算执行时间时，则向上舍入到最接近的 1 毫秒。 执行一次函数的最短执行时间和最小内存分别为 100 毫秒和 128 MB。  
  
**计数 ID**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**测定仪名称**： 共享 App Service 小时数  
**单元**: 1 小时  
**说明**： 每小时使用情况的 App Service 计划的分片。 计划按单个应用来计量。  
  
**计数 ID**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**测定仪名称**： 免费 App Service 小时数  
**单元**: 1 小时  
**说明**： 每小时使用情况的免费 App Service 计划。 计划按单个应用来计量。  
  
**计数 ID**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**测定仪名称**： 小标准的 App Service 小时数  
**单元**: 1 小时  
**说明**： 计算根据的大小和实例数。  
  
**计数 ID**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**测定仪名称**： 中等标准 App Service 小时数  
**单元**: 1 小时  
**说明**： 计算根据的大小和实例数。  
  
**计数 ID**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**测定仪名称**： 大型标准的 App Service 小时数  
**单元**: 1 小时  
**说明**： 计算根据的大小和实例数。  
  
自定义辅助角色层  
  
**计数 ID**:*自定义的辅助角色层*  
**测定仪名称**： 自定义辅助角色层  
**单元**： 小时数  
**说明**： 确定性测定仪 ID 创建取决于 SKU 和自定义的辅助角色层名称。 此计量 ID 对每个自定义辅助角色层来说都是唯一的。  
  
**计数 ID**: 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**测定仪名称**: SNI SSL  
**单元**： 每个 SNI SSL 绑定  
**说明**: App Service 支持两种类型的 SSL 连接： 服务器名称指示 (SNI) SSL 和 IP 地址的 SSL 连接。 基于 SNI 的 SSL 适用于新式浏览器，而基于 IP 的 SSL 适用于所有浏览器。  
  
**计数 ID**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**测定仪名称**: IP SSL  
**单元**： 每个 IP 基于的 SSL 绑定  
**说明**: App Service 支持两种类型的 SSL 连接： 服务器名称指示 (SNI) SSL 和 IP 地址的 SSL 连接。 基于 SNI 的 SSL 适用于新式浏览器，而基于 IP 的 SSL 适用于所有浏览器。  
  
**计数 ID**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**测定仪名称**: Web 进程  
**单元**:  
**说明**： 计算每个每小时的活动站点。  
  
**计数 ID**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**测定仪名称**： 外部出口带宽  
**单元**: GB  
**说明**： 总传入的请求响应字节 + 总传出请求字节 + 总传入 FTP 请求响应字节 + 总传入的 web 部署请求响应字节数。  
  

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Azure Stack 用量 API 与 [Azure 用量 API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)（目前为公共预览版）有何差别？
* 租户用量 API 与 Azure API 相同，但有一点除外：Azure Stack 目前不支持 *showDetails* 标志。
* 提供程序用量 API 只适用于 Azure Stack。
* 目前，Azure Stack 不提供 Azure 中所提供的[费率卡 API](https://msdn.microsoft.com/library/azure/mt219004.aspx)。

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>使用时间与报告时间有何差别？
用量数据报告包含两个主要时间值：

* **报告时间**。 用量事件进入用量系统的时间
* **使用时间**。 使用 Azure Stack 资源的时间

你可能会发现，特定用量事件的“使用时间”与“报告时间”值有差异。 在任何环境中，延迟可能长达数小时。

目前，只能按“报告时间”查询。

## <a name="what-do-these-usage-api-error-codes-mean"></a>这些用量 API 错误代码的含义是什么？
| **HTTP 状态代码** | **错误代码** | **说明** |
| --- | --- | --- |
| 400/错误的请求 |*NoApiVersion* |未提供 *api-version* 查询参数。 |
| 400/错误的请求 |*InvalidProperty* |属性缺失或使用了无效值。 响应正文中错误代码内的消息指示缺少属性。 |
| 400/错误的请求 |*RequestEndTimeIsInFuture* |*ReportedEndTime* 的值是将来时间。 此参数不允许将来的时间值。 |
| 400/错误的请求 |*SubscriberIdIsNotDirectTenant* |提供程序 API 调用使用的订阅 ID 不是调用方的有效租户。 |
| 400/错误的请求 |*SubscriptionIdMissingInRequest* |缺少调用方的订阅 ID。 |
| 400/错误的请求 |*InvalidAggregationGranularity* |请求的聚合粒度无效。 有效值为 daily 和 hourly。 |
| 503 |*ServiceUnavailable* |由于服务繁忙或调用受到限制，发生了可重试的错误。 |

## <a name="next-steps"></a>后续步骤
[Azure Stack 中的客户计费和退款](azure-stack-billing-and-chargeback.md)

[提供程序资源使用情况 API](azure-stack-provider-resource-api.md)

[租户资源使用情况 API](azure-stack-tenant-resource-usage-api.md)
