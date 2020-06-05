---
title: 从自动化帐户启用 Azure 自动化更新管理
description: 本文介绍如何从自动化帐户启用更新管理。
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: a521ff690f59b6beafd1113b177b43193dc7447e
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743994"
---
# <a name="enable-update-management-from-an-automation-account"></a>从自动化帐户启用更新管理

本文介绍如何使用自动化帐户为环境中的 VM 启用[更新管理](automation-update-management.md)功能。 若要大规模启用 Azure VM，必须使用更新管理启用现有 VM。 

> [!NOTE]
> 在启用更新管理时，只有某些区域支持链接 Log Analytics 工作区和自动化帐户。 有关支持的映射对的列表，请参阅[自动化帐户和 Log Analytics 工作区的区域映射](how-to/region-mappings.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 用于管理计算机的[自动化帐户](automation-offering-get-started.md)。
* [虚拟机](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure。

## <a name="enable-update-management"></a>启用更新管理

1. 在自动化帐户中，选择“更新管理”下的“更新管理”。

2. 选择 Log Analytics 工作区和自动化帐户，然后单击“启用”以启用更新管理。 安装最多需要 15 分钟才能完成。

    ![启用更新管理](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>检查作用域配置

更新管理在工作区中使用作用域配置来确定要启用该功能的计算机。 作用域配置是包含一个或多个已保存搜索的组，该组用来将功能的作用域限制为特定计算机。 有关详细信息，请参阅[使用更新管理的作用域配置](automation-scope-configurations-update-management.md)。

## <a name="enable-azure-vms"></a>启用 Azure VM

1. 从自动化帐户中，选择“更新管理”下的“更新管理”。

2. 单击“+ 添加 Azure VM”并从列表中选择一个或多个 VM。 无法启用的虚拟机为灰显，无法选择。 Azure VM 可以位于任何区域中，无论自动化帐户的位置如何。 

3. 单击“启用”以将选定 VM 添加到计算机组为此功能保存的搜索结果。

    ![启用 Azure VM](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>启用非 Azure VM

需要手动添加 Azure 中没有的计算机。 

1. 从自动化帐户中，选择“更新管理”下的“更新管理”。

2. 单击“添加非 Azure 计算机”。 此操作将打开一个新的浏览器窗口，其中包含 [安装和配置适用于 Windows 的 Log Analytics 代理的说明](../azure-monitor/platform/log-analytics-agent.md)以便计算机可以开始报告更新管理操作。 如果你启用当前由 Operations Manager 管理的计算机，则不需要新代理，且工作区信息将输入到现有代理中。

## <a name="enable-machines-in-the-workspace"></a>在工作区中启用计算机

必须将手动安装的计算机或已向工作区报告的计算机添加到 Azure 自动化中，才能启用更新管理。 

1. 从自动化帐户中，选择“更新管理”下的“更新管理”。

2. 选择“管理计算机”。 如果之前选择了“在所有可用和将来的计算机上启用”选项，则“管理计算机”按钮可能灰显 

    ![保存的搜索](media/automation-onboard-solutions-from-automation-account/managemachines.png)

4. 若要为所有可用的计算机启用更新管理，请在“管理计算机”页上选择“在所有可用的计算机上启用”。 此操作禁用单独添加计算机的控件。 此任务会将向工作区进行报告的所有计算机的名称添加到计算机组保存的搜索查询。 选中此项后，此操作将禁用“管理计算机”按钮。

5. 若要为所有可用的计算机和将来的计算机启用该功能，请选择“在所有可用的和将来的计算机上启用”。 此选项将从工作区中删除已保存的搜索和作用域配置，并为向工作区报告的所有 Azure 和非 Azure 计算机启用该功能。 选中此项后，此操作将永久禁用“管理计算机”按钮，因为没有剩余的作用域配置。

6. 如果需要，可以通过重新添加初始保存的搜索来添加作用域配置。 有关详细信息，请参阅[使用更新管理的作用域配置](automation-scope-configurations-update-management.md)。

7. 若要为一台或多台计算机启用该功能，请选择“在所选计算机上启用”并单击每台计算机旁边的“添加”以启用该功能。 此任务会将所选计算机名称添加到计算机组为此功能保存的搜索查询。

## <a name="next-steps"></a>后续步骤

* 若要将更新管理用于 VM，请参阅[管理 Azure VM 的更新和修补程序](automation-tutorial-update-management.md)。
* 有关作用域配置，请参阅[使用更新管理的作用域配置](automation-scope-configurations-update-management.md)。
* 如果不再需要 Log Analytics 工作区，请参阅[为更新管理取消工作区与自动化帐户的链接](automation-unlink-workspace-update-management.md)中的说明。
* 若要从更新管理中删除 VM，请参阅[从更新管理中删除 VM](automation-remove-vms-from-update-management.md)。
* 若要对常规更新管理错误进行故障排除，请参阅[排查更新管理问题](troubleshoot/update-management.md)。
* 若要对 Windows 更新代理的问题进行故障排除，请参阅[排查 Windows 更新代理问题](troubleshoot/update-agent-issues.md)。
* 若要对 Linux 更新代理的问题进行故障排除，请参阅[排查 Linux 更新代理问题](troubleshoot/update-agent-issues-linux.md)。
