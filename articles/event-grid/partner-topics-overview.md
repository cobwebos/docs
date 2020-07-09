---
title: Azure 事件网格合作伙伴主题
description: 通过 Azure 事件网格将第三方事件网格 SaaS 和 PaaS 合作伙伴的事件直接发送到 Azure 服务。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 97829f262a00e2353624b2a3fa9df210518ad3a0
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86115989"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Azure 事件网格中的合作伙伴主题（预览）
通过使用合作伙伴主题，你可以将第三方事件源直接连接到 Azure 事件网格。 通过这种集成，你可以订阅合作伙伴的事件，并且订阅方式与 Azure 服务相同。 

## <a name="available-partners"></a>可用合作伙伴
通过事件网格合作伙伴提供的第一个合作伙伴主题是 Auth0。 可以使用[Auth0 合作伙伴主题](auth0-overview.md)连接 Auth0 和 Azure 帐户。 集成允许您实时响应、记录和监视 Auth0 事件。

[若要试用](auth0-how-to.md)，请登录 Auth0 帐户，并创建事件网格集成。 在 Auth0 中选择 "**创建**" 后，你将在 Azure 帐户中看到一个挂起的 Auth0 主题。 选择 "**激活**"，可以创建事件网格订阅来路由、筛选和传递事件，就像处理任何其他事件源一样。

## <a name="pricing"></a>定价
合作伙伴主题按照与系统主题相同的运作费率进行收费。

## <a name="limits"></a>限制
合作伙伴主题采用公共预览。 在公共预览版期间，合作伙伴主题将受到与系统主题和自定义主题[相同的限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits)。

## <a name="how-do-i-become-an-event-grid-partner"></a>如何成为事件网格合作伙伴？
为支持此启动创建的基础结构使新的合作伙伴能够轻松快捷地将其事件功能与事件网格集成。 有关详细信息，请参阅[合作伙伴载入文档](partner-onboarding-overview.md)。

## <a name="next-steps"></a>后续步骤

- [Auth0 合作伙伴主题](auth0-overview.md)
- [如何使用 Auth0 合作伙伴主题](auth0-how-to.md)
- [成为事件网格合作伙伴](partner-onboarding-overview.md)