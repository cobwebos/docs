---
title: Trello | Microsoft Docs
description: "使用 Azure 应用服务创建逻辑应用。 Trello 可使你在工作和家中洞察你的所有项目。  它是一种简单、自由、灵活且可视的管理项目和组织一切事务的方式。  连接到 Trello 以管理你的看板、列表和卡片"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: fe7a4377-5c24-4f72-ab1a-6d9d23e8d895
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 2616db980cb37c1c3759096ec3c05b98d687e047


---
# <a name="get-started-with-the-trello-connector"></a>Trello 连接器入门
Trello 可使你在工作和家中洞察你的所有项目。  它是一种简单、自由、灵活且可视的管理项目和组织一切事务的方式。  连接到 Trello 以管理你的看板、列表和卡片。

> [!NOTE]
> 此文章版本适用于 2015 年 8 月 1 日预览版架构的逻辑应用。
> 
> 

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>触发器和操作
Trello 连接器可用作操作；它具有触发器。 所有连接器都支持采用 JSON 和 XML 格式的数据。

 Trello 连接器具有以下可用操作和/或触发器：

### <a name="trello-actions"></a>Trello 操作
可执行以下操作：

| 操作 | 说明 |
| --- | --- |
| [ListCards](connectors-create-api-trello.md#listcards) |列出看板中的卡片 |
| [GetCard](connectors-create-api-trello.md#getcard) |按 ID 获取卡片 |
| [UpdateCard](connectors-create-api-trello.md#updatecard) |更新卡片 |
| [DeleteCard](connectors-create-api-trello.md#deletecard) |删除卡片 |
| [CreateCard](connectors-create-api-trello.md#createcard) |在 Trello 帐户中新建卡片 |
| [ListBoards](connectors-create-api-trello.md#listboards) |列出看板 |
| [GetBoard](connectors-create-api-trello.md#getboard) |按 ID 获取看板 |
| [ListLists](connectors-create-api-trello.md#listlists) |列出看板中的卡片列表 |
| [GetList](connectors-create-api-trello.md#getlist) |按 ID 获取列表 |

### <a name="trello-triggers"></a>Trello 触发器
可侦听以下事件：

| 触发器 | 说明 |
| --- | --- |
| 向看板添加新卡片时 |向看板添加新卡片时触发流 |
| 向列表添加新卡片时 |向列表添加新卡片时触发流 |

## <a name="create-a-connection-to-trello"></a>创建到 Trello 的连接
若要使用 Trello 创建逻辑应用，必须先创建**连接**，然后提供以下属性的详细信息：

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 令牌 |是 |提供 Trello 凭据 |

创建连接后，可使用它执行操作并侦听触发器，如本文所述。

> [!INCLUDE [Steps to create a connection to Trello](../../includes/connectors-create-api-trello.md)]
> 
> [!TIP]
> 可在其他逻辑应用中使用此连接。
> 
> 

## <a name="reference-for-trello"></a>Trello 的参考
适用于版本：1.0

## <a name="onnewcardinboard"></a>OnNewCardInBoard
向看板添加新卡片时：向看板添加新卡片时触发流

```GET: /trigger/boards/{board_id}/cards```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| board_id |字符串 |是 |路径 |无 |要提取卡片的看板的唯一 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败 |

## <a name="onnewcardinlist"></a>OnNewCardInList
向列表添加新卡片时：向列表添加新卡片时触发流

```GET: /trigger/lists/{list_id}/cards```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| board_id |字符串 |是 |查询 |无 |要提取卡片的看板的唯一 ID |
| list_id |字符串 |是 |路径 |无 |要提取卡片的列表的唯一 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败 |

## <a name="listcards"></a>ListCards
列出看板中的卡片：列出看板中的卡片

```GET: /boards/{board_id}/cards```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| board_id |字符串 |是 |路径 |无 |要提取所有卡片的看板的 ID |
| actions |字符串 |否 |查询 |无 |列出要返回的操作。 指定“全部”或有效值的逗号分隔列表 |
| attachments |布尔值 |否 |查询 |无 |显示附件 |
| attachment_fields |字符串 |否 |查询 |无 |列出要返回的附件字段。 指定“全部”或有效值的逗号分隔列表 |
| stickers |布尔值 |否 |查询 |无 |显示便签 |
| members |布尔值 |否 |查询 |无 |显示成员 |
| memeber_fields |字符串 |否 |查询 |无 |列出要返回的成员字段。 指定“全部”或有效值的逗号分隔列表 |
| CheckItemStates |布尔值 |否 |查询 |无 |返回卡片状态 |
| Checklists |字符串 |否 |查询 |无 |显示清单 |
| limit |integer |否 |查询 |无 |要返回的最大结果数，介于 1 到 1000 之间 |
| since |字符串 |否 |查询 |无 |提取此日期之后的所有卡片 |
| before |字符串 |否 |查询 |无 |提取此日期之前的所有卡片 |
| filter |字符串 |否 |查询 |无 |筛选响应 |
| fields |字符串 |否 |查询 |无 |列出要返回的卡片字段。 指定“全部”或有效值的逗号分隔列表 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败 |

## <a name="getcard"></a>GetCard
按 ID 获取卡片：按 ID 获取卡片

```GET: /cards/{card_id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| board_id |字符串 |是 |查询 |无 |要提取卡片的看板的 ID |
| card_id |字符串 |是 |路径 |无 |要提取的卡片的 ID |
| actions |字符串 |否 |查询 |无 |列出要返回的操作。 指定“全部”或有效值的逗号分隔列表 |
| actions_entities |布尔值 |否 |查询 |无 |返回操作实体 |
| actions_display |布尔值 |否 |查询 |无 |返回操作显示 |
| actions_limit |integer |否 |查询 |无 |要返回的最大操作数 |
| action_fields |字符串 |否 |查询 |无 |要为每个操作返回的操作字段列表。 指定“全部”或有效值的逗号分隔列表 |
| action_memberCreator_fields |字符串 |否 |查询 |无 |要返回的操作成员创建者字段列表。 指定“全部”或有效值的逗号分隔列表 |
| attachments |布尔值 |否 |查询 |无 |返回附件 |
| attachement_fields |字符串 |否 |查询 |无 |要为每个附件返回的附件字段列表。 指定“全部”或有效值的逗号分隔列表 |
| members |布尔值 |否 |查询 |无 |返回成员 |
| member_fields |字符串 |否 |查询 |无 |要为每个成员返回的成员字段列表。 指定“全部”或有效值的逗号分隔列表 |
| membersVoted |布尔值 |否 |查询 |无 |返回投票成员 |
| memberVoted_fields |字符串 |否 |查询 |无 |要为每个投票成员返回的投票成员字段列表。 指定“全部”或有效值的逗号分隔列表 |
| checkItemStates |布尔值 |否 |查询 |无 |返回卡片状态 |
| checkItemState_fields |字符串 |否 |查询 |无 |要为每个卡片项目状态返回的状态字段列表。 指定“全部”或有效值的逗号分隔列表 |
| checklists |字符串 |否 |查询 |无 |返回清单 |
| checklist_fields |字符串 |否 |查询 |无 |要为每个清单返回的清单字段列表。 指定“全部”或有效值的逗号分隔列表 |
| board |布尔值 |否 |查询 |无 |返回卡片所属的看板 |
| board_fields |字符串 |否 |查询 |无 |列出要返回的看板字段。 指定“全部”或有效值的逗号分隔列表 |
| list |布尔值 |否 |查询 |无 |返回卡片所属的列表 |
| list_fields |字符串 |否 |查询 |无 |列出要返回的列表字段。 指定“全部”或有效值的逗号分隔列表 |
| stickers |布尔值 |否 |查询 |无 |返回便签 |
| sticker_fields |字符串 |否 |查询 |无 |列出要为每个便签返回的便签字段。 指定“全部”或有效值的逗号分隔列表 |
| fields |字符串 |否 |查询 |无 |列出要返回的卡片字段。 指定“全部”或有效值的逗号分隔列表 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败 |

## <a name="updatecard"></a>UpdateCard
更新卡片：更新卡片

```PUT: /cards/{card_id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| board_id |字符串 |是 |查询 |无 |要从中提取卡片的看板的 ID |
| card_id |字符串 |是 |路径 |无 |要更新的卡片的 ID |
| updateCard | |是 |body |无 |更新后的卡片值 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败 |

## <a name="deletecard"></a>DeleteCard
删除卡片：删除卡片

```DELETE: /cards/{card_id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| board_id |字符串 |是 |查询 |无 |要从中提取卡片的看板的 ID |
| card_id |字符串 |是 |路径 |无 |要删除的卡片的 ID |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败 |

## <a name="createcard"></a>CreateCard
创建卡片：在 Trello 帐户中新建卡片

```POST: /cards```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| board_id |字符串 |是 |查询 |无 |要在其中创建卡片的看板的唯一 ID |
| newCard | |是 |body |无 |要添加到 Trello 看板的新卡片 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败 |

## <a name="listboards"></a>ListBoards
列出看板：列出看板

```GET: /member/me/boards```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| filter |字符串 |否 |查询 |无 |列出要应用到看板结果的筛选器。 指定“全部”或有效值的逗号分隔列表 |
| fields |字符串 |否 |查询 |无 |列出要返回的看板字段。 指定“全部”或有效值的逗号分隔列表 |
| actions |字符串 |否 |查询 |无 |列出要返回的操作字段。 指定“全部”或有效值的逗号分隔列表 |
| actions_entities |布尔值 |否 |查询 |无 |返回操作实体 |
| actions_limit |integer |否 |查询 |无 |要返回的最大操作数 |
| actions_format |字符串 |否 |查询 |无 |指定要返回的操作的格式 |
| actions_since |字符串 |否 |查询 |无 |返回指定日期之后的操作 |
| action_fields |字符串 |否 |查询 |无 |列出要返回的操作字段。 指定“全部”或有效值的逗号分隔列表 |
| memberships |字符串 |否 |查询 |无 |指定要返回的成员身份信息。 指定“全部”或有效值的逗号分隔列表 |
| organization |布尔值 |否 |查询 |无 |指定返回组织信息 |
| organization_fields |字符串 |否 |查询 |无 |列出要返回的组织字段。 指定“全部”或有效值的逗号分隔列表 |
| lists |字符串 |否 |查询 |无 |指定是否返回属于看板的列表 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败 |

## <a name="getboard"></a>GetBoard
按 ID 获取看板：按 ID 获取看板

```GET: /boards/{board_id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| board_id |字符串 |是 |路径 |无 |要获取的看板的唯一 ID |
| actions |字符串 |否 |查询 |无 |列出要返回的操作。 指定“全部”或有效值的逗号分隔列表 |
| action_entities |布尔值 |否 |查询 |无 |指定是否返回操作实体 |
| actions_display |布尔值 |否 |查询 |无 |指定是否返回操作显示 |
| actions_format |字符串 |否 |查询 |无 |指定要返回的操作的格式 |
| actions_since |字符串 |否 |查询 |无 |仅返回此日期之后的操作 |
| actions_limit |integer |否 |查询 |无 |要返回的最大操作数 |
| action_fields |字符串 |否 |查询 |无 |列出要与每个字段一起返回的字段。 指定“全部”或有效值的逗号分隔列表 |
| action_memeber |布尔值 |否 |查询 |无 |指定是否返回操作成员 |
| action_member_fields |字符串 |否 |查询 |无 |列出要与每个操作成员一起返回的成员字段。 指定“全部”或有效值的逗号分隔列表 |
| action_memberCreator |布尔值 |否 |查询 |无 |指定是否返回操作成员创建者 |
| action_memberCreator_fields |字符串 |否 |查询 |无 |列出要返回的操作成员创建者字段。 指定“全部”或有效值的逗号分隔列表 |
| cards |字符串 |否 |查询 |无 |指定要返回的卡片 |
| card_fields |字符串 |否 |查询 |无 |列出要为每个卡片返回的字段。 指定“全部”或有效值的逗号分隔列表 |
| card_attachments |布尔值 |是 |查询 |无 |指定是否返回卡片上的附件 |
| card_attachment_fields |字符串 |否 |查询 |无 |列出要为每个附件返回的附件字段。 指定“全部”或有效值的逗号分隔列表 |
| card_checklists |字符串 |否 |查询 |无 |指定要为每个卡片返回的清单 |
| card_stickers |布尔值 |否 |查询 |无 |指定是否返回卡片便签 |
| boardStarts |字符串 |否 |查询 |无 |指定要返回的看板星号 |
| 标签 |字符串 |否 |查询 |无 |指定要返回的标签 |
| label_fields |字符串 |否 |查询 |无 |列出要为每个标签返回的标签字段。 指定“全部”或有效值的逗号分隔列表 |
| labels_limits |integer |否 |查询 |无 |要返回的最大标签数 |
| lists |字符串 |否 |查询 |无 |指定要返回的列表 |
| list_fields |字符串 |否 |查询 |无 |列出要为每个列表返回的列表字段。 指定“全部”或有效值的逗号分隔列表 |
| memberships |字符串 |否 |查询 |无 |列出要返回的成员身份。 指定“全部”或有效值的逗号分隔列表 |
| memberships_member |布尔值 |否 |查询 |无 |指定是否返回成员身份成员 |
| memberships_member_fields |字符串 |否 |查询 |无 |列出要为每个成员身份成员返回的成员字段。 指定“全部”或有效值的逗号分隔列表 |
| members |字符串 |否 |查询 |无 |列出要返回的成员 |
| member_fields |字符串 |否 |查询 |无 |列出要为每个成员返回的成员字段。 指定“全部”或有效值的逗号分隔列表 |
| membersInvited |字符串 |否 |查询 |无 |指定要返回的已邀请成员 |
| membersInvited_fields |字符串 |否 |查询 |无 |列出要为每个返回的字段。 指定“全部”或有效值的逗号分隔列表 |
| checklists |字符串 |否 |查询 |无 |指定要返回的清单 |
| checklist_fields |字符串 |否 |查询 |无 |列出要为每个清单返回的清单字段。 指定“全部”或有效值的逗号分隔列表 |
| organization |布尔值 |否 |查询 |无 |指定是否返回组织信息 |
| organization_fields |字符串 |否 |查询 |无 |列出要为每个组织返回的组织字段。 指定“全部”或有效值的逗号分隔列表 |
| organization_memberships |字符串 |否 |查询 |无 |列出要返回的组织成员身份。 指定“全部”或有效值的逗号分隔列表 |
| myPerfs |布尔值 |否 |查询 |无 |指定是否返回我的性能 |
| fields |字符串 |否 |查询 |无 |列出要返回的字段。 指定“全部”或有效值的逗号分隔列表 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败 |

## <a name="listlists"></a>ListLists
列出看板中的卡片列表：列出看板中的卡片列表

```GET: /boards/{board_id}/lists```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| board_id |字符串 |是 |路径 |无 |要提取列表的看板的唯一 ID |
| cards |字符串 |否 |查询 |无 |指定要返回的卡片 |
| card_fields |字符串 |否 |查询 |无 |列出要从中返回的卡片字段。 指定“全部”或有效值的逗号分隔列表 |
| filter |字符串 |否 |查询 |无 |指定列表的筛选器属性 |
| fields |字符串 |否 |查询 |无 |列出要返回的字段。 指定“全部”或有效值的逗号分隔列表 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败 |

## <a name="getlist"></a>GetList
按 ID 获取列表：按 ID 获取列表

```GET: /lists/{list_id}```

| Name | 数据类型 | 必选 | 所在位置 | 默认值 | 说明 |
| --- | --- | --- | --- | --- | --- |
| board_id |字符串 |是 |查询 |无 |列出要从中提取列表的看板的唯一 ID |
| list_id |字符串 |是 |路径 |无 |要提取的列表的唯一 ID |
| cards |字符串 |否 |查询 |无 |指定要返回的卡片 |
| card_fields |字符串 |否 |查询 |无 |列出要为每个卡片返回的卡片字段。 指定“全部”或有效值的逗号分隔列表 |
| board |布尔值 |否 |查询 |无 |指定是否返回看板信息 |
| board_fields |字符串 |否 |查询 |无 |列出要返回的看板字段。 指定“全部”或有效值的逗号分隔列表 |
| fields |字符串 |否 |查询 |无 |列出要返回的列表字段。 指定“全部”或有效值的逗号分隔列表 |

#### <a name="response"></a>响应
| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误 |
| default |操作失败 |

## <a name="object-definitions"></a>对象定义
### <a name="card"></a>卡片
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |字符串 |否 |
| checkItemStates |数组 |否 |
| closed |布尔值 |否 |
| dateLastActivity |字符串 |否 |
| desc |字符串 |否 |
| idBoard |字符串 |否 |
| idList |字符串 |否 |
| idMembersVoted |数组 |否 |
| idShort |integer |否 |
| idAttachmentCover |字符串 |否 |
| manualCoverAttachment |布尔值 |否 |
| idLabels |数组 |否 |
| 名称 |字符串 |否 |
| pos |integer |否 |
| shortLink |字符串 |否 |
| badges |未定义 |否 |
| due |字符串 |否 |
| email |字符串 |否 |
| shortUrl |字符串 |否 |
| subscribed |布尔值 |否 |
| url |字符串 |否 |

### <a name="badges"></a>Badges
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| Votes |integer |否 |
| ViewingMemberVoted |布尔值 |否 |
| Subscribed |布尔值 |否 |
| Fogbugz |字符串 |否 |
| CheckItems |integer |否 |
| CheckItemsChecked |integer |否 |
| 注释 |integer |否 |
| Attachments |integer |否 |
| 说明 |布尔值 |否 |
| Due |字符串 |否 |

### <a name="object"></a>对象
### <a name="createcard"></a>CreateCard
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| idList |字符串 |是 |
| 名称 |字符串 |是 |
| desc |字符串 |否 |
| pos |字符串 |否 |
| idMembers |字符串 |否 |
| idLabels |字符串 |否 |
| urlSource |字符串 |否 |
| fileSource |字符串 |否 |
| idCardSource |字符串 |否 |
| keepFromSource |字符串 |否 |

### <a name="updatecard"></a>UpdateCard
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| 名称 |字符串 |否 |
| desc |字符串 |否 |
| closed |布尔值 |否 |
| idMembers |字符串 |否 |
| idAttachmentCover |字符串 |否 |
| idList |字符串 |否 |
| idBoard |字符串 |否 |
| pos |字符串 |否 |
| due |字符串 |否 |
| subscribed |布尔值 |否 |

### <a name="board"></a>Board
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |字符串 |否 |
| closed |布尔值 |否 |
| dateLastActivity |字符串 |否 |
| dateLastView |字符串 |否 |
| desc |字符串 |否 |
| idOrganization |字符串 |否 |
| invitations |数组 |否 |
| invited |布尔值 |否 |
| labelNames |未定义 |否 |
| memberships |数组 |否 |
| 名称 |字符串 |否 |
| pinned |布尔值 |否 |
| powerUps |数组 |否 |
| perfs |未定义 |否 |
| shortLink |字符串 |否 |
| shortUrl |字符串 |否 |
| starred |字符串 |否 |
| subscribed |字符串 |否 |
| url |字符串 |否 |

### <a name="label"></a>标签
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| green |字符串 |否 |
| yellow |字符串 |否 |
| orange |字符串 |否 |
| red |字符串 |否 |
| purple |字符串 |否 |
| blue |字符串 |否 |
| sky |字符串 |否 |
| lime |字符串 |否 |
| pink |字符串 |否 |
| black |字符串 |否 |

### <a name="membership"></a>Membership
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |字符串 |否 |
| idMember |字符串 |否 |
| memberType |字符串 |否 |
| unconfirmed |布尔值 |否 |

### <a name="perfs"></a>Perfs
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| permissionLevel |字符串 |否 |
| voting |字符串 |否 |
| 注释 |字符串 |否 |
| invitations |字符串 |否 |
| selfJoin |布尔值 |否 |
| cardCovers |布尔值 |否 |
| calendarFeedEnabled |布尔值 |否 |
| background |字符串 |否 |
| backgroundColor |字符串 |否 |
| backgroundImage |字符串 |否 |
| backgroundImageScaled |字符串 |否 |
| backgroundTile |布尔值 |否 |
| backgroundBrightness |字符串 |否 |
| canBePublic |布尔值 |否 |
| canBeOrg |布尔值 |否 |
| canBePrivate |布尔值 |否 |
| canInvite |布尔值 |否 |

### <a name="list"></a>列出
| 属性名称 | 数据类型 | 必选 |
| --- | --- | --- |
| id |字符串 |否 |
| 名称 |字符串 |否 |
| closed |布尔值 |否 |
| idBoard |字符串 |否 |
| pos |数字 |否 |
| subscribed |布尔值 |否 |
| cards |数组 |否 |
| board |未定义 |否 |

## <a name="next-steps"></a>后续步骤
[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


