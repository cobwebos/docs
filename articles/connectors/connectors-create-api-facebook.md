---
title: "在逻辑应用中添加 Facebook 连接器 | Microsoft Docs"
description: "使用 REST API 参数的 Facebook 连接器概述"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f4d6f0ed-c09b-488c-be1c-8cf2b5b1d4b8
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 27a9297c5d4cfb52f254ceb12e37423403e7f41b
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-facebook-connector"></a>Facebook 连接器入门
连接到 Facebook，发布到时间线、获取页面源等。 通过 Facebook，可以：

* 根据从 Facebook 中获取的数据生成业务流。 
* 在收到新发布时使用触发器。
* 使用发布到时间线、获取页面源等操作。 这些操作可获得响应，并使输出可用于其他操作。 例如，当时间线上有新发布时，可以获取该发布并将其推送到 Twitter 源。 

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-facebook"></a>创建到 Facebook 的连接
将此连接器添加到逻辑应用时，必须授权逻辑应用连接到 Facebook。

1. 登录到 Facebook 帐户
2. 选择“授权”，允许逻辑应用连接和使用 Facebook。 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 


## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/facebook/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="more-connectors"></a>更多连接器
返回到 [API 列表](apis-list.md)。