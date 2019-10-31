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
ms.date: 10/22/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: 1d16668e2c0f52c0824016c977251e64c800c54d
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161727"
---
# <a name="paper-entity"></a>论文实体

<sub>* 以下属性特定于纸张实体。（Ty = ' 0 '）</sub>

名称 | 描述 | Type | Operations
--- | --- | --- | ---
AA.AfId | 作者单位 ID | Int64 | 等于
AA.AfN | 作者单位名称 | 字符串 | 等于、StartsWith
AA.AuId | 作者 ID | Int64 | 等于
AA.AuN | 规范化的作者姓名 | 字符串 | 等于、StartsWith
后面.DAuN | 原始作者姓名 | 字符串 | None
后面.DAfN | 原始隶属关系名称 | 字符串 | None
AA.S | 作者列表中的数字位置 | Int32 | 等于
CC | 引文计数 | Int32 | None  
C.CId | 会议系列 ID | Int64 | 等于
C.CN | 会议系列名称 | 字符串 | 等于、StartsWith
D | 发布日期（yyyy-mm-dd 格式） | 日期 | 等于、IsBetween
E | 扩展的元数据（请参阅下表） | 字符串 | N/A  
ECC | 估计的引文计数 | Int32 | None
F. DFN | 研究名称的原始字段 | 字符串 | None
F.FId | 研究领域 ID | Int64 | 等于
F.FN | 研究名称的规范化字段 | 字符串 | 等于、StartsWith
ID | 纸张 ID | Int64 | 等于
J.JId | 期刊 ID | Int64 | 等于
J.JN | 期刊名称 | 字符串 | 等于、StartsWith
五 | 发布类型（0：未知，1：杂志文章，2：专利，3：会议纸张，4：书籍章节，5：书籍，6：书籍参考条目，7：数据集，8：存储库 | 字符串 | 等于
RId | 引用的纸张 Id 列表 | Int64[] | 等于
Ti | 规范化标题 | 字符串 | 等于、StartsWith
W | 标题中的唯一单词 | String[] | 等于
Y | 已发布年份 | Int32 | 等于、IsBetween

## <a name="extended-metadata-attributes"></a>扩展的元数据属性 ##

名称 | 描述               
--- | ---
BT | BibTex 文档类型（"a"：杂志文章，"b"：书籍，"c"：书本章节，"p"：会议室）
BV | BibTex 场地名称
CC | 引文上下文–所引用的纸张 ID 的列表以及纸张中的相应上下文（例如 [{123： ["棕色 foxes 对于在纸张 123" 中所引用的跳跃识别），惰性狗是历史 misnomer，如纸张 123 "]} 中所示。
DN | 原始纸张标题
DOI | 数字对象标识符
FP | 发布中的第一页纸
I | 发布问题
IA | 倒排的摘要
IA.IndexLength | 索引中的项数（摘要的单词计数）
IA.InvertedIndex | 抽象词及其在原始抽象中的相应位置列表（例如 [{"： [0，15，30]}，{" 棕色 "： [1]}，{" fox "： [2]}]）
LP | 发布的纸张的最后一页
PB | 发布者
S | 来源 - 论文的 Web 来源列表，按静态排名排序
S.Ty | 来源类型（1:HTML、2:Text、3:PDF、4:DOC、5:PPT、6:XLS、7:PS）
S.U | 来源 URL
V | 发布卷
VFN | 日志或会议地点的全名
VSN | 日志或会议地点的短名称
