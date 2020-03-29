---
title: 区域或 SKU 系列不可用 - Azure
description: 某些 SKU 系列对于此区域的选定订阅不可用，这可能需要订阅管理支持请求。
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: b0f0762ded6804c0b0d90a19223c082f0fb8fd49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843614"
---
# <a name="region-or-sku-unavailable"></a>区域或 SKU 不可用

本文介绍如何解决 Azure 订阅无法访问区域或 VM SKU 的问题。

## <a name="symptoms"></a>症状

部署虚拟机时，收到以下错误消息之一：

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

购买虚拟机预留实例时，收到以下错误消息之一：

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

创建支持请求以增加计算核心配额时，区域或 SKU 系列无法选择。

## <a name="solution"></a>解决方案

首先，我们建议你考虑选择满足业务需求的备选区域或 SKU。

如果找不到合适的区域或 SKU，请按照以下步骤创建**订阅管理**[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)：

1. 在[Azure 门户](https://portal.azure.com)菜单中，选择 **"帮助 + 支持**"。 然后选择“新建支持请求”。****

1. 在 **"基本"** 中，对于**问题类型**，请选择**订阅管理**。

1. 选择**订阅**并在**摘要**中输入简要说明。

   !["新建支持请求"的基础知识选项卡](./media/SKU-series-unavailable/support-request-basics.png)

1. 对于**问题类型**，**选择"选择问题类型**"。

1. 对于 **"选择问题类型**"，请选择一个选项，例如，"**无法访问我的订阅或资源** > **"我的问题未列在上面**。 选择“保存”。****

   ![指定请求的问题](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. 选择 **"下一步"：解决方案**以探索可能的解决方案。 如有必要，请选择 **"下一步：详细信息**"以继续。

1. 输入您可以提供的任何其他信息以及您的联系信息。

1. 选择“查看 + 创建”****。 验证信息后，选择 **"创建**"以创建请求。

## <a name="send-us-your-suggestions"></a>向我们发送您的建议

我们总是乐于听取反馈和建议！ 向我们发送[建议](https://feedback.azure.com/forums/266794-support-feedback)。 此外，您可以在[推特](https://twitter.com/azuresupport)或[MSDN论坛上](https://social.msdn.microsoft.com/Forums/azure)与我们互动。

## <a name="learn-more"></a>了解详细信息

[Azure 支持常见问题](https://azure.microsoft.com/support/faq)
