---
title: 使用 REST API 进行 Azure 安全中心符合性监视 | Microsoft Docs
description: 使用 Azure 安全中心 REST API 查看自动符合性扫描的结果。
services: security-center
documentationcenter: na
author: rloutlaw
manager: angerobe
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rloutlaw
ms.openlocfilehash: 651d7737258f1b1b17c8392a09882388ddf95635
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819579"
---
# <a name="review-security-center-compliance-results-using-the-azure-rest-apis"></a>使用 Azure REST API 查看安全中心符合性结果

本文介绍如何使用 Azure REST API 检索订阅列表的安全符合性信息。

## <a name="review-compliance-for-each-subscription"></a>查看每个订阅的符合性

以下示例使用[获取符合性](/rest/api/securitycenter/compliances/get)操作获取给定符合性和订阅的安全评估信息。

```http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/compliances/{complianceName}?api-version=2017-08-01-preview
```

`{complianceName}` 参数是必需的，并且应包含 `{subscription-id}` 的评估符合性名称。 输出将包含评估结果，例如：

```json
{
...
  "properties": {
    "assessmentResult": [
      {
        "segmentType": "Compliant",
        "percentage": 77.777777777777786
      }
    ],
    "resourceCount": 18,
    "assessmentTimestampUtcDate": "2018-01-01T00:00:00Z"
  }
}
```

## <a name="review-compliance-for-multiple-subscriptions"></a>查看多个订阅的符合性

若要获取多个订阅的数据，请通过以下调用先使用[列出订阅](/rest/api/resources/subscriptions/list)操作获取订阅列表。 为每个返回的订阅 ID 调用上述[获取符合性](/rest/api/securitycenter/compliances/get)操作。

API 调用为：

```http
GET https://management.azure.com/subscriptions?api-version=2016-06-01
```

这将返回包含以下值的数组。 在上述调用中使用 subscriptionId 值来查看所有订阅的符合性信息。

```json
"value": [
    {
      "id": "/subscriptions/{subscription-id}",
      "subscriptionId": "{subscription-id}",
      "displayName": "Demo subscription",
      ...
    }
```






