---
title: Azure 安全中心的主仪表板或 "概述" 页
description: 了解安全中心 "概述" 页的功能
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 637f7dde63d562f71aa31561a960ed3502a80229
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933367"
---
# <a name="azure-security-centers-overview-page"></a>Azure 安全中心的 "概述" 页

打开 Azure 安全中心时，要显示的第一页是 "概述" 页。 

:::image type="content" source="media/overview-page/overview.png" alt-text="安全中心概述页面":::

通过 "安全中心概述" 页，发现并评估工作负荷的安全性，并识别和缓解风险。

概述为混合云工作负荷的安全状况提供统一的视图。 此外，它还显示安全警报、覆盖范围信息等。


## <a name="features-of-the-overview-page"></a>"概述" 页的功能

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="安全中心的 "概述" 页的顶部栏":::

**顶部菜单栏**提供：
- **订阅** -通过选择此按钮，可以查看和筛选订阅列表。 安全中心会调整显示信息，以反映所选订阅的安全状况。
- **新增** 功能-打开 [发行说明](release-notes.md) ，以便随时了解新功能、bug 修复和弃用的功能。
- 连接的云帐户的**高级数字**，用于在下面的主要磁贴中显示信息的上下文。 以及活动的建议和警报数量。
    详细了解如何连接你的 [AWS 帐户](quickstart-onboard-aws.md) 和 [GCP 项目](quickstart-onboard-gcp.md)。


页面的中心是 **四个磁贴**，每个磁贴都链接到专用仪表板以获取更多详细信息：
- 安全**分数**-安全中心会持续评估你的资源、订阅和组织的安全问题。 然后，它将所有调查结果汇总成一个分数，让你可以一目了然地了解当前的安全状况：分数越高，识别出的风险级别就越低。 [了解详细信息](secure-score-security-controls.md)。
- **合规性** -安全中心根据 Azure 环境的持续评估，提供对符合性状态的见解。 安全中心根据安全最佳做法分析混合云环境中的风险因素。 这些评估将从一组受支持的标准映射到符合性控制。[了解更多](security-center-compliance-dashboard.md)。
- **Azure Defender** -这是 (CWPP) 集成在安全中心内的云工作负荷保护平台，用于对 Azure 和混合工作负荷进行高级、智能、保护。 该磁贴将显示当前所选订阅的已连接资源的覆盖区 () 和最近的警报，按严重性进行颜色编码。 [了解详细信息](azure-defender.md)。
- **清单** -磁贴显示了受监视的 vm 的数量，以及由安全 CenterBen 监视的资源的简单 barometer。 [了解详细信息](asset-inventory.md)。


**Insights**窗格为你的环境提供自定义项，包括：
- 最受攻击的资源
- 最可能增加安全分数的安全控制
- 受影响资源最多的活动建议
- Azure 安全中心专家最近撰写的博客文章

## <a name="next-steps"></a>后续步骤

此页面介绍了安全中心概述页。 如需相关信息，请参阅：

- [利用资产清单和管理工具浏览和管理资源](asset-inventory.md)
- [Azure 安全中心的安全评分](secure-score-security-controls.md)