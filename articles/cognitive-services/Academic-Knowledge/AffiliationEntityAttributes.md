---
title: 学术知识 API 中的隶属关系实体属性
titlesuffix: Azure Cognitive Services
description: 了解可与学术知识 API 中的隶属关系实体结合使用的属性。
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: 52dcccebe1a1cbab7a6afadeb6b543b34b8b1eb7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143931"
---
# <a name="affiliation-entity"></a>隶属关系实体

> [!NOTE]
> 以下属性特定于隶属关系实体。 （Ty = ' 5 '）

名称 | 说明 | 类型 | 操作
--- | --- | --- | ---
AfN | 隶属关系规范化名称 |String |等于
CC | 隶属关系引文总计数 |Int32        |无  
DAfN | 隶属关系显示名称 |String |无
E | 扩展元数据</br></br>**重要说明**：此属性已弃用，并且仅适用于旧版应用程序。 单独请求此属性（即 attribute = Id、Ti、E）将导致在*序列化的 JSON 字符串*中返回所有扩展的元数据属性</br></br>扩展的元数据中包含的所有属性现在作为顶级属性提供，并且可以按如下方式请求（即 attribute = Id、Ti、DOI>10.1145、IA） | [扩展](#extended) | 无
ECC | 隶属关系估计引文总计数 |Int32 |无
ID | 实体 ID |Int64 |等于
PC | 与此实体隶属的已写入发布总数 | Int32 | 无

## <a name="extended"></a>扩展

> [!IMPORTANT]
> 此属性已弃用，并且仅适用于旧版应用程序。 单独请求此属性（即 attribute = Id、Ti、E）将导致在*序列化的 JSON 字符串*中返回所有扩展的元数据属性</br></br>扩展的元数据中包含的所有属性现在作为顶级属性提供，并且可以按如下方式请求（即 attribute = Id、Ti、DOI>10.1145、IA）

> [!IMPORTANT]
> 支持使用 "E" 请求单独的扩展属性。 作用域，即 "E. DN" 即将弃用。 虽然在技术上仍支持，但使用 "E" 请求单独的扩展属性。 范围将导致在 JSON 响应中的两个位置返回属性值，作为 "E" 对象的一部分，并作为顶级属性返回。

名称 | 说明 | 类型 | 操作
--- | --- | --- | ---
PC | 与此实体隶属的已写入发布总数 | Int32 | 无
