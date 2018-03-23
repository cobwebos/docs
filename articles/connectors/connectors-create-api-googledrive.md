---
title: 在逻辑应用中添加 Google Drive 连接器 | Microsoft Docs
description: 使用 REST API 参数的 Google Drive 连接器概述
services: ''
suite: ''
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: b2bcebc5-02d2-435b-b0da-ef53bc51c4b6
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 9cea2ea13b93e798912e4feea012f6bd64b90cac
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-google-drive-connector"></a>Google Drive 连接器入门
连接到 Google Drive，创建文件、获取行等。 通过 Google Drive，可以： 

* 根据从搜索中获取的数据生成业务流。 
* 使用搜索图像、搜索资讯等操作。 这些操作可获得响应，并使输出可用于其他操作。 例如，可以搜索视频，并使用 Twitter 将该视频发布到 Twitter 源。

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-the-connection-to-google-drive"></a>创建到 Google Drive 的连接
将此连接器添加到逻辑应用时，必须授权逻辑应用连接到 Google Drive。

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]
> 
> 

创建连接后，输入 Google Drive 属性，例如文件夹路径或文件名。 

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/googledrive/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="more-connectors"></a>更多连接器
返回到 [API 列表](apis-list.md)。
