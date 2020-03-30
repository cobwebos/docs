---
title: Azure 开发人员测试实验室中环境的安全警报
description: 本文介绍如何在 DevTest 实验室中查看环境的安全警报并采取适当的操作。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2020
ms.author: spelluru
ms.openlocfilehash: fbac5a2fab91cdac8ebf626e324f12f209cfade5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588699"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Azure 开发人员测试实验室中环境的安全警报
作为实验室用户，您现在可以查看实验室环境的 Azure 安全中心警报。 安全中心会自动收集、分析以及整合 Azure 资源、网络和所连合作伙伴解决方案（如，防火墙和终结点保护解决方案）的日志数据，检测真正的威胁并减少误报。 安全中心显示了一系列安全警报（按严重程度排序），并显示了快速调查问题所需的信息以及修复攻击的建议。 [了解有关 Azure 安全中心中安全警报的更多详细信息](../security-center//security-center-alerts-overview.md)。  


## <a name="prerequisites"></a>先决条件
目前，您只能查看部署在实验室中的平台作为服务 （PaaS） 环境的安全警报。 要测试或使用此功能，请将[环境部署到实验室](devtest-lab-create-environment-from-arm.md)。 

## <a name="view-security-alerts-for-an-environment"></a>查看环境的安全警报

1. 在实验室的主页上，选择左侧菜单上**的安全警报**。 您应该看到安全警报的数量（高、中和低）。 详细了解[警报的分类方式](../security-center/security-center-alerts-overview.md#how-are-alerts-classified)。

    ![安全警报 - 概述](./media/environment-security-alerts/security-alerts-overview-page.png)
2. 右键单击最后一列中的三个点 （...），然后选择 **"查看安全警报**"。 

    ![查看安全警报](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. 您将看到有关警报和顾问建议的更多详细信息。 详细了解 Azure[安全中心中的安全和响应](../security-center/security-center-managing-and-responding-alerts.md)。

    ![查看安全警报](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>后续步骤
要了解有关环境的更多详细信息，请参阅以下文章：

- [使用 Azure 资源管理器模板创建多 vm 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)
- [配置和使用公共环境](devtest-lab-configure-use-public-environments.md)
