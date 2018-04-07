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
ms.date: 03/09/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: 3d6f2bf569ecfebe3bf4f535a7a8ab3bf22849f1
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Azure Stack 用量 API 的常见问题解答
此文解答有关 Azure Stack 用量 API 的一些常见问题。

## <a name="what-meter-ids-can-i-see"></a>可以查看哪些计量 ID？
系统针对以下资源提供程序报告用量：

| **资源提供程序** | **电表 ID** | **计量器名称** | **单位** | 其他信息 |
| --- | --- | --- | --- | --- |
| **网络** |F271A8A388C44D93956A063E1D2FA80B |静态 IP 地址用量 |IP 地址| 已用 IP 地址计数。 如果以每日粒度调用用量 API，计量器会返回 IP 地址乘以小时数。 |
| |9E2739BA86744796B465F64674B822BA |动态 IP 地址用量 |IP 地址| 已用 IP 地址计数。 如果以每日粒度调用用量 API，计量器会返回 IP 地址乘以小时数。 |
| **存储** |B4438D5D-453B-4EE1-B42A-DC72E377F1E4 |TableCapacity |GB\*小时 |表使用的总容量。 |
| |B5C15376-6C94-4FDD-B655-1A69D138ACA3 |PageBlobCapacity |GB\*小时 |页 Blob 使用的总容量。 |
| |B03C6AE7-B080-4BFA-84A3-22C800F315C6 |QueueCapacity |GB\*小时 |队列使用的总容量。 |
| |09F8879E-87E9-4305-A572-4B7BE209F857 |BlockBlobCapacity |GB\*小时 |块 Blob 使用的总容量。 |
| |B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 |TableTransactions |请求计数（以万为单位） |表服务请求数（以万为单位）。 |
| |50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D |TableDataTransIn |传入数据 (GB) |表服务传入数据 (GB)。 |
| |1B8C1DEC-EE42-414B-AA36-6229CF199370 |TableDataTransOut |传出数据 (GB) |表服务传出数据 (GB) |
| |43DAF82B-4618-444A-B994-40C23F7CD438 |BlobTransactions |请求计数（以万为单位） |Blob 服务请求数（以万为单位）。 |
| |9764F92C-E44A-498E-8DC1-AAD66587A810 |BlobDataTransIn |传入数据 (GB) |Blob 服务传入数据 (GB)。 |
| |3023FEF4-ECA5-4D7B-87B3-CFBC061931E8 |BlobDataTransOut |传出数据 (GB) |Blob 服务传出数据 (GB)。 |
| |EB43DD12-1AA6-4C4B-872C-FAF15A6785EA |QueueTransactions |请求计数（以万为单位） |队列服务请求数（以万为单位）。 |
| |E518E809-E369-4A45-9274-2017B29FFF25 |QueueDataTransIn |传入数据 (GB) |队列服务传入数据 (GB)。 |
| |DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2 |QueueDataTransOut |传出数据 (GB) |队列服务传出数据 (GB) |
| **Sql RP**            | CBCFEF9A-B91F-4597-A4D3-01FE334BED82 | DatabaseSizeHourSqlMeter   | MB\*小时   | 创建时的总数据库容量。 如果以每日粒度调用用量 API，计量器会返回 MB 乘以小时数。 |
| **MySql RP**          | E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3 | DatabaseSizeHourMySqlMeter | MB\*小时    | 创建时的总数据库容量。 如果以每日粒度调用用量 API，计量器会返回 MB 乘以小时数。 |
| **计算** |FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5 |基本 VM 大小小时 |虚拟核心小时数 | 虚拟核心数乘以 VM 运行小时数。 |
| |9CD92D4C-BAFD-4492-B278-BEDC2DE8232A |Windows VM 大小小时数 |虚拟核心小时数 | 虚拟核心数乘以 VM 运行小时数。 |
| |6DAB500F-A4FD-49C4-956D-229BB9C8C793 |VM 大小小时数 |VM 小时数 |捕获基本 VM 和 Windows 的 VM。 不针对核心进行调整。 |
| **Key Vault** |EBF13B9F-B3EA-46FE-BF54-396E93D48AB4 |Key Vault 事务 | 请求计数（以万为单位）| Key Vault 数据平面接收的 REST API 请求数。 |
| |2C354225-B2FE-42E5-AD89-14F0EA302C87 |高级密钥事务 | 10K 事务|     RSA 3K/4K，ECC 密钥事务。 （预览版）。 |
| **应用服务** | 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA | 应用服务 | 虚拟核心小时数 | 用于运行应用服务的虚拟核心数。 注意：Microsoft 使用此计量对基于 Azure Stack 的应用服务收费。 云服务提供商可能使用其他应用服务计量（见下）来计算其租户的用量。 |
|  | 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE | Functions 请求 | 10 个请求 | 请求的执行总数（按 10 个执行计）。 每当函数为响应事件而运行，或者被绑定触发时，计为一次执行。 |
|  | D1D04836-075C-4F27-BF65-0A1130EC60ED | Functions - 计算 | GB-s | 以千兆字节/秒 (GB/s) 计量的资源消耗量。 **观察到的资源消耗**的计算方式是：将平均内存大小 (GB) 乘以执行函数所需的时间（毫秒）。 在衡量函数使用的内存时，会向上舍入到最接近的 128 MB，最大内存大小为 1,536 MB，而在计算执行时间时，则向上舍入到最接近的 1 毫秒。 执行一次函数的最短执行时间和最小内存分别为 100 毫秒和 128 MB。 |
|  | 957E9F36-2C14-45A1-B6A1-1723EF71A01D | 共享应用服务小时数 | 1 小时	 | 共享应用服务计划的每小时使用情况。 计划按单个应用来计量。 |
|  | 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9 | 免费应用服务小时数 | 1 小时	 | 免费应用服务计划的每小时使用情况。 计划按单个应用来计量。 |
|  | 88039D51-A206-3A89-E9DE-C5117E2D10A6 | 小型标准应用服务小时数 | 1 小时	 | 根据大小与实例数进行计算。 |
|  | 83A2A13E-4788-78DD-5D55-2831B68ED825 | 中型标准应用服务小时数 | 1 小时	 | 根据大小与实例数进行计算。 |
|  | 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6 | 大型标准应用服务小时数 | 1 小时	 | 根据大小与实例数进行计算。 |
|  | 自定义辅助角色层 | 自定义辅助角色层 | 小时 | 确定性计量 ID 根据 SKU 和自定义辅助角色层名称来创建。 此计量 ID 对每个自定义辅助角色层来说都是唯一的。 |
|  | 264ACB47-AD38-47F8-ADD3-47F01DC4F473 | SNI SSL | 按 SNI SSL 绑定 | 应用服务支持两类 SSL 连接：服务器名称指示 (SNI) SSL 连接和 IP 地址 SSL 连接。 基于 SNI 的 SSL 适用于新式浏览器，而基于 IP 的 SSL 适用于所有浏览器。 |
|  | 60B42D72-DC1C-472C-9895-6C516277EDB4 | IP SSL | 按基于 IP 的 SSL 绑定 | 应用服务支持两类 SSL 连接：服务器名称指示 (SNI) SSL 连接和 IP 地址 SSL 连接。 基于 SNI 的 SSL 适用于现代浏览器，而基于 IP 的 SSL 适用于所有浏览器。 |
|  | 73215A6C-FA54-4284-B9C1-7E8EC871CC5B | Web 进程 |  | 按每小时每活动站点计算。 |
|  | 5887D39B-0253-4E12-83C7-03E1A93DFFD9 | 外部出口带宽 | GB | 总传入请求响应字节数 + 总传出请求字节数 + 总传入 FTP 请求响应类型数 + 总传入 Web 部署请求响应字节数。 |

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Azure Stack 用量 API 与 [Azure 用量 API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)（目前为公共预览版）有何差别？
* 租户用量 API 与 Azure API 相同，但有一点除外：Azure Stack 目前不支持 *showDetails* 标志。
* 提供程序用量 API 只适用于 Azure Stack。
* 目前，Azure Stack 不提供 Azure 中所提供的[费率卡 API](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx)。

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
