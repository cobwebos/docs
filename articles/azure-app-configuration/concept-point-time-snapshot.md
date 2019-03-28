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
ms.openlocfilehash: 6238f96c9e8df0431e42caa5e5846af3fc60e681
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484009"
---
# <a name="point-in-time-snapshot"></a>时间点快照

Azure 应用程序配置记录新建键值对和后续修改键值对的精确时间。 这些记录形成键值更改的完整时间线。 应用程序配置存储区可以重新构造任何键值的历史记录并重播任何给定时刻（直到现在）的过去值。 通过此功能，可以向后查看并检索旧的键值。 例如，可以获得昨天（在最近部署前）的配置设置，这样便可以恢复以前的配置和回滚应用程序。

## <a name="key-value-retrieval"></a>键值检索

要检索键值，请指定在 REST API 调用的 HTTP 标头中拍摄键值快照的时间。 例如：

        GET /kv HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

目前，应用程序配置将保留 7 天的更改历史记录。

## <a name="next-steps"></a>后续步骤

* [快速入门：创建 ASP.NET Web 应用](quickstart-aspnet-core-app.md)  
