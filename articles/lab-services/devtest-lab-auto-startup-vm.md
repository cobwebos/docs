---
title: 在 Azure 开发测试实验室中为 VM 配置自动启动设置 |Microsoft Docs
description: 了解如何在实验室中为 Vm 配置自动启动设置。 此设置允许实验室中的 Vm 按计划自动启动。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: 95f810ba16f358c5aabc35e26294cdb3f8c3cca0
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807909"
---
# <a name="auto-startup-lab-virtual-machines"></a>自动启动实验室虚拟机  
Azure 开发测试实验室允许在实验室中配置虚拟机，使其根据计划自动启动和关闭。 有关配置自动关闭设置的信息，请参阅[在 Azure 开发测试实验室中管理实验室的自动关闭策略](devtest-lab-auto-shutdown.md)。 

与自动关闭不同，当启用策略时，所有 Vm 都包含在其中，自动启动策略需要实验室用户显式选择 VM 并选择加入此计划。 这样一来，您就不会轻易地遇到不需要的 Vm 意外自动启动并导致意外支出的情况。

本文介绍如何为实验室配置自动启动策略。

## <a name="configure-autostart-settings-for-a-lab"></a>为实验室配置自动启动设置 
1. 导航到实验室的主页。 
2. 在左侧菜单中选择 "**配置和策略**"。 

    !["配置和策略" 菜单](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. 在 "**配置和策略**" 页上，执行以下步骤：
    
    1. 对于 **"** **允许将虚拟机计划为自动启动**"，选择 "启用" 以启用此实验室的自动启动功能。 
    2. 为 "**计划开始**" 字段选择开始时间（例如： 8:00:00 AM）。 
    3. 选择要**使用的时区。** 
    4. 选择需要自动启动 Vm**的周日期**。 
    5. 然后，在工具栏上选择 "**保存**" 以保存设置。 

        ![自动启动设置](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > 此策略不会自动将自动启动应用于实验室中的任何虚拟机。 若要选择单独的虚拟机，请**在**"虚拟机" 页上，为该 VM 启用 "**自动启动**"。

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>在实验室中为 VM 启用自动启动
以下过程提供了有关在实验室中选择 VM 的步骤。 

1. 在实验室的主页上，选择 "**我的虚拟机**" 列表中的**VM** 。 

    !["配置和策略" 菜单](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. 在 "**虚拟机**" 页上，从左侧菜单或在 "**计划**" 列表中选择 "**自动启动**"。 

    ![选择自动启动菜单](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. 在 "**自动**启动" 页上，为 "**允许将此虚拟机计划为自动启动**" 选项选择 **"打开**"。

    ![为 VM 启用自动启动](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. 然后，在工具栏上选择 "**保存**" 以保存设置。 


## <a name="next-steps"></a>后续步骤
若要了解有关实验室的配置自动关闭策略，请参阅[在 Azure 开发测试实验室中管理实验室的自动关闭策略](devtest-lab-auto-shutdown.md)
