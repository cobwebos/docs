---
title: "在逻辑应用中添加 Office 365 用户连接器 | Microsoft Docs"
description: "使用 REST API 参数的 Office 365 用户连接器概述"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2146481-9105-4f56-b4c2-7ae340cb922f
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 330f733440932a769eb0fe6031cd0d947a820080
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017

---
# <a name="get-started-with-the-office-365-users-connector"></a>Office 365 用户连接器入门
连接到 Office 365 用户，获取配置文件、搜索用户等。 通过 Office 365 用户，你可以：

* 根据从 Office 365 用户中获取的数据生成你的业务流。 
* 使用获取直接下属、获取经理的用户配置文件等操作。 这些操作可获得响应，然后使输出可用于其他操作。 例如，获取用户的直接下属，然后获取此信息并更新 SQL Azure 数据库。 

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="create-a-connection-to-office-365-users"></a>创建到 Office 365 用户的连接
将此连接器添加到逻辑应用时，必须登录到 Office 365 用户帐户，并允许逻辑应用连接到你的帐户。

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

创建连接后，输入 Office 365 用户属性，如用户 ID。 本主题中的 **REST API 参考**介绍了这些属性。

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/officeusers/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="more-connectors"></a>更多连接器
返回到 [API 列表](apis-list.md)。
