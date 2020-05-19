---
title: 配置学习行为
description: Apprentice 模式为你提供了 Personalizer 服务及其机器学习功能的置信度，并提供了服务发送信息的指标，这些信息可以从中了解，而不会造成在线流量的风险。
ms.topic: how-to
ms.date: 05/01/2020
ms.openlocfilehash: af38500fc439964f9928cdd08aae2380ee0d0a8a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599476"
---
# <a name="configure-the-personalizer-learning-behavior"></a>配置 Personalizer 学习行为

[Apprentice 模式](concept-apprentice-mode.md)为你提供了 Personalizer 服务及其机器学习功能的信任和置信度，并确保向服务发送了可从其获知的信息，而不会造成在线流量的风险。

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="configure-apprentice-mode"></a>配置 Apprentice 模式

1. 登录到 Personalizer 资源的[Azure 门户](https://portal.azure.com)。

1. 在 "**配置**" 页上的 "**学习行为**" 选项卡上，选择 "**返回基线操作"，学习为 Apprentice，** 然后选择 "**保存**"。

> [!div class="mx-imgBorder"]
> ![在 Azure 门户中配置 apprentice 模式学习行为的屏幕截图](media/settings/configure-learning-behavior-azure-portal.png)

## <a name="changes-to-the-existing-application"></a>对现有应用程序的更改

你的现有应用程序不应更改其当前选择要显示的操作的方式，或应用程序如何确定此**操作的值**。 应用程序的唯一更改可能是发送到 Personalizer 的排名 API 的操作的顺序。 应用程序当前显示的操作将作为操作列表中的_第一个操作_发送。 [排名 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)使用第一个操作训练 Personalizer 模型。

### <a name="configure-your-application-to-call-the-rank-api"></a>配置应用程序以调用排名 API

若要将 Personalizer 添加到你的应用程序，你需要调用排名和奖励 Api。

1. 在你确定操作列表及其功能的现有应用程序逻辑中的点后面添加[排名 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)调用。 操作列表中的第一个操作需要是现有逻辑选择的操作。

1. 配置代码以显示与排名 API 响应的**奖励操作 ID**相关联的操作。

### <a name="configure-your-application-to-call-reward-api"></a>配置应用程序以调用奖励 API

1. 使用现有业务逻辑计算所显示操作的**奖励**。 该值必须介于0到1之间。 使用[奖励 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)向 Personalizer 发送此奖励。 回报值不应立即出现，可以在某个时间段延迟，具体取决于你的业务逻辑。

1. 如果未在配置的 "**奖励等待时间**" 内返回奖励，则将改用默认的奖励。

## <a name="evaluate-apprentice-mode"></a>评估 Apprentice 模式

在 Azure 门户的 Personalizer 资源的 "**评估**" 页上，查看当前的**学习行为性能**。

> [!div class="mx-imgBorder"]
> ![查看 Azure 门户中的 apprentice 模式学习行为评估的屏幕截图](media/settings/evaluate-apprentice-mode.png)

Apprentice 模式提供下列**评估指标**：
* **基线–平均奖励**：应用程序默认值（基线）的平均收益。
* **Personalizer –平均奖励**：可能已达到的总奖励 Personalizer 平均数。
* **优于最近1000个事件的成就率**：基线和 Personalizer 奖励的比率–在最近的1000事件上标准化。

## <a name="evaluate-apprentice-mode-features"></a>评估 Apprentice 模式功能

使用[脱机评估](how-to-offline-evaluation.md)来评估功能。

## <a name="switch-behavior-to-online-mode"></a>将行为切换到联机模式

确定 Personalizer 的平均使用百分比为75-85% 时，该模型已准备好切换到联机模式。

在 Personalizer 资源 Azure 门户中，在 "**配置**" 页上的 "**学习行为**" 选项卡上，选择 "**返回最佳操作"** ，然后选择 "**保存**"。

不需要对排名和奖励 API 调用进行任何更改。

## <a name="next-steps"></a>后续步骤

* [管理模型和学习设置](how-to-manage-model.md)
