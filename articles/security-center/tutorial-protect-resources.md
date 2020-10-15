---
title: 访问及应用程序控制教程 - Azure 安全中心
description: 本教程介绍如何配置实时 VM 访问策略和应用程序控制策略。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: 89d04588d07407be5dbd7ddbafd6543ed461462e
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945713"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>教程：使用 Azure 安全中心来保护资源
安全中心使用访问控制和应用程序控制来阻止恶意活动，限制威胁所造成的危害。 可以通过实时 (JIT) 虚拟机 (VM) 访问拒绝对 VM 的永久性访问，减少自己遭受攻击的可能性。 仅在需要的情况下，才提供对 VM 的受控且经过审核的访问权限。 自适应应用程序控制可以对哪些应用程序能够在 VM 上运行进行控制，从而强化 VM 对抗恶意软件的能力。 安全中心使用机器学习来分析在 VM 中运行的进程，帮助你运用此智能来应用允许列表规则。

在本教程中，将了解如何：

> [!div class="checklist"]
> * 配置实时 VM 访问策略
> * 配置应用程序控制策略

## <a name="prerequisites"></a>先决条件
若要逐步执行本教程中介绍的功能，需要启用 Azure Defender。 可以免费试用 Azure Defender。 有关详细信息，请参阅[试用 Azure Defender](security-center-pricing.md)。

## <a name="manage-vm-access"></a>管理 VM 访问权限
JIT VM 访问可以用来锁定发往 Azure VM 的入站流量，降低遭受攻击的可能性，同时在需要时还允许轻松连接到 VM。

管理端口不需要始终处于打开状态。 它们只需要在特定的时间打开，例如在你连接到 VM 来执行管理或维护任务时。 如果启用了实时功能，安全中心会使用网络安全组 (NSG) 规则，这些规则将限制对管理端口的访问以使其不会成为攻击者的目标。

按照[使用实时访问保护管理端口](security-center-just-in-time.md)指南进行操作。

## <a name="harden-vms-against-malware"></a>强化 VM 对抗恶意软件的能力
可以通过自适应应用程序控制来定义一组应用程序，允许这些应用程序在配置的资源组上运行。这样有很多好处，其中之一就是有助于强化 VM 对抗恶意软件的能力。 安全中心使用机器学习来分析在 VM 中运行的进程，帮助你运用此智能来应用允许列表规则。

按照[使用自适应应用程序控制来减少计算机的攻击面](security-center-adaptive-application.md)指南进行操作。

## <a name="next-steps"></a>后续步骤
本教程介绍了如何限制威胁所造成的危害，方法是：

> [!div class="checklist"]
> * 配置实时 VM 访问策略，仅在需要的时候提供受控且经过审核的 VM 访问权限
> * 配置自适应应用程序控制策略，对哪些应用程序可以在 VM 上运行进行控制

若要了解如何响应安全事件，请转到下一教程。

> [!div class="nextstepaction"]
> [教程：响应安全事件](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
