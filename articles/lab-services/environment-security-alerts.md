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
ms.openlocfilehash: 4ca17bece33107de756eb221e14eaab851660a99
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992230"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Azure 开发测试实验室中的环境的安全警报
本文介绍如何查看 Azure 开发测试实验室中的环境的安全警报。 

## <a name="prerequisites"></a>必备组件
目前，你只能查看部署到实验室中的环境的安全警报。 若要测试或使用此功能，请将环境部署到实验室。 

## <a name="view-security-alerts-for-an-environment"></a>查看环境的安全警报

1. 在实验室的主页上，在左侧菜单中选择 "**安全警报**"。 你应看到安全警报的数量（高、中和低）。

    ![安全警报-概述](./media/environment-security-alerts/security-alerts-overview-page.png)
2. 右键单击最后一列中的三个点（...），然后选择 "**查看安全警报**"。 

    ![查看安全警报](./media/environment-security-alerts/view-security-alerts-menu.png)
3. 你将看到有关警报和顾问建议的更多详细信息。 

    ![查看安全警报](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>后续步骤
若要了解有关环境的详细信息，请参阅以下文章：

- [利用 Azure 资源管理器模板创建多 vm 环境和 PaaS 资源](devtest-lab-create-environment-from-arm.md)
- [配置和使用公共环境](devtest-lab-configure-use-public-environments.md)
