---
title: 作者实体属性 - 学术知识 API
titlesuffix: Azure Cognitive Services
description: 了解可与学术知识 API 中的作者实体结合使用的属性。
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: d5fc770c380397f605f8810fa41d3a8907f2358e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146487"
---
# <a name="author-entity"></a>作者实体

> [!NOTE]
> 以下属性特定于 author 实体。 （Ty = ' 1 '）

名称 | 说明 | 类型 | 操作
--- | --- | --- | ---
ID      | 实体 ID                             |Int64      |等于
AuN     | 作者规范化名称                    |String     |等于
CC      | 作者引文总计数           |Int32      |无  
DAuN    | 作者显示名称                   |String     |无
E | 扩展元数据</br></br>**重要说明**：此属性已弃用，并且仅适用于旧版应用程序。 单独请求此属性（即 attribute = Id、Ti、E）将导致在*序列化的 JSON 字符串*中返回所有扩展的元数据属性</br></br>扩展的元数据中包含的所有属性现在作为顶级属性提供，并且可以按如下方式请求（即 attribute = Id、Ti、DOI>10.1145、IA） | [扩展](#extended) | 无
ECC     | 作者估计引文总计数 |Int32      |无
LKA.AfId | 为作者找到的最新已知隶属关系的实体 ID | Int64 | 无
LKA.AfN | 为作者找到的最新已知隶属关系的规范化名称 | String | 无
PC | 作者总数总计 | Int32 | 无

## <a name="extended"></a>扩展

> [!IMPORTANT]
> 此属性已弃用，并且仅适用于旧版应用程序。 单独请求此属性（即 attribute = Id、Ti、E）将导致在*序列化的 JSON 字符串*中返回所有扩展的元数据属性</br></br>扩展的元数据中包含的所有属性现在作为顶级属性提供，并且可以按如下方式请求（即 attribute = Id、Ti、DOI>10.1145、IA）

> [!IMPORTANT]
> 支持使用 "E" 请求单独的扩展属性。 作用域，即 "E. DN" 即将弃用。 虽然在技术上仍支持，但使用 "E" 请求单独的扩展属性。 范围将导致在 JSON 响应中的两个位置返回属性值，作为 "E" 对象的一部分，并作为顶级属性返回。

名称 | 说明 | 类型 | 操作
--- | --- | --- | ---
LKA.AfId | 为作者找到的最新已知隶属关系的实体 ID | Int64 | 无
LKA.AfN | 为作者找到的最新已知隶属关系的规范化名称 | String | 无
PC | 作者总数总计 | Int32 | 无
