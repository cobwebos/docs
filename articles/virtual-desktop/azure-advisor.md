---
title: 将 Windows 虚拟桌面与 Azure Advisor 集成-Azure
description: 如何将 Azure Advisor 用于 Windows 虚拟桌面部署。
author: Heidilohr
ms.topic: how-to
ms.date: 08/28/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 76b7f97b6020a3a0d5571a3a105d15f7d7893485
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89147386"
---
# <a name="use-azure-advisor-with-windows-virtual-desktop"></a>将 Azure Advisor 用于 Windows 虚拟桌面

Azure 顾问可帮助用户自行解决常见问题，而无需提供支持案例。 建议减少了提交帮助请求的需要，从而节省了时间和成本。

本文将介绍如何在 Windows 虚拟桌面部署中设置 Azure 顾问，以帮助用户。

## <a name="what-is-azure-advisor"></a>什么是 Azure 顾问？

Azure 顾问会分析你的配置和遥测，提供个性化的建议来解决常见问题。 利用这些建议，你可以优化 Azure 资源以实现可靠性、安全性、卓越运营、性能和成本。 在 [Azure 顾问网站](https://azure.microsoft.com/services/advisor/)上了解详细信息。

## <a name="how-to-start-using-azure-advisor"></a>如何开始使用 Azure 顾问

你只需 Azure 门户上的 Azure 帐户即可开始使用。 首先，在上打开 Azure 门户 <https://portal.azure.com/#home> ，然后选择 " **Azure 服务**" 下的 "**顾问**"，如下图所示。 还可以在 Azure 门户的搜索栏中输入 "Azure 顾问"。

> [!div class="mx-imgBorder"]
> ![Azure 门户的屏幕快照。 用户将鼠标光标悬停在 Azure Advisor 链接上，从而出现下拉菜单。](media/azure-advisor.png)

打开 Azure Advisor 后，你将看到五个类别：

- 成本
- 安全性
- 可靠性
- 卓越运营
- 性能

> [!div class="mx-imgBorder"]
> ![显示每个类别的五个菜单的 Azure 顾问的屏幕截图。 这五项显示在其自己的框中是成本、安全性、可靠性、卓越运营和性能。](media/advisor-categories.png)

选择某个类别后，你将看到其 "活动建议" 页。 在此页上，可以查看 Azure Advisor 为你提供的建议，如下图所示。

> [!div class="mx-imgBorder"]
> ![适用于卓越运营的活动建议列表的屏幕截图。 此列表显示具有不同优先级的七个建议。](media/active-suggestions.png)

## <a name="additional-tips-for-azure-advisor"></a>Azure 顾问的其他提示

- 请务必经常检查建议，每周至少一次。 Azure 顾问每天多次更新其活动建议。 检查新建议可以帮助你找出并解决较小的问题，从而防止出现更大的问题。

- 请始终尝试解决 Azure 顾问中优先级最高的问题。 高优先级问题标有红色。 使高优先级建议无法解决可能会导致线路出现问题。

- 如果建议不太重要，则可以将其取消或延迟。 若要消除或推迟建议，请转到 " **操作** " 列并更改项的状态。

- 请不要解除建议，除非你知道它们的出现原因，并确保它不会对你或你的用户产生负面影响。 始终选择 " **了解更多** " 以查看问题所在。 如果根据 Azure 顾问中的说明解决了问题，则该问题将自动从列表中消失。 解决问题比重复延迟问题更好。

- 每当遇到 Windows 虚拟桌面中的问题时，始终首先检查 Azure 顾问。 Azure 顾问将为你提供有关如何解决此问题的说明，或者至少指向可帮助的资源。

## <a name="next-steps"></a>后续步骤

若要了解如何解决建议，请参阅 [如何解决 Azure 顾问建议](azure-advisor-recommendations.md)。

如果你对新建议有建议，请在我们的 [Azure Advisor 用户语音论坛](https://windowsvirtualdesktop.uservoice.com/forums/930847-azure-advisor-recommendations)上发布。
