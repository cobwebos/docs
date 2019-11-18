---
title: 论文实体属性 - 学术知识 API
titlesuffix: Azure Cognitive Services
description: 了解可与学术知识 API 中的论文实体结合使用的属性。
services: cognitive-services
author: DarrinEide
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: fc3bb5987c31fe718951a3cae02ed7c4ddc29957
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123081"
---
# <a name="paper-entity"></a>论文实体

> [!NOTE]
> 以下属性是特定于纸张实体的。 （Ty = ' 0 '）

名称 | 说明 | 类型 | 操作
--- | --- | --- | ---
AA.AfId | 作者单位 ID | Int64 | 等于
AA.AfN | 作者单位名称 | String | 等于、StartsWith
AA.AuId | 作者 ID | Int64 | 等于
AA.AuN | 规范化的作者姓名 | String | 等于、StartsWith
后面.DAuN | 原始作者姓名 | String | 无
后面.DAfN | 原始隶属关系名称 | String | 无
AA.S | 作者列表中的数字位置 | Int32 | 等于
BT | BibTex 文档类型（"a"：杂志文章，"b"：书籍，"c"：书本章节，"p"：会议室） | String | 无
BV | BibTex 场地名称 | String | 无
C.CId | 会议系列 ID | Int64 | 等于
C.CN | 会议系列名称 | String | 等于、StartsWith
CC | 引文计数 | Int32 | 无  
CitCon | 引文上下文</br></br>所引用的纸张 ID 的列表以及纸张中的相应上下文（例如 [{123： ["棕色 foxes，对于在纸张123中引用的跳跃是已知的），惰性狗是历史 misnomer，如纸张 123"]} 中所示。 | “自定义” | 无
D | 发布日期（yyyy-mm-dd 格式） | 日期 | 等于、IsBetween
DN | 原始纸张标题 | String | 无
DOI | 数字对象标识符 | String | 等于、StartsWith
E | 扩展元数据</br></br>**重要说明**：此属性已弃用，并且仅适用于旧版应用程序。 单独请求此属性（即 attribute = Id、Ti、E）将导致在*序列化的 JSON 字符串*中返回所有扩展的元数据属性</br></br>扩展的元数据中包含的所有属性现在作为顶级属性提供，并且可以按如下方式请求（即 attribute = Id、Ti、DOI>10.1145、IA） | [扩展](#extended) | 无
ECC | 估计的引文计数 | Int32 | 无
F. DFN | 研究名称的原始字段 | String | 无
F.FId | 研究领域 ID | Int64 | 等于
F.FN | 研究名称的规范化字段 | String | 等于、StartsWith
FP | 发布中的第一页纸 | String | 等于
I | 发布问题 | String | 等于
IA | 反转抽象 | [InvertedAbstract](#invertedabstract) | 无
ID | 纸张 ID | Int64 | 等于
J.JId | 期刊 ID | Int64 | 等于
J.JN | 期刊名称 | String | 等于、StartsWith
LP | 发布的纸张的最后一页 | String | 等于
PB | 发布者 | String | 无
五 | 发布类型（0：未知，1：杂志文章，2：专利，3：会议纸张，4：书籍章节，5：书籍，6：书籍参考条目，7：数据集，8：存储库 | String | 等于
RId | 引用的纸张 Id 列表 | Int64[] | 等于
S | 纸张的源 Url 列表，按相关性排序 | [源](#source)[] | 无
Ti | 规范化标题 | String | 等于、StartsWith
V | 发布卷 | String | 等于
VFN | 日志或会议地点的全名 | String | 无
VSN | 日志或会议地点的短名称 | String | 无
W | 标题中的唯一单词 | String[] | 等于
Y | 已发布年份 | Int32 | 等于、IsBetween

## <a name="extended"></a>扩展
> [!IMPORTANT]
> 此属性已弃用，并且仅适用于旧版应用程序。 单独请求此属性（即 attribute = Id、Ti、E）将导致在*序列化的 JSON 字符串*中返回所有扩展的元数据属性</br></br>扩展的元数据中包含的所有属性现在作为顶级属性提供，并且可以按如下方式请求（即 attribute = Id、Ti、DOI>10.1145、IA）

> [!IMPORTANT]
> 支持使用 "E" 请求单独的扩展属性。 作用域，即 "E. DN" 即将弃用。 虽然在技术上仍支持，但使用 "E" 请求单独的扩展属性。 范围将导致在 JSON 响应中的两个位置返回属性值，作为 "E" 对象的一部分，并作为顶级属性返回。

名称 | 说明 | 类型 | 操作
--- | --- | --- | ---
BT | BibTex 文档类型（"a"：杂志文章，"b"：书籍，"c"：书本章节，"p"：会议室） | String | 无
BV | BibTex 场地名称 | String | 无
CC | 引文上下文</br></br>所引用的纸张 ID 的列表以及纸张中的相应上下文（例如 [{123： ["棕色 foxes，对于在纸张123中引用的跳跃是已知的），惰性狗是历史 misnomer，如纸张 123"]} 中所示。 | “自定义” | 无
DN | 原始纸张标题 | String | 无
DOI | 数字对象标识符 | String | 无
FP | 发布中的第一页纸 | String | 无
I | 发布问题 | String | 无
IA | 倒排的摘要 | [InvertedAbstract](#invertedabstract) | 无
LP | 发布的纸张的最后一页 | String | 无
PB | 发布者 | String | 无
S | 纸张的源 Url 列表，按相关性排序 | [源](#source)[] | 无
V | 发布卷 | String | 无
VFN | 日志或会议地点的全名 | String | 无
VSN | 日志或会议地点的短名称 | String | 无

## <a name="invertedabstract"></a>InvertedAbstract

> [!IMPORTANT]
> 不能将 InvertedAbstract 特性直接作为返回特性来请求。 如果需要单个属性，则必须请求顶层 "IA" 属性，即获取 IA。IndexLength 请求属性 = IA

名称 | 说明 | 类型 | 操作
--- | --- | --- | ---
IndexLength | 索引中的项数（摘要的单词计数） | Int32 | 无
InvertedIndex | 抽象词及其在原始抽象中的相应位置列表（例如 [{"： [0，15，30]}，{" 棕色 "： [1]}，{" fox "： [2]}]） | “自定义” | 无

## <a name="source"></a>Source

> [!IMPORTANT]
> 不能将源特性作为返回特性直接请求。 如果需要单个属性，则必须请求顶层 "S" 属性，即获取 U 请求属性 = S

名称 | 说明 | 类型 | 操作
--- | --- | --- | ---
Ty | 源 URL 类型（1： HTML、2：文本、3： PDF、4： DOC、5： PPT、6： XLS、7： PS） | String | 无
U | 来源 URL | String | 无
