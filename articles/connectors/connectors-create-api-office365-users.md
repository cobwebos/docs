---
title: 连接到 Office 365 用户 - Azure 逻辑应用 | Microsoft Docs
description: 使用 Office 365 用户 REST API 和 Azure 逻辑应用管理用户配置文件
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 08/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: f030ac07dc1615c435c1a110836d7a03ab8a8546
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295602"
---
# <a name="get-started-with-the-office-365-users-connector"></a>Office 365 用户连接器入门
连接到 Office 365 用户，获取配置文件、搜索用户等。 通过 Office 365 用户，可以：

* 根据从 Office 365 用户中获取的数据生成业务流。 
* 使用获取直接下属、获取经理的用户配置文件等操作。 这些操作可获得响应，并使输出可用于其他操作。 例如，获取用户的直接下属，然后使用此信息更新 SQL Azure 数据库。 

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-office-365-users"></a>创建到 Office 365 用户的连接
将此连接器添加到逻辑应用时，必须登录到 Office 365 用户帐户，并允许逻辑应用连接到帐户。

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

创建连接后，输入 Office 365 用户属性，如用户 ID。 本文中的 **REST API 参考**介绍了这些属性。

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/officeusers/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="more-connectors"></a>更多连接器
返回到 [API 列表](apis-list.md)。