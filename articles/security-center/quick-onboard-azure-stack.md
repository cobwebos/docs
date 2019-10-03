---
title: Azure 安全中心快速入门 - 将 Azure Stack 虚拟机载入安全中心 | Microsoft Docs
description: 本快速入门介绍如何在 Azure Stack 虚拟机上预配“Azure 监视、更新和配置管理”虚拟机扩展。
services: security-center
documentationcenter: na
author: pipposera
manager: dsavage
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: fiseraci
ms.openlocfilehash: 1772fd34a2d79b725b2b5ccaa66adb0b251b7e1d
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202848"
---
# <a name="quickstart--onboard-your-azure-stack-virtual-machines-to-security-center"></a>快速入门：将 Azure Stack 虚拟机载入安全中心
载入 Azure 订阅后，可以通过从 Azure Stack 市场添加“Azure 监视、更新和配置管理”虚拟机扩展，启用安全中心来保护 Azure Stack 上运行的虚拟机。 

本快速入门介绍如何在 Azure Stack中运行的 虚拟机（支持 Linux 和 Windows）上添加“Azure 监视、更新和配置管理”虚拟机扩展。 

## <a name="prerequisites"></a>先决条件
若要开始使用安全中心，必须具有 Microsoft Azure 订阅。 如果尚无订阅，可注册[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

在开始学习本快速入门之前，必须在安全中心的标准层上拥有一个 Azure 订阅。 有关升级说明，请参阅[将 Azure 订阅载入到安全中心标准版](security-center-get-started.md)。 可以免费试用安全中心标准层 30 天。 若要了解详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="select-your-workspace-in-azure-security-center"></a>在 Azure 安全中心中选择工作区

1. 登录到 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)。
2. 在 **Microsoft Azure** 菜单上选择“安全中心”  。 此时会打开“安全中心 - 概览”。  

   ![安全中心概述][2]

3. 在“安全中心”主菜单下，选择“入门”  。
4. 选择“入门”  选项卡。

   ![入门][3]

5. 单击“添加新的非 Azure 计算机”下的“配置”   。 此时将显示 Log Analytics 工作区的列表。 该列表包含启用自动预配时由安全中心创建的默认工作区（如果适用）。 请选择此工作区或其他工作区，让 Azure Stack VM 向其报告安全数据。

    ![添加非 Azure 计算机](./media/quick-onboard-windows-computer/non-azure.png)

   将打开“直接代理”边栏选项卡，其中包含一个用于下载代理的链接，以及在配置该代理时要使用的工作区 ID 的密钥。 

   >[!NOTE]
   > 不需要手动下载代理。 在以下步骤中，该代理将安装为 VM 扩展。

6. 在“工作区 ID”  右侧选择复制图标，并将该 ID 粘贴到记事本中。

7. 在“主密钥”  右侧选择复制图标，并将该密钥粘贴到记事本中。

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>将虚拟机扩展添加到现有的 Azure Stack 虚拟机
现在，必须将“Azure 监视、更新和配置管理”虚拟机扩展添加到 Azure Stack 上运行的虚拟机。 

1. 在新的浏览器标签页中，登录到 **Azure Stack** 门户。
2. 转到“虚拟机”页，选择要使用安全中心保护的虚拟机。  有关如何在 Azure Stack 上创建虚拟机的信息，请参阅[适用于 Windows 虚拟机的此快速入门](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal)，或[适用于 Linux 虚拟机的此快速入门](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)。
3. 选择“扩展”。  此时将显示此虚拟机上安装的虚拟机扩展列表。
4. 单击“添加”  选项卡。此时会打开“新建资源”菜单边栏选项卡，其中显示了可用虚拟机扩展的列表。  
5. 选择“Azure 监视、更新和配置管理”扩展，然后单击“创建”。   此时会打开“安装扩展”配置边栏选项卡。 

>[!NOTE]
> 如果在市场中没有看到“Azure Monitor、更新和配置管理”扩展，请联系 Azure Stack 操作员以将其列出  。

6. 在“安装扩展”配置边栏选项卡上，粘贴在前面步骤中复制到记事本的“工作区 ID” 和“工作区密钥(主密钥)”。   
7.  提供所需的配置设置后，请单击“确定”  。
8. 扩展安装完成后，其状态将显示为“预配成功”。  最长可能需要经过一小时，该虚拟机才会显示在安全中心门户中。

有关安装和配置 Windows 代理的详细信息，请参阅[连接 Windows 计算机](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)。

有关如何排查 Linux 代理问题，请参阅[排查 Azure Log Analytics Linux 代理问题](../azure-monitor/platform/agent-linux-troubleshoot.md)。

现在，可以从单个位置监视 Azure VM 和非 Azure 计算机了。 在 Azure 上的安全中心门户中的“计算”下面，可以概览所有 VM 和计算机及其相关建议。  安全中心还会在安全警报中显示针对这些计算机检测到的任何问题。

  ![“计算”边栏选项卡][6]

有两种类型的图标表示在“计算”  边栏选项卡上：

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) 非 Azure 计算机 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure VM（Azure Stack VM 将在此组中显示）

## <a name="clean-up-resources"></a>清理资源
如果不再需要，可以通过 Azure Stack 门户删除虚拟机中的扩展。

删除扩展：

1. 打开 **Azure Stack 门户**。
2. 转到“虚拟机”页，选择要从中删除扩展的虚拟机。 
3. 选择“扩展”，然后选择“Microsoft.EnterpriseCloud.Monitoring”扩展。  
4. 单击“卸载”，然后单击“是”以确认所做的选择。  

## <a name="next-steps"></a>后续步骤
在本快速入门中，我们在 Azure Stack 中运行的虚拟机上预配了“Azure 监视、更新和配置管理”扩展。 若要详细了解如何使用安全中心，请继续阅读教程，了解如何配置安全策略和评估资源的安全性。

> [!div class="nextstepaction"]
> [教程：定义和评估安全策略](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
