---
title: Azure 应用配置的时间点快照 |Microsoft Docs
description: Azure 应用配置中时间点快照的工作原理概述
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 4f26f3a31dc6303e991c39fc7f85d9bf254d57bc
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885132"
---
# <a name="point-in-time-snapshot"></a>时间点快照

Azure 应用配置记录新建键值对和后续修改键值对的精确时间。 这些记录形成键值更改的完整时间线。 应用配置存储区可以重新构造任何键值的历史记录并重播任何给定时刻（直到现在）的过去值。 通过此功能，可以向后查看并检索旧的键值。 例如，可以获得昨天（在最近部署前）的配置设置，这样便可以在需要回滚应用程序时恢复以前的配置。

## <a name="key-value-retrieval"></a>键值检索

要检索键值，需要指定在 REST API 调用的 HTTP 标头中拍摄键值快照的时间。 例如：

        GET /revisions HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

目前，应用配置将保留 7 天的更改历史记录。

## <a name="next-steps"></a>后续步骤

* [快速入门：创建 ASP.NET Web 应用](quickstart-aspnet-core-app.md)  
