---
title: 连接到 Google Drive
description: 使用 Azure 逻辑应用自动创建和管理 Google Drive 文件的工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 155a579438747ca7de67eba6449b8a0191e86e2c
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666833"
---
# <a name="create-and-manage-files-for-google-drive-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用创建和管理 Google Drive 的文件

连接到 Google Drive，创建文件、获取行等。 通过 Google Drive，可以： 

* 根据从搜索中获取的数据生成业务流。 
* 使用搜索图像、搜索资讯等操作。 这些操作可获得响应，并使输出可用于其他操作。 例如，可以搜索视频，并使用 Twitter 将该视频发布到 Twitter 源。

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-the-connection-to-google-drive"></a>创建到 Google Drive 的连接

将此连接器添加到逻辑应用时，必须授权逻辑应用连接到 Google Drive。

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]

创建连接后，输入 Google Drive 属性，例如文件夹路径或文件名。 

## <a name="connector-specific-details"></a>特定于连接器的详细信息

有关由连接器的 Swagger 说明描述的触发器、操作和限制的技术详细信息，请查看[连接器的参考页](/connectors/googledrive/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](apis-list.md)
