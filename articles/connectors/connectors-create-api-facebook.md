---
title: 连接到 Facebook - Azure 逻辑应用 | Microsoft Docs
description: 使用 Facebook REST API 和 Azure 逻辑应用管理时间线和页面
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 25595127d913d3cd093e0af3d7916e33fc7cb352
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105968"
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