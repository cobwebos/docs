---
title: 在 Azure 开发人员测试实验室中配置 VM 的自动启动设置 |微软文档
description: 了解如何在实验室中配置 VM 的自动启动设置。 此设置允许按计划自动启动实验室中的 VM。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807909"
---
# <a name="auto-startup-lab-virtual-machines"></a>自动启动实验室虚拟机  
Azure 开发人员测试实验室允许您根据计划配置实验室中的虚拟机，以便自动启动和关闭。 有关配置自动关机设置的信息，请参阅[在 Azure DevTest 实验室中管理实验室的自动关闭策略](devtest-lab-auto-shutdown.md)。 

与自动关闭不同，在打开策略时包含所有 VM，自动启动策略要求实验室用户显式选择 VM 并选择加入此计划。 这样，您就不会轻易遇到不需要的 VM 意外自动启动并导致意外支出的情况。

本文介绍如何为实验室配置自动启动策略。

## <a name="configure-autostart-settings-for-a-lab"></a>为实验室配置自动启动设置 
1. 导航到实验室的主页。 
2. 选择左侧菜单上的 **"配置"和"策略**"。 

    ![配置和策略菜单](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. 在 **"配置和策略"** 页上，执行以下步骤：
    
    1. 选择 **"打开**"以**允许自动启动虚拟机**，以启用此实验室的自动启动功能。 
    2. 为 **"计划开始"** 字段选择开始时间（例如：上午 8：00）。 
    3. 选择要使用的**时区**。 
    4. 选择需要自动启动 VM 的**一周中的天数**。 
    5. 然后，选择 **"在**工具栏上保存"以保存设置。 

        ![自动启动设置](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > 此策略不会自动应用于实验室中的任何虚拟机。 要**选择单个**虚拟机，请访问虚拟机页面并为该 VM 启用**自动启动**。

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>在实验室中为 VM 启用自动启动
以下过程为您提供了选择将 VM 纳入实验室自动启动策略的步骤。 

1. 在实验室的主页上，选择"**我的虚拟机**"列表中的**VM。** 

    ![配置和策略菜单](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. 在 **"虚拟机**"页上，在左侧菜单或 **"计划"** 列表中选择 **"自动启动**"。 

    ![选择自动启动菜单](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. 在 **"自动启动"** 页上，**On**选择"**允许为此虚拟机计划自动启动**"选项。

    ![为 VM 启用自动启动](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. 然后，选择 **"在**工具栏上保存"以保存设置。 


## <a name="next-steps"></a>后续步骤
要了解实验室的配置自动关闭策略，请参阅在[Azure 开发人员测试实验室中管理实验室的自动关闭策略](devtest-lab-auto-shutdown.md)
