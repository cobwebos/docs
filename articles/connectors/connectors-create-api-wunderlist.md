---
title: "奇妙清单 | Microsoft Docs"
description: "使用 Azure App Service 创建逻辑应用。 Wunderlist 提供待办事项列表和任务管理器，帮助人们完成他们的事务。  无论是与爱人共享购物单、处理某个项目还是计划度假，Wunderlist 都可使捕获、共享和完成待办事项更轻松。 Wunderlist 在手机、平板电脑和计算机之间即时同步，以便你从任意位置访问所有任务。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 084ac505003e926e9c4b75352ec96a77f8c3584a


---
# <a name="get-started-with-the-wunderlist-connector"></a>Wunderlist 连接器入门
Wunderlist 提供待办事项列表和任务管理器，帮助人们完成他们的事务。  无论是与爱人共享购物单、处理某个项目还是计划度假，Wunderlist 都可使捕获、共享和完成待办事项更轻松。 Wunderlist 在手机、平板电脑和计算机之间即时同步，以便你从任意位置访问所有任务。

> [!NOTE]
> 此文章版本适用于 2015 年 8 月 1 日预览版架构的逻辑应用。
> 
> 

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>触发器和操作
Wunderlist 连接器可用作操作；它具有触发器。 所有连接器都支持采用 JSON 和 XML 格式的数据。

 Wunderlist 连接器具有以下可用操作和/或触发器：

### <a name="wunderlist-actions"></a>Wunderlist 操作
可执行以下操作：

| 操作 | 说明 |
| --- | --- |
| [RetrieveLists](connectors-create-api-wunderlist.md#retrievelists) |检索与帐户关联的列表。 |
| [CreateList](connectors-create-api-wunderlist.md#createlist) |创建列表。 |
| [ListTasks](connectors-create-api-wunderlist.md#listtasks) |从特定列表中检索任务。 |
| [CreateTask](connectors-create-api-wunderlist.md#createtask) |创建任务 |
| [ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks) |从特定列表或特定任务中检索子任务。 |
| [CreateSubTask](connectors-create-api-wunderlist.md#createsubtask) |在特定任务内创建子任务 |
| [ListNotes](connectors-create-api-wunderlist.md#listnotes) |检索特定列表或特定任务的备注。 |
| [CreateNote](connectors-create-api-wunderlist.md#createnote) |向特定任务添加备注 |
| [ListComments](connectors-create-api-wunderlist.md#listcomments) |检索特定列表或特定任务的任务评论。 |
| [CreateComment](connectors-create-api-wunderlist.md#createcomment) |向特定任务添加评论 |
| [RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders) |检索特定列表或特定任务的提醒。 |
| [CreateReminder](connectors-create-api-wunderlist.md#createreminder) |设置提醒。 |
| [RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles) |检索特定列表或特定任务的文件。 |
| [GetList](connectors-create-api-wunderlist.md#getlist) |检索特定列表 |
| [DeleteList](connectors-create-api-wunderlist.md#deletelist) |删除列表 |
| [UpdateList](connectors-create-api-wunderlist.md#updatelist) |更新特定列表 |
| [GetTask](connectors-create-api-wunderlist.md#gettask) |检索特定任务 |
| [UpdateTask](connectors-create-api-wunderlist.md#updatetask) |更新特定任务 |
| [DeleteTask](connectors-create-api-wunderlist.md#deletetask) |删除特定任务 |
| [GetSubTask](connectors-create-api-wunderlist.md#getsubtask) |检索特定子任务 |
| [UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask) |更新特定子任务 |
| [DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask) |删除特定子任务 |
| [GetNote](connectors-create-api-wunderlist.md#getnote) |检索特定备注 |
| [UpdateNote](connectors-create-api-wunderlist.md#updatenote) |更新特定备注 |
| [DeleteNote](connectors-create-api-wunderlist.md#deletenote) |删除特定备注 |
| [GetComment](connectors-create-api-wunderlist.md#getcomment) |检索特定任务评论 |
| [UpdateReminder](connectors-create-api-wunderlist.md#updatereminder) |更新特定提醒 |
| [DeleteReminder](connectors-create-api-wunderlist.md#deletereminder) |删除特定提醒 |

### <a name="wunderlist-triggers"></a>Wunderlist 触发器
可侦听以下事件：

| 触发器 | 说明 |
| --- | --- |
| 任务截止时 |列表中的任务截止时触发新流 |
| 新建任务时 |在列表中新建任务时触发新流 |
| 发生提醒时 |发生提醒时触发新流 |

## <a name="create-a-connection-to-wunderlist"></a>创建到 Wunderlist 的连接
若要使用 Wunderlist 创建逻辑应用，必须先创建**连接**，然后提供以下属性的详细信息：

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 令牌 |是 |提供 Wunderlist 凭据 |

创建连接后，可使用它执行操作并侦听触发器，如本文所述。

> [!INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)]
> 
> [!TIP]
> 可在其他逻辑应用中使用此连接。
> 
> 

## <a name="reference-for-wunderlist"></a>Wunderlist 的参考
适用于版本：1.0

## <a name="triggertaskdue"></a>TriggerTaskDue
任务截止时：列表中的任务截止时触发新流

```GET: /trigger/tasksdue```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |是 |查询 |无 |列表 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |

## <a name="triggertasknew"></a>TriggerTaskNew
新建任务时：在列表中新建任务时触发新流

```GET: /trigger/tasksnew```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |是 |查询 |无 |列表 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |

## <a name="triggerreminder"></a>TriggerReminder
发生提醒时：发生提醒时触发新流

```GET: /trigger/reminders```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |是 |查询 |无 |列表 ID |
| task_id |integer |否 |查询 |无 |任务 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |

## <a name="retrievelists"></a>RetrieveLists
获取列表：检索与帐户关联的列表。

```GET: /lists```

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |错误的请求 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="createlist"></a>CreateList
创建列表：创建列表。

```POST: /lists```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 发布 | |是 |body |无 |要创建的新列表 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| default |操作失败。 |

## <a name="listtasks"></a>ListTasks
获取任务：从特定列表中检索任务。

```GET: /tasks```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |是 |查询 |无 |列表 ID |
| 已完成 |布尔值 |否 |查询 |无 |已完成 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |错误的请求 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="createtask"></a>CreateTask
创建任务：创建任务

```POST: /tasks```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 发布 | |是 |body |无 |要创建的新任务 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 201 |已创建 |

## <a name="listsubtasks"></a>ListSubTasks
获取子任务：从特定列表或特定任务中检索子任务。

```GET: /subtasks```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |是 |查询 |无 |列表 ID |
| task_id |integer |否 |查询 |无 |任务 ID |
| 已完成 |布尔值 |否 |查询 |无 |已完成 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |错误的请求 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="createsubtask"></a>CreateSubTask
创建子任务：在特定任务内创建子任务

```POST: /subtasks```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 发布 | |是 |body |无 |要创建的新子任务 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 201 |已创建 |

## <a name="listnotes"></a>ListNotes
获取备注：检索特定列表或特定任务的备注。

```GET: /notes```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |是 |查询 |无 |列表 ID |
| task_id |integer |否 |查询 |无 |任务 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |错误的请求 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="createnote"></a>CreateNote
创建备注：向特定任务添加备注

```POST: /notes```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 发布 | |是 |body |无 |要创建的新备注 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 201 |已创建 |

## <a name="listcomments"></a>ListComments
获取任务评论：检索特定列表或特定任务的任务评论。

```GET: /task_comments```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |是 |查询 |无 |列表 ID |
| task_id |integer |否 |查询 |无 |任务 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |错误的请求 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="createcomment"></a>CreateComment
向任务添加评论：向特定任务添加评论

```POST: /task_comments```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 发布 | |是 |body |无 |要创建的新任务评论 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 201 |已创建 |

## <a name="retrievereminders"></a>RetrieveReminders
获取提醒：检索特定列表或特定任务的提醒。

```GET: /reminders```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |是 |查询 |无 |列表 ID |
| task_id |integer |否 |查询 |无 |任务 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |错误的请求 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="createreminder"></a>CreateReminder
设置提醒：设置提醒。

```POST: /reminders```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| 发布 | |是 |body |无 |要创建的新提醒 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| default |操作失败。 |

## <a name="retrievefiles"></a>RetrieveFiles
获取文件：检索特定列表或特定任务的文件。

```GET: /files```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |是 |查询 |无 |列表 ID |
| task_id |integer |否 |查询 |无 |任务 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |操作成功 |
| 400 |错误的请求 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败。 |

## <a name="getlist"></a>GetList
获取列表：检索特定列表

```GET: /lists/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |字符串 |是 |路径 |无 |列表 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |

## <a name="deletelist"></a>DeleteList
删除列表：删除列表

```DELETE: /lists/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |是 |路径 |无 |列表 ID |
| revision |integer |是 |查询 |无 |修订 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 204 |无内容 |

## <a name="updatelist"></a>UpdateList
更新列表：更新特定列表

```PATCH: /lists/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |是 |路径 |无 |列表 ID |
| post | |是 |body |无 |列表详细信息 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |

## <a name="gettask"></a>GetTask
获取任务：检索特定任务

```GET: /tasks/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |是 |查询 |无 |列表 ID |
| id |integer |是 |路径 |无 |任务 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |

## <a name="updatetask"></a>UpdateTask
更新任务：更新特定任务

```PATCH: /tasks/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |是 |查询 |无 |列表 ID |
| id |integer |是 |路径 |无 |任务 ID |
| post | |是 |body |无 |任务详细信息 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |

## <a name="deletetask"></a>DeleteTask
删除任务：删除特定任务

```DELETE: /tasks/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| list_id |integer |是 |查询 |无 |列表 ID |
| id |integer |是 |路径 |无 |任务 ID |
| revision |integer |是 |查询 |无 |修订 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 204 |无内容 |

## <a name="getsubtask"></a>GetSubTask
获取子任务：检索特定子任务

```GET: /subtasks/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |字符串 |是 |路径 |无 |子任务 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |

## <a name="updatesubtask"></a>UpdateSubTask
更新子任务：更新特定子任务

```PATCH: /subtasks/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |是 |路径 |无 |子任务 ID |
| post | |是 |body |无 |子任务详细信息 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |

## <a name="deletesubtask"></a>DeleteSubTask
删除子任务：删除特定子任务

```DELETE: /subtasks/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |是 |路径 |无 |子任务 ID |
| revision |integer |是 |查询 |无 |修订 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 204 |无内容 |

## <a name="getnote"></a>GetNote
获取备注：检索特定备注

```GET: /notes/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |字符串 |是 |路径 |无 |备注 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |

## <a name="updatenote"></a>UpdateNote
更新备注：更新特定备注

```PATCH: /notes/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |是 |路径 |无 |备注 ID |
| post | |是 |body |无 |备注详细信息 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |

## <a name="deletenote"></a>DeleteNote
删除备注：删除特定备注

```DELETE: /notes/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |是 |路径 |无 |备注 ID |
| revision |integer |是 |查询 |无 |修订 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 204 |无内容 |

## <a name="getcomment"></a>GetComment
获取任务评论：检索特定任务评论

```GET: /task_comments/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |字符串 |是 |路径 |无 |评论 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |

## <a name="updatereminder"></a>UpdateReminder
更新提醒：更新特定提醒

```PATCH: /reminders/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |是 |路径 |无 |提醒 ID |
| post | |是 |body |无 |提醒详细信息 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |

## <a name="deletereminder"></a>DeleteReminder
删除提醒：删除特定提醒

```DELETE: /reminders/{id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| id |integer |是 |路径 |无 |提醒的 ID。 |
| revision |integer |是 |查询 |无 |修订 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 204 |无内容 |

## <a name="object-definitions"></a>对象定义
### <a name="list"></a>列出
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |integer |否 |
| created_at |字符串 |否 |
| title |字符串 |否 |
| list_type |字符串 |否 |
| type |字符串 |否 |
| revision |integer |否 |

### <a name="createdlist"></a>CreatedList
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |integer |否 |
| created_at |字符串 |否 |
| title |字符串 |否 |
| revision |integer |否 |
| type |字符串 |否 |

### <a name="task"></a>任务
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |integer |否 |
| assignee_id |integer |否 |
| assigner_id |integer |否 |
| created_at |字符串 |否 |
| created_by_id |integer |否 |
| due_date |字符串 |否 |
| list_id |integer |否 |
| revision |integer |否 |
| starred |布尔值 |否 |
| title |字符串 |否 |

### <a name="subtask"></a>子任务
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |integer |否 |
| task_id |integer |否 |
| created_at |字符串 |否 |
| created_by_id |integer |否 |
| revision |字符串 |否 |
| title |字符串 |否 |

### <a name="note"></a>注意
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |integer |否 |
| task_id |integer |否 |
| 内容 |字符串 |否 |
| created_at |字符串 |否 |
| updated_at |字符串 |否 |
| revision |integer |否 |

### <a name="comment"></a>注释
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |integer |否 |
| task_id |integer |否 |
| revision |integer |否 |
| text |字符串 |否 |
| type |字符串 |否 |
| created_at |字符串 |否 |

### <a name="reminder"></a>Reminder
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |integer |否 |
| 日期 |字符串 |否 |
| task_id |integer |否 |
| revision |integer |否 |
| type |字符串 |否 |
| created_at |字符串 |否 |
| updated_at |字符串 |否 |

### <a name="createdreminder"></a>CreatedReminder
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |integer |否 |
| 日期 |字符串 |否 |
| task_id |integer |否 |
| revision |integer |否 |
| created_at |字符串 |否 |
| updated_at |字符串 |否 |

### <a name="file"></a>文件
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |integer |否 |
| url |字符串 |否 |
| task_id |integer |否 |
| list_id |integer |否 |
| user_id |integer |否 |
| file_name |字符串 |否 |
| content_type |字符串 |否 |
| file_size |integer |否 |
| local_created_at |字符串 |否 |
| created_at |字符串 |否 |
| updated_at |字符串 |否 |
| type |字符串 |否 |
| revision |integer |否 |

### <a name="newtask"></a>NewTask
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| list_id |integer |是 |
| title |字符串 |是 |
| assignee_id |integer |否 |
| 已完成 |布尔值 |否 |
| recurrence_type |字符串 |否 |
| recurrence_count |integer |否 |
| due_date |字符串 |否 |
| starred |布尔值 |否 |

### <a name="newlist"></a>NewList
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| title |字符串 |是 |

### <a name="newsubtask"></a>NewSubtask
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| list_id |integer |是 |
| task_id |integer |是 |
| title |字符串 |是 |
| 已完成 |布尔值 |否 |

### <a name="newnote"></a>NewNote
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| list_id |integer |是 |
| task_id |integer |是 |
| 内容 |字符串 |是 |

### <a name="newcomment"></a>NewComment
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| list_id |integer |是 |
| task_id |integer |是 |
| text |字符串 |是 |

### <a name="newreminder"></a>NewReminder
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| list_id |integer |是 |
| task_id |integer |是 |
| 日期 |字符串 |是 |

### <a name="updatetask"></a>UpdateTask
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| revision |integer |否 |
| title |字符串 |否 |
| assignee_id |integer |否 |
| 已完成 |布尔值 |否 |
| recurrence_type |字符串 |否 |
| recurrence_count |integer |否 |
| due_date |字符串 |否 |
| starred |布尔值 |否 |

### <a name="updatelist"></a>UpdateList
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| revision |integer |否 |
| title |字符串 |否 |

### <a name="updatesubtask"></a>UpdateSubtask
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| revision |integer |否 |
| title |字符串 |否 |
| 已完成 |布尔值 |否 |

### <a name="updatenote"></a>UpdateNote
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| revision |integer |否 |
| 内容 |字符串 |否 |

### <a name="updatereminder"></a>UpdateReminder
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 日期 |字符串 |否 |
| revision |integer |否 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


