---
title: 连接到 Office 365 用户
description: 使用 Azure 逻辑应用自动执行获取和管理 Office 365 用户配置文件中的配置文件的任务和工作流
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 179c7538d8072f0e1a4f7d049cf521971efd7a21
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666850"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用在 Office 365 用户中获取和管理配置文件

连接到 Office 365 用户，获取配置文件、搜索用户等。 通过 Office 365 用户，可以：

* 根据从 Office 365 用户中获取的数据生成业务流。 
* 使用获取直接下属、获取经理的用户配置文件等操作。 这些操作可获得响应，并使输出可用于其他操作。 例如，获取用户的直接下属，然后使用此信息更新 SQL Azure 数据库。 

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-office-365-users"></a>创建到 Office 365 用户的连接

将此连接器添加到逻辑应用时，必须登录到 Office 365 用户帐户，以便 Azure 逻辑应用可以连接到你的帐户。

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

创建连接后，输入 Office 365 用户属性，如用户 ID。 本文中的 **REST API 参考**介绍了这些属性。

## <a name="connector-specific-details"></a>特定于连接器的详细信息

有关由连接器的 Swagger 说明描述的触发器、操作和限制的技术详细信息，请查看[连接器的参考页](/connectors/officeusers/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](apis-list.md)