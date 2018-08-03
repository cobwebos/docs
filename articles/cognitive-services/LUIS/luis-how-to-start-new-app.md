---
title: 使用 LUIS 创建新应用 | Microsoft Docs
description: 在语言理解 (LUIS) 网页上创建和管理应用程序。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/17/2018
ms.author: diberry
ms.openlocfilehash: 3adeecd4a4e2040a92689b7c92be9630c9a0d93b
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225408"
---
# <a name="create-an-app"></a>创建应用
可通过不同的方式创建新应用： 

* [首先](#create-new-app)创建一个空应用，然后创建意向、表述和实体。
* [首先](#create-new-app)创建一个空应用，然后添加[预生成域](luis-how-to-use-prebuilt-domains.md)。
* 从已包含意向、表述和实体的 JSON 文件[导入 LUIS 应用](#import-new-app)。

## <a name="what-is-an-app"></a>什么是应用
应用包含模型的[版本](luis-how-to-manage-versions.md)和该应用的任何[协作者](luis-how-to-collaborate.md)。 创建应用时，请选择区域性（[语言](luis-supported-languages.md)），此选项以后无法更改。 

新应用的默认版本为“0.1”。 

可在“我的应用”页创建和管理应用程序。 始终可以通过选择 [LUIS](luis-reference-regions.md) 网站顶部导航栏上的“我的应用”来访问此页。 

[![](media/luis-create-new-app/apps-list.png "应用列表的屏幕截图")](media/luis-create-new-app/apps-list.png#lightbox)

## <a name="create-new-app"></a>创建新应用

1. 在“我的应用”页，选择“创建新应用”。
2. 在对话框中，将应用程序命名为“TravelAgent”。

    ![“创建新应用”对话框](./media/luis-create-new-app/create-app.png)

3. 选择应用程序区域性（对于 TravelAgent 应用，请选择英语），然后选择“完成”。 

    >[!NOTE]
    >创建应用程序后将无法更改区域性。 

## <a name="import-new-app"></a>导入新应用
在 JSON 文件中，可以设置名称（最多 50 个字符）、版本（最多 10 个字符）和应用说明。 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/Examples-BookFlight) 提供示例应用程序 JSON 文件。

1. 在“我的应用”页，选择“导入新应用”。
2. 在“导入新应用”对话框中，选择定义 LUIS 应用的 JSON 文件。

    ![“导入新应用”对话框](./media/luis-create-new-app/import-app.png)

## <a name="export-app"></a>导出应用
1. 在“我的应用”页，选中应用所在行末尾的省略号 (...)。

    [![](media/luis-create-new-app/apps-list.png "每个应用操作的弹出对话框屏幕截图")](media/luis-create-new-app/three-dots.png#lightbox)

2. 从菜单中选择“导出应用”。 

## <a name="rename-app"></a>重命名应用

1. 在“我的应用”页，选中应用所在行末尾的省略号 (...)。 
2. 从菜单中选择“重命名”。
3. 输入应用的新名称，然后选择“完成”。

## <a name="delete-app"></a>删除应用

> [!CAUTION]
> 此操作将删除所有协作者和所有者的该应用。 请在删除应用前将其[导出](#export-app)。 

1. 在“我的应用”页，选中应用所在行末尾的省略号 (...)。 
2. 从菜单中选择“删除”。
3. 在确认消息窗口中，选择“确定”。

## <a name="export-endpoint-logs"></a>导出终结点日志
日志中包含查询、UTC 时间和 LUIS JSON 响应。

1. 在“我的应用”页，选中应用所在行末尾的省略号 (...)。 
2. 从菜单中选择“导出终结点日志”。

```
Query,UTC DateTime,Response
text i'm driving and will be 30 minutes late to the meeting,02/13/2018 15:18:43,"{""query"":""text I'm driving and will be 30 minutes late to the meeting"",""intents"":[{""intent"":""None"",""score"":0.111048922},{""intent"":""SendMessage"",""score"":0.987501}],""entities"":[{""entity"":""i ' m driving and will be 30 minutes late to the meeting"",""type"":""Message"",""startIndex"":5,""endIndex"":58,""score"":0.162995353}]}"
```

## <a name="next-steps"></a>后续步骤

应用中的第一个任务是[添加意向](luis-how-to-add-intents.md)。