---
title: 导出 Azure 订阅顶层信息 | Microsoft Docs
description: 介绍如何查看与帐户关联的所有 Azure 订阅 ID。
keywords: ''
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/9/2017
ms.author: adpick
ms.openlocfilehash: b995b0c7154faab66a44bef1a7d74eeb8404e5c1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658938"
---
# <a name="export-and-view-your-top-level-subscription-information"></a>导出并查看顶层订阅信息
如果需要查看与用户凭据关联的订阅 ID 集合，请[从 Azure 帐户中心下载具有订阅信息的 .json 文件](http://account.azure.com/subscriptions/download)。

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

下载的 .json 文件提供以下信息：
- Email：与帐户关联的电子邮件地址。
- Puid：与付费帐户关联的唯一标识符。
- SubscriptionId：属于帐户的订阅的列表，按订阅 ID 枚举。

### <a name="subscriptionsjson-sample"></a>subscriptions.json 示例
~~~~
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
~~~~
