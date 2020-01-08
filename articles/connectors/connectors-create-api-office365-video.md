---
title: 连接到 Office 365 视频
description: 使用 Azure 逻辑应用自动执行管理 Office 365 视频中视频的任务和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: 8ac6b7b411e7f42dd076c5b16e7b500a819c617f
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665779"
---
# <a name="manage-videos-in-office365-video-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用管理 Office365 视频中的视频

连接到 Office 365 视频，获取有关 Office 365 视频的信息、获取视频列表等。 通过 Office 365 视频，可以：

* 根据从 Office 365 视频中获取的数据生成业务流。 

* 使用检查视频门户状态、获取频道中所有视频列表等操作。 这些操作可获得响应，并使输出可用于其他操作。 

例如，可以使用必应搜索连接器搜索 Office 365 视频，并使用 Office 365 视频连接器获取该视频的相关信息。 如果视频满足要求，可在 Facebook 上发布此视频。

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="connect-to-office365-video"></a>连接到 Office365 视频

将此连接器添加到逻辑应用时，必须登录到 Office 365 视频帐户，并允许逻辑应用连接到帐户。

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

创建连接后，输入 Office 365 视频属性，如租户名称或频道 ID。 

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/office365videoconnector/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)