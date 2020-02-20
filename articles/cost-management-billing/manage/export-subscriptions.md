---
title: 导出 Azure 订阅顶层信息 | Microsoft Docs
description: 介绍如何查看与帐户关联的所有 Azure 订阅 ID。
keywords: ''
author: bandersmsft
ms.reviewer: matrive
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 83b6a9db0107cb35448aec491c81c2630e87ddd7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199715"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>导出并查看顶层订阅信息
如果需要查看与用户凭据关联的订阅 ID 集合，请[从 Azure 帐户中心下载具有订阅信息的 .json 文件](https://account.azure.com/subscriptions/download)。

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

下载的 .json 文件提供以下信息：
- 电子邮件：与帐户关联的电子邮件地址。
- Puid：与付费帐户关联的唯一标识符。
- Subscriptionid：属于帐户的订阅的列表，按订阅 ID 枚举。

### <a name="subscriptionsjson-sample"></a>subscriptions.json 示例

```json
{
  "Email":"admin@contoso.com",
  "Puid":"00052xxxxxxxxxxx",
  "SubscriptionIds":[
    "38124d4d-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "7c8308f1-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "39a25f2b-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "52ec2489-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "e42384b2-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "90757cdc-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
  ]
}
```
