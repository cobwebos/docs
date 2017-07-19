---
title: "Azure 数据工厂实体的命名规则 | Microsoft Docs"
description: "描述数据工厂实体的命名规则。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/24/2017
ms.author: shlo
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: e8f190ec8f6cffdcc5748454ab7ae765648f2e30
ms.contentlocale: zh-cn
ms.lasthandoff: 06/21/2017


---
# <a name="azure-data-factory---naming-rules"></a>Azure 数据工厂 - 命名规则
下表提供了数据工厂项目的命名规则。

| Name | 名称唯一性 | 验证检查 |
|:--- |:--- |:--- |
| Data Factory |在 Microsoft Azure 内唯一。 名称不区分大小写，即 `MyDF` 和 `mydf` 指的是同一个数据工厂。 |<ul><li>一个数据工厂绑定到一个 Azure 订阅。</li><li>对象名称必须以字母或数字开头，并且只能包含字母、数字和短划线 (-) 字符。</li><li>每个短划线 (-) 字符的前后必须紧接字母或数字。 容器名称中不允许使用连续短划线。</li><li>名称长度为 3-63 个字符。</li></ul> |
| 链接服务/表/管道 |在数据工厂中唯一。 名称不区分大小写。 |<ul><li>表名称的最大字符数：260。</li><li>对象名称必须以字母、数字或下划线 (_) 开头。</li><li>不允许使用以下字符：“.”、“+”、“?”、“/”、“<”、“>”、“*”、“%”、“&”、“:”、“\\”</li></ul> |
| 资源组 |在 Microsoft Azure 内唯一。 名称不区分大小写。 |<ul><li>最大字符数：1000。</li><li>名称可包含字母、数字和以下字符：“-”、“_”、“,”和“.”</li></ul> |


