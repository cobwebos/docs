---
title: 从自动化帐户启用 Azure 自动化更新管理
description: 本文介绍如何从自动化帐户启用更新管理。
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 930861c61843c5963c83d8fa6dc1efdce20853f4
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450023"
---
# <a name="enable-update-management-from-an-automation-account"></a>从自动化帐户启用更新管理

本文介绍如何使用自动化帐户为环境中的 VM 启用[更新管理](update-mgmt-overview.md)功能。 若要大规模启用 Azure VM，必须使用更新管理启用现有 VM。

> [!NOTE]
> 在启用更新管理时，只有某些区域支持链接 Log Analytics 工作区和自动化帐户。 有关支持的映射对的列表，请参阅[自动化帐户和 Log Analytics 工作区的区域映射](../how-to/region-mappings.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 用于管理计算机的[自动化帐户](../index.yml)。
* [虚拟机](../../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="enable-update-management"></a>启用更新管理

1. 在自动化帐户中，选择“更新管理”下的“更新管理”。

2. 选择 Log Analytics 工作区和自动化帐户，并选择 "**启用**" 以启用更新管理。 安装最多需要 15 分钟才能完成。

    ![启用更新管理](media/update-mgmt-enable-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>启用 Azure VM

1. 从自动化帐户中，选择“更新管理”下的“更新管理”。

2. 选择 " **+ 添加 Azure vm** "，然后从列表中选择一个或多个 vm。 无法启用的虚拟机将灰显，无法选择。 Azure VM 可以位于任何区域中，无论自动化帐户位于哪里。

3. 选择 "**启用**" 将所选 vm 添加到 "计算机" 组 "已保存的搜索" 功能。

    ![启用 Azure VM](media/update-mgmt-enable-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>启用非 Azure VM

需要手动添加 Azure 中没有的计算机。

1. 从自动化帐户中，选择“更新管理”下的“更新管理”。

2. 选择 "**添加非 Azure 计算机**"。 此操作将打开一个新的浏览器窗口，其中包含[用于安装和配置 Windows Log Analytics agent 的说明](../../azure-monitor/platform/log-analytics-agent.md)，以便计算机可以开始向更新管理报告。 如果要启用 Operations Manager 当前管理的计算机，则不需要新的代理。 工作区信息将添加到代理配置。

## <a name="enable-machines-in-the-workspace"></a>在工作区中启用计算机

必须将手动安装的计算机或已向工作区报告的计算机添加到 Azure 自动化中，才能启用更新管理。

1. 从自动化帐户中，选择“更新管理”下的“更新管理”。

2. 选择“管理计算机”。 如果之前选择了“在所有可用的和将来的计算机上启用”选项，则“管理计算机”按钮可能灰显 

    ![保存的搜索](media/update-mgmt-enable-automation-account/managemachines.png)

3. 若要为所有可用的计算机启用更新管理，请在“管理计算机”页上选择“在所有可用的计算机上启用”。 此操作禁用单独添加计算机的控件。 此任务会将向工作区进行报告的所有计算机的名称添加到计算机组保存的搜索查询。 选中此项后，此操作将禁用“管理计算机”按钮。

4. 若要为所有可用的计算机和将来的计算机启用该功能，请选择“在所有可用的和将来的计算机上启用”。 此选项将从工作区中删除已保存的搜索和作用域配置，并为向工作区报告的所有 Azure 和非 Azure 计算机启用该功能。 选中此项后，此操作将永久禁用“管理计算机”按钮，因为没有剩余的作用域配置。

5. 如果需要，可以通过重新添加初始的已保存搜索来添加作用域配置。 有关详细信息，请参阅[限制更新管理的部署范围](update-mgmt-scope-configuration.md)。

6. 若要为一个或多个计算机启用此功能，请选择 "**在选定计算机上启用**"，然后选择 "在每台计算机上**添加**"。 此任务会将所选计算机名称添加到计算机组为此功能保存的搜索查询。

## <a name="next-steps"></a>后续步骤

* 若要将更新管理用于 Vm，请参阅[管理 vm 的更新和修补程序](update-mgmt-manage-updates-for-vm.md)。

* 若要对常规更新管理错误进行故障排除，请参阅[排查更新管理问题](../troubleshoot/update-management.md)。