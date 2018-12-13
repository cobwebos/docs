---
title: 如何在 Azure API 管理中创建订阅 | Microsoft Docs
description: 了解如何在 Azure API 管理中创建订阅。
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 3f4e113125ec9644aac974e47996afe290e57cee
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621676"
---
# <a name="how-to-create-subscriptions-in-azure-api-management"></a>如何在 Azure API 管理中创建订阅

通过 Azure API 管理 (APIM) 发布 API 时，若要确保对这些 API 进行安全的访问，最容易且最常用的方式是使用订阅密钥。 换句话说，需使用已发布 API 的客户端应用程序必须在调用这些 API 时在 HTTP 请求中包括一个有效的订阅密钥。 若要获取访问 API 所需的订阅密钥，必须有一个订阅。 有关订阅的详细信息，请参阅 [Azure API 管理中的订阅](api-management-subscriptions.md)

本文展示了用于在 Azure 门户中创建订阅的步骤。

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，你需要：

+ [创建 APIM 实例](get-started-create-service-instance.md)
+ 了解 [Azure 中的订阅](api-management-subscriptions.md)

## <a name="create-a-new-subscription"></a>创建新订阅

1. 单击左侧菜单中的“订阅”
2. 单击“添加订阅”
3. 提供订阅的名称，并选择范围
4. 单击“保存”

![灵活的订阅](./media/api-management-subscriptions/flexible-subscription.png)

在创建订阅后，会预配用于访问 API 的一对 API 密钥（主密钥和辅助密钥）。

## <a name="next-steps"></a>后续步骤
有关 API 管理的详细信息，请执行以下操作：

+ 了解 API 管理中的其他[概念](api-management-terminology.md)
+ 按[教程](import-and-publish.md)操作，详细了解 API 管理
+ 查看[常见问题解答页](api-management-faq.md)，了解常见问题