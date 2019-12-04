---
title: 连接到 Office 365 视频
description: 使用 Office 365 视频 REST API 和 Azure 逻辑应用管理视频
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: bf3c7c4faac3fcd4dd0c3f368fbe18a224ed591b
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789472"
---
# <a name="get-started-with-the-office365-video-connector"></a>Office 365 视频连接器入门

连接到 Office 365 视频，获取有关 Office 365 视频的信息、获取视频列表等。 通过 Office 365 视频，可以：

* 根据从 Office 365 视频中获取的数据生成业务流。 

* 使用检查视频门户状态、获取频道中所有视频列表等操作。 这些操作可获得响应，并使输出可用于其他操作。 

例如，可以使用必应搜索连接器搜索 Office 365 视频，并使用 Office 365 视频连接器获取该视频的相关信息。 如果视频满足要求，可在 Facebook 上发布此视频。

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-office365-video-connector"></a>创建到 Office 365 视频连接器的连接

将此连接器添加到逻辑应用时，必须登录到 Office 365 视频帐户，并允许逻辑应用连接到帐户。

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

创建连接后，输入 Office 365 视频属性，如租户名称或频道 ID。 

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/office365videoconnector/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)