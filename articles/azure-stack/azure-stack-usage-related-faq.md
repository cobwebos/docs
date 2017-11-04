---
title: "使用情况 API 相关常见问题 |Microsoft 文档"
description: "Azure 堆栈米、 比较 Azure 使用情况 API、 使用时间和报告的时间，错误代码的列表。"
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: alfredop
ms.openlocfilehash: 166147c8cb4949be1b23e0a06868e66c8a5844f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>在 Azure 堆栈使用情况 API 的常见问题 
本文回答了一些有关 Azure 堆栈使用情况 API 的常见问题。

## <a name="what-meter-ids-can-i-see"></a>可以看到哪些测定仪 Id？
目前，网络、 存储和计算资源提供程序报告使用率。

| **资源提供程序** | **电表 ID** | **测定仪名称** | **单位** | **其他信息** |
| --- | --- | --- | --- | --- | 
| **网络** |F271A8A388C44D93956A063E1D2FA80B |静态 IP 地址使用情况 |IP 地址|使用计数的 IP 地址 | 
| |9E2739BA86744796B465F64674B822BA |动态 IP 地址使用情况 |IP 地址|使用计数的 IP 地址 | 
| **存储** |B4438D5D-453B-4EE1-B42A-DC72E377F1E4 |TableCapacity |GB\*小时数 |表占用的总容量 |
| | B5C15376-6C94-4FDD-B655-1A69D138ACA3 |PageBlobCapacity |GB\*小时数 |由页 blob 的总容量 |
| | B03C6AE7-B080-4BFA-84A3-22C800F315C6 |QueueCapacity |GB\*小时数 |使用的队列的总容量 |
| | 09F8879E-87E9-4305-A572-4B7BE209F857 |BlockBlobCapacity |GB\*小时数 |使用块 blob 的总容量 |
| | B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 |TableTransactions |以 10,000 计的请求计数 |表服务请求 （以 10,000 计） |
| | 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D |TableDataTransIn |以 gb 为单位的传入数据 |以 gb 为单位的表服务数据流入 |
| | 1B8C1DEC-EE42-414B-AA36-6229CF199370 |TableDataTransOut |以 gb 为单位的 Outgress |以 gb 为单位的表服务数据流出量 |
| | 43DAF82B-4618-444A-B994-40C23F7CD438 |BlobTransactions |请求计数以 10,000 计 |Blob 服务请求 （以 10,000 计） |
| | 9764F92C-E44A-498E-8DC1-AAD66587A810 |BlobDataTransIn |以 gb 为单位的传入数据 |以 gb 为单位的 blob 服务数据流入 |
| | 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8 |BlobDataTransOut |以 gb 为单位的 Outgress |以 gb 为单位的 blob 服务数据流出量 |
| | EB43DD12-1AA6-4C4B-872C-FAF15A6785EA |QueueTransactions |请求计数以 10,000 计 |队列服务请求 （以 10,000 计） |
| | E518E809-E369-4A45-9274-2017B29FFF25 |QueueDataTransIn |以 gb 为单位的传入数据 |以 gb 为单位的队列服务数据流入 | 
| | DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2 |QueueDataTransOut |以 gb 为单位的 Outgress |以 gb 为单位的队列服务数据流出量 |
| **计算** |FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5 |基本 VM 大小小时数 |虚拟内核分钟 | Vcores 次数 VM 运行的分钟 |
| |9CD92D4C-BAFD-4492-B278-BEDC2DE8232A |Windows VM 大小小时数 |虚拟内核分钟 | Vcores 次数 VM 运行的分钟 |
| |6DAB500F-A4FD-49C4-956D-229BB9C8C793 |VM 大小小时数 |VM 小时数 |捕获基类和 Windows 的 VM。 不进行调整的 vcores |
| **Key Vault** | EBF13B9F-B3EA-46FE-BF54-396E93D48AB4 |密钥保管库事务 | 中 10000s 请求计数| REST API 收到的请求数由密钥保管库数据平面 |

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>如何实现 Api 与 Azure 堆栈使用[Azure 使用情况 API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) （当前在公共预览版）？
* 租户使用情况 API 是与 Azure API，有一个例外一致： *showDetails*标志目前不支持 Azure 堆栈中。
* 提供程序使用情况 API 仅适用于 Azure 堆栈。
* 目前， [RateCard API](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx) ，它是可用在 Azure 中不可用 Azure 堆栈中。

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>使用时间和报告的时间之间的区别是什么？
使用情况数据报告具有两个主要的时间值：

* **报告的时间**。 使用事件时进入使用情况系统的时间
* **使用时间**。 当 Azure 堆栈资源已使用的时间

特定使用事件，可能会使用时间和报告时间看到值之间存在差异。 延迟可能长达在任何环境中的多个小时。

目前，你可以查询*只能由报告时间*。

## <a name="what-do-these-usage-api-error-codes-mean"></a>这些使用情况 API 错误代码是什么意思？
| **HTTP 状态代码** | **错误代码** | **说明** |
| --- | --- | --- |
| 400/错误的请求 |*NoApiVersion* |*Api 版本*缺少查询参数，则。 |
| 400/错误的请求 |*InvalidProperty* |属性缺失或具有无效值。 响应正文中的错误代码中的消息标识缺少的属性。 |
| 400/错误的请求 |*RequestEndTimeIsInFuture* |值*ReportedEndTime*在将来。 为此参数在将来不允许值。 |
| 400/错误的请求 |*SubscriberIdIsNotDirectTenant* |提供程序 API 调用使用不是有效的租户的调用方的订阅 ID。 |
| 400/错误的请求 |*SubscriptionIdMissingInRequest* |找不到调用方的订阅 ID。 |
| 400/错误的请求 |*InvalidAggregationGranularity* |已请求无效的聚合粒度。 有效值为每日和每小时。 |
| 503 |*ServiceUnavailable* |由于服务正忙于或调用将被阻止出现可重试错误。 |

## <a name="next-steps"></a>后续步骤
[客户计费和 Azure 堆栈中的 chargeback](azure-stack-billing-and-chargeback.md)

[提供程序资源使用情况 API](azure-stack-provider-resource-api.md)

[租户资源使用情况 API](azure-stack-tenant-resource-usage-api.md)

