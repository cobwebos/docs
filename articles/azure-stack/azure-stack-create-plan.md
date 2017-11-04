---
title: "在 Azure 堆栈中创建一个计划 |Microsoft 文档"
description: "作为云管理员，创建一个计划，允许订阅服务器上设置虚拟机。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 30759dca746fd7fd02653556cb105f419f5bf854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-plan-in-azure-stack"></a>在 Azure 堆栈中创建计划

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

[计划](azure-stack-key-features.md)是对一个或多个服务的分组。 作为提供程序，你可以创建计划为你的用户提供。 反过来，你的用户订阅您提供要使用的计划和它们包括的服务。 此示例演示如何创建一个计划，包括计算、 网络和存储资源提供程序。 此计划使订阅服务器能够设置虚拟机。

1. 登录到 Azure 堆栈管理员门户 (https://adminportal.local.azurestack.external)。 输入在步骤 5 的过程中创建的帐户的凭据[运行 PowerShell 脚本](azure-stack-run-powershell-script.md)部分。

2. 若要创建计划和用户可以订阅的优惠，请单击**新建** > **租户提供 + 计划** > **计划**。

   ![](media/azure-stack-create-plan/image01.png)
3. 在**新的计划**边栏选项卡，填写**显示名称**和**资源名称**。 显示名称是用户看到的计划的友好名称。 只有管理员可以查看资源名称。 它是管理员用于使用 Azure 资源管理器资源作为计划的名称。

   ![](media/azure-stack-create-plan/image02.png)
4. 创建一个新**资源组**，或选择一个现有，作为计划的容器。

   ![](media/azure-stack-create-plan/image02a.png)
5. 单击**服务**，选择**Microsoft.Compute**， **Microsoft.Network**，和**Microsoft.Storage**，然后单击**选择**。

   ![](media/azure-stack-create-plan/image03.png)
6. 单击**配额**，单击**Microsoft.Storage （本地）**，然后选择默认配额或单击**创建新配额**以自定义配额。

   ![](media/azure-stack-create-plan/image04.png)
7. 如果你要创建新的配额，输入配额的名称 > 设置的配额值 > 单击**确定**> 单击新的配额的名称。

   ![](media/azure-stack-create-plan/image06.png)
8. 单击**Microsoft.Network （本地）**，然后选择默认配额或单击**创建新配额**以自定义配额。

    ![](media/azure-stack-create-plan/image07.png)
9. 如果你要创建新的配额，请键入配额的名称 > 设置的配额值 > 单击**确定**> 单击新的配额的名称。

    ![](media/azure-stack-create-plan/image08.png)
10. 单击**Microsoft.Compute （本地）**，然后选择默认配额或单击**创建新配额**以自定义配额。

    ![](media/azure-stack-create-plan/image09.png)
11. 如果你要创建新的配额，请键入配额的名称 > 设置的配额值 > 单击**确定**> 单击新的配额的名称。

    ![](media/azure-stack-create-plan/image10.png)
12. 在**配额**边栏选项卡，单击**确定**，然后在**新计划**边栏选项卡，单击**创建**以创建计划。

    ![](media/azure-stack-create-plan/image11.png)
13. 若要查看你的新计划，单击**的所有资源**，然后搜索该计划并单击其名称。

    ![](media/azure-stack-create-plan/image12.png)

### <a name="next-steps"></a>后续步骤
[创建产品/服务](azure-stack-create-offer.md)
