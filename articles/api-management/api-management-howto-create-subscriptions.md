---
title: 在 Azure API 管理中创建订阅 | Microsoft Docs
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
ms.openlocfilehash: 1393e548c46c23f6b50c1b18a274febb74914ae8
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054483"
---
# <a name="create-subscriptions-in-azure-api-management"></a>在 Azure API 管理中创建订阅

通过 Azure API 管理发布 API 时，常见的简单做法是使用订阅密钥保护对这些 API 的访问。 需使用已发布 API 的客户端应用程序调用这些 API 时必须在 HTTP 请求中包括一个有效的订阅密钥。 若要获取访问 API 所需的订阅密钥，必须拥有订阅。 有关订阅的详细信息，请参阅 [Azure API 管理中的订阅](api-management-subscriptions.md)。

本文演示如何在 Azure 门户中创建订阅。

## <a name="prerequisites"></a>先决条件

若要执行本文中的步骤，必须具备以下先决条件：

+ [创建 API 管理实例](get-started-create-service-instance.md)。
+ 了解 [API 管理中的订阅](api-management-subscriptions.md)。

## <a name="create-a-new-subscription"></a>创建新订阅

1. 选择左侧菜单中的“订阅”。
2. 选择“添加订阅”。
3. 提供订阅的名称，并选择范围。
4. 选择“保存”。

![灵活的订阅](./media/api-management-subscriptions/flexible-subscription.png)

创建订阅后，系统将提供两个用于访问 API 的 API 密钥。 一个主密钥，一个辅助密钥。 

## <a name="next-steps"></a>后续步骤
获取有关 API 管理的详细信息：

+ 了解 API 管理中的其他[概念](api-management-terminology.md)。
+ 按[教程](import-and-publish.md)操作，详细了解 API 管理。
+ 查看[常见问题解答页](api-management-faq.md)，了解常见问题。