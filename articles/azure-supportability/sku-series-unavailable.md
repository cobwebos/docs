---
title: "SKU 系列不可用 | Microsoft 文档"
description: "对于该区域的选定订阅，某些 SKU 系列不可用。"
services: Azure Supportability
documentationcenter: 
author: stevendotwang
manager: rajatk
editor: 
ms.service: azure-supportability
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: 62964d0c5d75168226a35b25e5c256a1b57f3f81
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2017
---
# <a name="region-or-sku-unavailable"></a>区域或 SKU 不可用
本文介绍如何解决 Azure 订阅无法访问区域或 VM SKU 的问题。

## <a name="symptoms"></a>症状

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>部署虚拟机时，收到以下错误消息之一：
```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>购买保留虚拟机实例时，收到以下错误消息之一：

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>在创建用于增加计算核心配额的支持请求时，无法选择某一区域或 SKU 系列。

## <a name="solution"></a>解决方案
首先，我们建议你考虑选择满足业务需求的备选区域或 SKU。 如果找不到合适的区域或 SKU，请按照以下步骤创建“订阅管理”[支持请求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)：


- 在“基本信息”页上，选择“订阅管理”作为问题类型，选择订阅并单击“下一步”。

![“基本信息”边栏选项卡](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   在“问题”页上，选择“其他常规问题”作为问题类型。
- 在“详细信息”部分中：
  - 请指示是要部署虚拟机还是要购买保留虚拟机实例
  - 请指定区域、SKU 以及计划部署或购买的虚拟机实例数


![问题](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   输入联系人详细信息，然后单击“创建”。

![联系信息](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>反馈
我们始终乐于接受反馈和建议！ 向我们发送[建议](https://feedback.azure.com/forums/266794-support-feedback)。 此外，可以通过 [Twitter](https://twitter.com/azuresupport) 或 [MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure)与我们联系。

## <a name="learn-more"></a>了解详细信息
[Azure 支持常见问题](https://azure.microsoft.com/support/faq)

