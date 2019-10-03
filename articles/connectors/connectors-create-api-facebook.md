---
title: 连接到 Facebook-Azure 逻辑应用
description: 使用 Facebook REST API 和 Azure 逻辑应用管理时间线和页面
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 83431184d7e9c5970ece6af143ee9b5166da96d5
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050955"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用管理 Facebook 时间线和页面

连接到 Facebook，发布到时间线、获取页面源等。 通过 Facebook，可以：

* 根据从 Facebook 中获取的数据生成业务流。 
* 在收到新发布时使用触发器。
* 使用发布到时间线、获取页面源等操作。 这些操作可获得响应，并使输出可用于其他操作。 例如，当时间线上有新发布时，可以获取该发布并将其推送到 Twitter 源。 

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-facebook"></a>创建到 Facebook 的连接

将此连接器添加到逻辑应用时，必须授权逻辑应用连接到 Facebook。

1. 登录到 Facebook 帐户。

2. 选择“授权”，允许逻辑应用连接和使用 Facebook。 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>连接器参考

如需技术详细信息（例如触发器、操作和限制，如连接器的 OpenAPI（以前为 Swagger）文件所述），请参阅[连接器的参考页](/connectors/facebook/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)