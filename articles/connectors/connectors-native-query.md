---
title: 在逻辑应用中添加查询操作 | Microsoft Docs
description: 用于执行筛选数组等操作的查询操作的概述。
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 34e702c7-f9e5-4885-9266-fc7404adecfe
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2016
ms.author: jehollan
ms.openlocfilehash: 05dd4ae3c4ee439d66401a3f5595f9104051f8ee
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
ms.locfileid: "27962639"
---
# <a name="get-started-with-the-query-action"></a>查询操作入门
通过使用查询操作，可使用批处理和数组完成工作流，以便：

* 为来自数据库的所有高优先级记录创建任务。
* 将电子邮件的所有 PDF 附件保存到 Azure blob 中。

若要开始在逻辑应用中使用查询操作，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="use-the-query-action"></a>使用查询操作
操作是由在逻辑应用中定义的工作流执行的操作。 [了解有关操作的详细信息](connectors-overview.md)。  

查询操作当前具有一个在设计器中公开的操作，称为筛选数组。 这允许查询数组并返回一组已筛选的结果。

下面是在逻辑应用中添加它的方法：

1. 选择“新步骤”按钮。
2. 选择“添加操作”。
3. 在操作搜索框中，键入“筛选”列出“筛选数组”操作。
   
    ![选择查询操作](./media/connectors-native-query/using-action-1.png)
4. 选择要筛选的数组。 （以下屏幕截图显示来自 Twitter 搜索的结果数组。）
5. 在每个项目上创建要评估的条件。 （以下屏幕截图筛选来自具有多于 100 个关注者的用户的推文）。
   
    ![完成查询操作](./media/connectors-native-query/using-action-2.png)
   
    此操作将输出仅包含满足筛选要求的结果的新数组。
6. 单击工具栏左上角保存，逻辑应用将保存并发布（激活）。

\*如果调用 HTTP 终结点并接收 JSON 响应，请使用 _Parse JSON_ 操作分析 JSON 响应。 如果不执行此步骤，_Filter Array_ 将仅看到正文而不了解 JSON 有效负载的结构。

## <a name="query-action"></a>Query 操作
下面是此连接器支持的操作的详细信息。 连接器具有一个可能的操作。

| 操作 | 说明 |
| --- | --- |
| 筛选数组 |为数组中的每个项目评估条件并返回结果 |

## <a name="action-details"></a>操作详细信息
查询条件附带一个可能的操作。 下表介绍操作的必填和选填输入字段以及与使用该操作相关联的对应输出详细信息。

### <a name="filter-array"></a>筛选数组
下面是操作的输入字段，该操作发出 HTTP 出站请求。
* 表示它是必填字段。

| 显示名称 | 属性名称 | 说明 |
| --- | --- | --- |
| 来自* |from |要筛选的数组 |
| 条件* |其中 |要为每个项目评估的条件 |

<br>

### <a name="output-details"></a>输出详细信息
下面是 HTTP 响应的输出详细信息。

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| 已筛选的数组 |数组 |为每个已筛选的结果包含一个对象的数组 |

## <a name="next-steps"></a>后续步骤
现在，试用平台并[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 通过查看 [API 列表](apis-list.md)了解逻辑应用中的其他可用连接器。

