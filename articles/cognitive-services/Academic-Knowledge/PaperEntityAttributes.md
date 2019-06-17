---
title: 论文实体属性 - 学术知识 API
titlesuffix: Azure Cognitive Services
description: 了解可与学术知识 API 中的论文实体结合使用的属性。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 92844b5faf691b67617c9f3424a1322aa05429bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64875736"
---
# <a name="paper-entity"></a>论文实体

<sub> *以下属性特定于论文实体。(Ty = '0') </sub>


名称    |描述                                        |Type       | 操作
------- | ------------------------------------------------- | --------- | ----------------------------
Id      |实体 ID                                          |Int64      |等于
Ti      |论文标题                                        |String     |Equals、<br/>-StartsWith
L       |纸张语言代码分隔"\@\@\@"          |String     |等于
Y       |论文年份                                         |Int32      |Equals、<br/>IsBetween
D       |论文日期                                         |Date       |Equals、<br/>IsBetween
CC      |引文计数                                     |Int32      |无  
ECC     |估计的引文计数                           |Int32      |无
AA.AuN  |作者姓名                                        |String     |Equals、<br/>-StartsWith
AA.AuId |作者 ID                                          |Int64      |等于
AA.AfN  |作者单位名称                            |String     |Equals、<br/>-StartsWith
AA.AfId |作者单位 ID                              |Int64      |等于
AA.S    |论文的作者顺序                         |Int32      |等于
F.FN    |研究领域名称                                |String     |Equals、<br/>-StartsWith
F.FId   |研究领域 ID                                  |Int64      |等于
J.JN    |期刊名称                                       |String     |Equals、<br/>-StartsWith
J.JId   |期刊 ID                                         |Int64      |等于
C.CN    |会议系列名称                             |String     |Equals、<br/>-StartsWith
C.CId   |会议系列 ID                               |Int64      |等于
RId     |参考的论文的 ID                              |Int64[]    |等于
W       |论文标题和摘要中的单词                |String[]   |等于
E       |扩展的元数据（请参阅下表）                |String     |无  
        


## <a name="extended-metadata-attributes"></a>扩展的元数据属性 ##

名称    | 描述               
--------|---------------------------    
DN      | 论文的显示名称 
S       | 来源 - 论文的 Web 来源列表，按静态排名排序
S.Ty    | 来源类型（1:HTML、2:Text、3:PDF、4:DOC、5:PPT、6:XLS、7:PS）
S.U     | 来源 URL
VFN     | 场馆全名 - 期刊或会议的全名
VSN     | 场馆短名称 - 期刊或会议的短名称
V       | 卷 - 期刊卷
BV      | 期刊名称
BT      | 
PB      | 日记本缩写
I       | 刊号 - 期刊刊号
FP      | FirstPage - 论文第一页
LP      | LastPage - 论文最后一页
DOI     | 数字对象标识符
CC      | 引文上下文 – 引用的论文 ID 的列表和论文中的对应上下文（例如 [{123:[“brown foxes are known for jumping as referenced in paper 123”, “the lazy dogs are a historical misnomer as shown in paper 123”]}）
IA      | 倒排的摘要
IA.IndexLength| 索引中的项数（摘要的单词计数）
IA.InvertedIndex| 摘要单词的列表及其在原始摘要中的对应位置（例如. [{“the”:[0, 15, 30]}, {“brown”:[1]}, {“fox”:[2]}]）
