---
title: 连接到 Google Drive
description: 使用 Google Drive REST API 和 Azure 逻辑应用创建和管理文件
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 6310c3b7e5b84915fa336708bc702e94317ad04c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789719"
---
# <a name="get-started-with-the-google-drive-connector"></a>Google Drive 连接器入门

连接到 Google Drive，创建文件、获取行等。 通过 Google Drive，可以： 

* 根据从搜索中获取的数据生成业务流。 
* 使用搜索图像、搜索资讯等操作。 这些操作可获得响应，并使输出可用于其他操作。 例如，可以搜索视频，然后使用 Twitter 将该视频发布到 Twitter 源。

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-the-connection-to-google-drive"></a>创建到 Google Drive 的连接

将此连接器添加到逻辑应用时，必须授权逻辑应用连接到 Google Drive。

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]

创建连接后，输入 Google Drive 属性，例如文件夹路径或文件名。 

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/googledrive/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="more-connectors"></a>更多连接器

返回到 [API 列表](apis-list.md)。
