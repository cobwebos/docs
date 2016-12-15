---
title: "了解如何在逻辑应用中使用 Salesforce 连接器 | Microsoft Docs"
description: "使用 Azure App Service 创建逻辑应用。 Salesforce Connector 提供使用 Salesforce 对象的 API。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/05/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f2f9a4d182527d2772e78d78a0c10bd94742d0fe


---
# <a name="get-started-with-the-salesforce-connector"></a>Salesforce 连接器入门
Salesforce Connector 提供使用 Salesforce 对象的 API。

若要使用“任何连接器”[](apis-list.md)，首先需要创建逻辑应用。 可通过“立即创建逻辑应用”[](../app-service-logic/app-service-logic-create-a-logic-app.md)开始操作。

## <a name="connect-to-salesforce-connector"></a>连接到 Salesforce 连接器
需要先创建到任何服务的*连接*，然后逻辑应用才能访问该服务。 [连接](connectors-overview.md)提供逻辑应用和其他服务之间的连接性。  

### <a name="create-a-connection-to-salesforce-connector"></a>创建到 Salesforce 连接器的连接
> [!INCLUDE [Steps to create a connection to Salesforce Connector](../../includes/connectors-create-api-salesforce.md)]
> 
> 

## <a name="use-a-salesforce-connector-trigger"></a>使用 Salesforce 连接器触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 [了解有关触发器的详细信息](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

> [!INCLUDE [Steps to create a Salesforce trigger](../../includes/connectors-create-api-salesforce-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>添加条件
> [!INCLUDE [Steps to create a Salesforce condition](../../includes/connectors-create-api-salesforce-condition.md)]
> 
> 

## <a name="use-a-salesforce-connector-action"></a>使用 Salesforce 连接器操作
操作是指在逻辑应用中定义的由工作流执行的操作。 [了解有关操作的详细信息](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

> [!INCLUDE [Steps to create a Salesforce action](../../includes/connectors-create-api-salesforce-action.md)]
> 
> 

## <a name="technical-details"></a>技术详细信息
下面详细介绍了此连接支持的触发器、操作和响应：

## <a name="salesforce-connector-triggers"></a>Salesforce 连接器触发器
Salesforce 连接器具有以下触发器：  

| 触发器 | 说明 |
| --- | --- |
| [创建对象时](connectors-create-api-salesforce.md#when-an-object-is-created) |此操作在创建对象时触发流。 |
| [修改对象时](connectors-create-api-salesforce.md#when-an-object-is-modified) |此操作在修改对象时触发流。 |

## <a name="salesforce-connector-actions"></a>Salesforce 连接器操作
Salesforce 连接器具有以下操作：

| 操作 | 说明 |
| --- | --- |
| [获取对象](connectors-create-api-salesforce.md#get-objects) |此操作获取特定对象类型（如“潜在顾客”）的对象。 |
| [创建对象](connectors-create-api-salesforce.md#create-object) |此操作创建对象。 |
| [获取对象](connectors-create-api-salesforce.md#get-object) |此操作获取对象。 |
| [删除对象](connectors-create-api-salesforce.md#delete-object) |此操作删除对象。 |
| [更新对象](connectors-create-api-salesforce.md#update-object) |此操作更新对象。 |
| [获取对象类型](connectors-create-api-salesforce.md#get-object-types) |此操作列出所有可用对象类型。 |

### <a name="action-details"></a>操作详细信息
下面详细介绍了此连接器的操作和触发器及其响应：

### <a name="get-objects"></a>获取对象
此操作获取特定对象类型（如“潜在顾客”）的对象。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |对象类型 |“潜在顾客”等 Salesforce 对象类型 |
| $filter |筛选查询 |要限制项数的 ODATA 筛选查询 |
| $orderby |排序依据 |用于指定项顺序的 ODATA orderBy 查询 |
| $skip |跳过计数 |要跳过的项数（默认值 = 0） |
| $top |最大获取计数 |要检索的最大项数（默认值 = 256） |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
ItemsList

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

### <a name="create-object"></a>创建对象
此操作创建对象。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |对象类型 |“潜在顾客”等对象类型 |
| item* |对象 |要创建的对象 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
项目

| 属性名称 | 数据类型 |
| --- | --- |
| ItemInternalId |字符串 |

### <a name="get-object"></a>获取对象
此操作获取对象。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |对象类型 |“潜在顾客”等 Salesforce 对象类型 |
| id* |对象 id |要获取的对象的标识符 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
项目

| 属性名称 | 数据类型 |
| --- | --- |
| ItemInternalId |字符串 |

### <a name="delete-object"></a>删除对象
此操作删除对象。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |对象类型 |“潜在顾客”等对象类型 |
| id* |对象 id |要删除的对象的标识符 |

* 指示属性是必需的

### <a name="update-object"></a>更新对象
此操作更新对象。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |对象类型 |“潜在顾客”等对象类型 |
| id* |对象 id |要更新的对象的标识符 |
| item* |对象 |已更新属性的对象 |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
项目

| 属性名称 | 数据类型 |
| --- | --- |
| ItemInternalId |字符串 |

### <a name="when-an-object-is-created"></a>创建对象时
此操作在创建对象时触发流。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |对象类型 |“潜在顾客”等对象类型 |
| $filter |筛选查询 |要限制项数的 ODATA 筛选查询 |
| $orderby |排序依据 |用于指定项顺序的 ODATA orderBy 查询 |
| $skip |跳过计数 |要跳过的项数（默认值 = 0） |
| $top |最大获取计数 |要检索的最大项数（默认值 = 256） |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
ItemsList

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

### <a name="when-an-object-is-modified"></a>修改对象时
此操作在修改对象时触发流。 

| 属性名称 | 显示名称 | 说明 |
| --- | --- | --- |
| table* |对象类型 |“潜在顾客”等对象类型 |
| $filter |筛选查询 |要限制项数的 ODATA 筛选查询 |
| $orderby |排序依据 |用于指定项顺序的 ODATA orderBy 查询 |
| $skip |跳过计数 |要跳过的项数（默认值 = 0） |
| $top |最大获取计数 |要检索的最大项数（默认值 = 256） |

* 指示属性是必需的

#### <a name="output-details"></a>输出详细信息
ItemsList

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

### <a name="get-object-types"></a>获取对象类型
此操作列出所有可用对象类型。 

此调用没有任何参数

#### <a name="output-details"></a>输出详细信息
TablesList

| 属性名称 | 数据类型 |
| --- | --- |
| value |数组 |

## <a name="http-responses"></a>HTTP 响应
上述操作和触发器可以返回以下一个或多个 HTTP 状态代码： 

| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 202 |已接受 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误。 |
| default |操作失败。 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


