---
title: Azure 应用配置时间点快照
description: Azure 应用配置中时间点快照的工作原理概述
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.openlocfilehash: 4a352ba913b6ad4e3c8607677078e21070f294fd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899589"
---
# <a name="point-in-time-snapshot"></a>时间点快照

Azure 应用程序配置记录新建键值对和后续修改键值对的精确时间。 这些记录形成键值更改的完整时间线。 应用程序配置存储区可以重新构造任何键值的历史记录并重播任何给定时刻（直到现在）的过去值。 通过此功能，可以向后查看并检索旧的键值。 例如，可以获得昨天（在最近部署前）的配置设置，这样便可以恢复以前的配置和回滚应用程序。

## <a name="key-value-retrieval"></a>键值检索

要检索键值，请指定在 REST API 调用的 HTTP 标头中拍摄键值快照的时间。 例如：

```rest
GET /kv HTTP/1.1
Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT
```

目前，应用程序配置将保留 7 天的更改历史记录。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建一个 ASP.NET Core Web 应用](./quickstart-aspnet-core-app.md)  
