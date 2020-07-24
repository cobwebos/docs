---
author: baanders
description: Azure 数字孪生限制的包含文件
ms.service: digital-twins
ms.topic: include
ms.date: 6/9/2020
ms.author: baanders
ms.openlocfilehash: 3035bd71a91f7cad6fb951d74081b77d8445a81f
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133778"
---
### <a name="functional-limits"></a>功能限制

下表列出了 Azure 数字孪生在当前预览版中的功能限制。

| 区域 | 功能 | 默认限制 | 可调? |
| --- | --- | --- | --- |
| Azure 资源 | 区域中每个订阅的 Azure 数字孪生实例数 | 10 | 是 |
| 数字孪生 | Azure 数字孪生实例中的孪生数 | 200,000 | 是 |
| 路由 | 单个 Azure 数字孪生实例的终结点数 | 6 | 否 |
| 路由 | 单个 Azure 数字孪生实例的路由数 | 6 | 是 |
| 模型 | 单个 Azure 数字孪生实例内的模型数 | 10,000 | 是 |
| 模型 | 可在单个 API 调用中上载的模型数 | 250 | 否 |
| 模型 | 在单个页面中返回的项目数 | 100 | 否 |
| 查询 | 在单个页面中返回的项目数 | 100 | 否 |
| 查询 | `AND`  /  `OR` 查询中的表达式数 | 50 | 是 |
| 查询 | 子句中的数组项数 `IN`  /  `NOT IN` | 50 | 是 |
| 查询 | 查询中的字符数 | 8,000 | 是 |
| 查询 | `JOINS`查询中的数目 | 1 | 是 |

### <a name="rate-limits"></a>速率限制

此表反映了不同 Api 的速率限制。

| API | 功能 | 默认限制 | 可调? |
| --- | --- | --- | --- |
| 模型 API | 每秒的请求数 | 100 | 是 |
| 数字孪生 API | 每秒的请求数 | 1,000 | 是 |
| 查询 API | 每秒的请求数 | 500 | 是 |
| 查询 API | 查询单位/秒 | 4,000 | 是 |
| 事件路由 API | 每秒的请求数 | 100 | 是 |

### <a name="other-limits"></a>其他限制

可在 GitHub 中的规范文档中找到有关 Azure 数字孪生模型的 DTDL 文档中的数据类型和字段的限制：[*数字孪生定义语言（DTDL）-版本 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)。
 
在预览期间，查询延迟的详细信息和其他有关编写查询的准则可在[*如何：查询克隆图形*](../articles/digital-twins/how-to-query-graph.md)中找到。