---
title: "Azure 安全中心教程 - 使用 Azure 安全中心来保护资源 | Microsoft Docs"
description: "本教程介绍如何配置恰时 VM 访问策略和应用程序控制策略。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: terrylan
ms.openlocfilehash: cda204f5b54aef239cc0795b62c6fa484a27ebb5
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2018
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>教程：使用 Azure 安装中心来保护资源
安全中心使用访问控制和应用程序控制来阻止恶意活动，限制威胁所造成的危害。 可以通过恰时虚拟机 (VM) 访问拒绝对 VM 的永久性访问，减少自己遭受攻击的可能性。 仅在需要的情况下，才提供对 VM 的受控且经过审核的访问权限。 自适应应用程序控制可以对哪些应用程序能够在 VM 上运行进行控制，从而强化 VM 对抗恶意软件的能力。 安全中心使用机器学习来分析在 VM 中运行的进程，帮助你运用此智能来应用允许列表规则。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 配置恰时 VM 访问策略
> * 配置应用程序控制策略

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="prerequisites"></a>先决条件
若要逐步执行本教程中介绍的功能，你必须位于安全中心的“标准”定价层。 可以尝试安全中心标准版，头 60 天免费。 快速入门教程“[将 Azure 订阅载入到安全中心标准版](security-center-get-started.md)”详细介绍了如何升级到标准版。

## <a name="manage-vm-access"></a>管理 VM 访问权限
恰时 VM 访问可以用来锁定发往 Azure VM 的入站流量，降低遭受攻击的可能性，同时在需要时还允许轻松连接到 VM。

恰时 VM 访问为预览版。

管理端口不需要始终处于打开状态。 它们只需要在特定的时间打开，例如在你连接到 VM 来执行管理或维护任务时。 当启用了恰时功能时，安全中心会使用网络安全组 (NSG) 规则，这些规则将限制对管理端口的访问以使其不会成为攻击者的目标。

1. 在安全中心主菜单的“高级云防御”下选择“恰时 VM 访问”。

  ![恰时 VM 访问][1]

  “恰时 VM 访问”提供 VM 的状态信息：

  - **已配置** - 已配置为支持恰时 VM 访问的 VM。
  - **推荐** - 可以支持恰时 VM 访问但尚未配置此功能的 VM。
  - **不推荐** - 导致不推荐某个 VM 的可能原因有：

    - 缺少 NSG - 恰时解决方案需要 NSG 准备就绪。
    - 经典 VM - 安全中心恰时 VM 访问当前仅支持通过 Azure 资源管理器部署的 VM。
    - 其他 - 如果在订阅或资源组的安全策略中未开启恰时解决方案，或者 VM 缺少公共 IP 且没有已准备就绪的 NSG，则该 VM 将位于此类别中。

2. 选择一个建议的 VM，然后单击“在 1 个 VM 上启用 JIT”，为该 VM 配置恰时策略：

  可以保存安全中心建议的默认端口，也可以添加并配置新的端口，以便在其上启用恰时解决方案。 本教程可选择“添加”来添加端口。

  ![添加端口配置][2]

3. 在“添加端口配置”下，请标识：

  - 端口
  - 协议类型
  - 允许的源 IP - 允许根据批准的请求获取访问权限的 IP 范围
  - 最大请求时间 - 特定端口可以处于打开状态的最大时间范围

4. 选择“确定”进行保存。

## <a name="harden-vms-against-malware"></a>强化 VM 对抗恶意软件的能力
可以通过自适应应用程序控制来定义一组应用程序，允许这些应用程序在配置的资源组上运行。这样有很多好处，其中之一就是有助于强化 VM 对抗恶意软件的能力。 安全中心使用机器学习来分析在 VM 中运行的进程，帮助你运用此智能来应用允许列表规则。

自适应应用程序控制为预览版。 此功能仅适用于 Windows 计算机。

1. 返回到安全中心主菜单。 在“高级云防御”下选择“自适应应用程序控制”。

   ![自适应应用程序控制][3]

  “资源组”部分包含三个选项卡：

  - **已配置**：所含 VM 已配置了应用程序控制的资源组的列表。
  - **已建议**：建议对其实施应用程序控制的资源组的列表。
  - **无建议**：所含 VM 没有任何应用程序控制建议的资源组的列表。 例如，其上的应用程序始终变化，尚未达到稳定状态的 VM。

2. 选择“已建议”选项卡会出现一个列表，其中包含的资源组带有应用程序控制建议。

  ![应用程序控制建议][4]

3. 选择一个资源组，打开“创建应用程序控制规则”选项。 在“选择 VM”中查看建议的 VM 的列表，取消选中不需向其应用应用程序控制的 VM。 在“选择适用于允许列表规则的进程”中查看建议的应用程序的列表，取消选中不需向其应用应用程序控制的应用程序。 此列表包括：

  - **名称**：完整的应用程序路径
  - **进程数**：每个路径中驻留的应用程序数
  - **常用**：“是”表示这些进程已在此资源组的大多数 VM 上执行
  - **可利用**：警告图标表示攻击者可能会利用应用程序来规避应用程序允许列表。 建议在这些应用程序审批前查看它们。

4. 选择完以后，请选择“创建”。

## <a name="clean-up-resources"></a>清理资源
本系列中的其他快速入门和教程是在本快速入门的基础上制作的。 如果打算继续学习后续的快速入门和教程，请继续运行“标准”层并让自动预配保持启用状态。 如果不打算继续或想要返回到“免费”层，请执行以下操作：

1. 返回到安全中心主菜单，选择“安全策略”。
2. 选择要返回到“免费”层的订阅或策略。 此时会打开“安全策略”。
3. 在“策略组件”下选择“定价层”。
4. 选择“免费”，将订阅从“标准”层更改为“免费”层。
5. 选择“保存”。

如果希望禁用自动预配，请执行以下操作：

1. 返回到安全中心主菜单，选择“安全策略”。
2. 选择希望禁用自动设置的订阅。
3. 在“安全策略 - 数据收集”下的“载入”下选择“关闭”，禁用自动预配。
4. 选择“保存”。

>[!NOTE]
> 禁用自动设置不会从已预配代理的 Azure VM 中删除 Microsoft Monitoring Agent。 禁用自动设置会限制对资源的安全监视。
>

## <a name="next-steps"></a>后续步骤
本教程介绍了如何限制威胁所造成的危害，方法是：

> [!div class="checklist"]
> * 配置恰时 VM 访问策略，仅在需要的时候提供受控且经过审核的 VM 访问权限
> * 配置自适应应用程序控制策略，对哪些应用程序可以在 VM 上运行进行控制

若要了解如何响应安全事件，请转到下一教程。

> [!div class="nextstepaction"]
> [教程：响应安全事件](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
