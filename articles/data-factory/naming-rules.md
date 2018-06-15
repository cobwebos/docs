---
title: Azure 数据工厂实体的命名规则 | Microsoft Docs
description: 描述数据工厂实体的命名规则。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: e9d2140edc64daca4df0463dbfdcd5b06f072012
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620402"
---
# <a name="azure-data-factory---naming-rules"></a>Azure 数据工厂 - 命名规则
下表提供了数据工厂项目的命名规则。

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 中的命名规则](v1/data-factory-naming-rules.md)。

| 名称 | 名称唯一性 | 验证检查 |
|:--- |:--- |:--- |
| 数据工厂 |在 Microsoft Azure 内唯一。 名称不区分大小写，即 `MyDF` 和 `mydf` 指的是同一个数据工厂。 |<ul><li>一个数据工厂绑定到一个 Azure 订阅。</li><li>对象名称必须以字母或数字开头，并且只能包含字母、数字和短划线 (-) 字符。</li><li>每个短划线 (-) 字符的前后必须紧接字母或数字。 容器名称中不允许使用连续短划线。</li><li>名称长度为 3-63 个字符。</li></ul> |
| 链接服务/表/管道 |在数据工厂中唯一。 名称不区分大小写。 |<ul><li>表名称的最大字符数：260。</li><li>对象名称必须以字母、数字或下划线 (_) 开头。</li><li>不允许使用以下字符：“.”、“+”、“?”、“/”、“<”、“>”、“ * ”、“%”、“&”、“:”、“\\”</li><li>短划线 ("-") 不可用于链接服务的名称，仅可用于数据集名称。</li></ul>  |
| 资源组 |在 Microsoft Azure 内唯一。 名称不区分大小写。 | 有关详细信息，请参阅 [Azure 命名规则和限制](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions)。 |

## <a name="next-steps"></a>后续步骤
了解如何按照[快速入门：创建数据工厂](quickstart-create-data-factory-powershell.md)一文中的分步说明创建数据工厂。 
