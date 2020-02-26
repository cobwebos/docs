---
title: Azure 开发测试实验室中的环境的安全警报
description: 本文介绍如何在开发测试实验室中查看环境的安全警报，并采取适当的措施。
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588699"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Azure 开发测试实验室中的环境的安全警报
作为实验室用户，你现在可以查看实验室环境的 Azure 安全中心警报。 安全中心会自动收集、分析以及整合 Azure 资源、网络和所连合作伙伴解决方案（如，防火墙和终结点保护解决方案）的日志数据，检测真正的威胁并减少误报。 安全中心显示了一系列安全警报（按严重程度排序），并显示了快速调查问题所需的信息以及修复攻击的建议。 [详细了解 Azure 安全中心的安全警报](../security-center//security-center-alerts-overview.md)。  


## <a name="prerequisites"></a>必备条件
目前，只能查看部署到实验室中的 "平台即服务" （PaaS）环境的安全警报。 若要测试或使用此功能，请将[环境部署到实验室](devtest-lab-create-environment-from-arm.md)。 

## <a name="view-security-alerts-for-an-environment"></a>查看环境的安全警报

1. 在实验室的主页上，在左侧菜单中选择 "**安全警报**"。 你应看到安全警报的数量（高、中和低）。 了解有关[如何对警报进行分类](../security-center/security-center-alerts-overview.md#how-are-alerts-classified)的详细信息。

    ![安全警报-概述](./media/environment-security-alerts/security-alerts-overview-page.png)
2. 右键单击最后一列中的三个点（...），然后选择 "**查看安全警报**"。 

    ![查看安全警报](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. 你将看到有关警报和顾问建议的更多详细信息。 详细了解[如何管理和响应 Azure 安全中心的安全警报](../security-center/security-center-managing-and-responding-alerts.md)。

    ![查看安全警报](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>后续步骤
若要了解有关环境的详细信息，请参阅以下文章：

- [利用 Azure 资源管理器模板创建多 vm 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)
- [配置和使用公共环境](devtest-lab-configure-use-public-environments.md)
