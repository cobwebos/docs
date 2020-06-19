---
title: Azure 事件网格合作伙伴主题
description: 通过 Azure 事件网格将第三方事件网格 SaaS 和 PaaS 合作伙伴的事件直接发送到 Azure 服务。
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 4546874b8a6cd8a7e45c3e6957a5181d66c7433f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83690033"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Azure 事件网格中的合作伙伴主题（预览版）
合作伙伴主题允许你将第三方事件源直接连接到事件网格。 通过这种集成，你可以订阅合作伙伴的事件，并且订阅方式与 Azure 服务相同。 

## <a name="available-partners"></a>可用合作伙伴
事件网格合作伙伴主题中可用的第一个合作伙伴是 Auth0。 [Auth0 合作伙伴主题](auth0-overview.md)使你可以连接 Auth0 和 Azure 帐户。 该集成用于实时响应、记录和监视 Auth0 事件。

登录 Auth0 帐户并创建事件网格集成以[立即试用](auth0-how-to.md)。 在 Auth0 中单击“创建”后，你将在 Azure 帐户中看到一个挂起的 Auth0 主题。 单击“激活”即可创建事件订阅、路由、筛选和传递事件，就像处理任何其他事件源一样。

## <a name="pricing"></a>定价
合作伙伴主题按照与系统主题相同的运作费率进行收费。

## <a name="limits"></a>限制
合作伙伴主题为公共预览版。 在公共预览期间，合作伙伴主题适用与系统主题和自定义主题[相同的限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits)。

## <a name="how-do-i-become-an-event-grid-partner"></a>如何成为事件网格合作伙伴？
为支持此启动创建的基础结构使新的合作伙伴能够轻松快捷地将其事件功能与事件网格集成。 有关详细信息，请参阅[新合作伙伴上手文档](partner-onboarding-overview.md)。

## <a name="next-steps"></a>后续步骤

- [Auth0 合作伙伴主题](auth0-overview.md)
- [如何使用 Auth0 合作伙伴主题](auth0-how-to.md)
- [成为事件网格合作伙伴](partner-onboarding-overview.md)