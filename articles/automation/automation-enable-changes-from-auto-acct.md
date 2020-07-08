---
title: 从自动化帐户启用 Azure 自动化更改跟踪和库存
description: 本文介绍了如何从自动化帐户启用更改跟踪和库存。
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 13f17ed9d165e368d2e9d9cde694408b600006cc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84171117"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>从自动化帐户启用更改跟踪和清单

本文介绍了如何使用自动化帐户为环境中的 VM 启用[更改跟踪和库存](change-tracking.md)功能。 若要大规模启用 Azure VM，必须使用更改跟踪和库存启用现有 VM。 

> [!NOTE]
> 在启用更改跟踪和库存时，只有某些区域支持链接 Log Analytics 工作区和自动化帐户。 有关支持的映射对的列表，请参阅[自动化帐户和 Log Analytics 工作区的区域映射](how-to/region-mappings.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 用于管理计算机的[自动化帐户](automation-offering-get-started.md)。
* [虚拟机](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure。

## <a name="enable-change-tracking-and-inventory"></a>启用更改跟踪和库存

1. 导航到你的自动化帐户，在“配置管理”下选择“库存”或“更改跟踪”。  

2. 选择 Log Analytics 工作区和自动化帐户，然后单击“启用”以启用更改跟踪和库存。 完成设置最多需要 15 分钟时间。

    ![启用更改跟踪和库存](media/automation-enable-changes-from-auto-acct/onboardsolutions.png)

## <a name="enable-azure-vms"></a>启用 Azure VM

1. 在自动化帐户中，选择“配置管理”下的“库存”或“更改跟踪”  。

2. 单击“+ 添加 Azure VM”并从列表中选择一个或多个 VM。 无法启用的虚拟机将灰显，无法选择。 Azure VM 可以位于任何区域中，无论自动化帐户位于哪里。 

3. 单击“启用”以将选定 VM 添加到计算机组为此功能保存的搜索结果。 有关详细信息，请参阅[Limit 更改跟踪和清点部署范围](automation-scope-configurations-change-tracking.md)。

    ![启用 Azure VM](media/automation-enable-changes-from-auto-acct/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>启用非 Azure VM

需要手动添加 Azure 中没有的计算机。 

1. 在你的自动化帐户中，在“配置管理”下选择“库存”或“更改跟踪”  。

2. 单击“添加非 Azure 计算机”。 此操作将打开一个新的浏览器窗口，其中包含 [安装和配置适用于 Windows 的 Log Analytics 代理的说明](../azure-monitor/platform/log-analytics-agent.md)以便计算机可以开始报告更改跟踪和库存操作。 如果你启用当前由 Operations Manager 管理的计算机，则不需要新代理，且工作区信息将输入到现有代理中。

## <a name="enable-machines-in-the-workspace"></a>在工作区中启用计算机

必须将手动安装的计算机或已向工作区报告的计算机添加到 Azure 自动化中，才能启用更改跟踪和库存。 

1. 在你的自动化帐户中，在“配置管理”下选择“库存”或“更改跟踪”  。

2. 选择“管理计算机”。 如果之前选择了“在所有可用的和将来的计算机上启用”选项，则“管理计算机”按钮可能灰显 

    ![保存的搜索](media/automation-enable-changes-from-auto-acct/managemachines.png)

3. 若要为所有可用的计算机启用更改跟踪和库存，请在“管理计算机”页上选择“在所有可用的计算机上启用”。 此操作禁用单独添加计算机的控件。 此任务会将向工作区进行报告的所有计算机的名称添加到计算机组保存的搜索查询。 选中此项后，此操作将禁用“管理计算机”按钮。

4. 若要为所有可用的计算机和将来的计算机启用该功能，请选择“在所有可用的和将来的计算机上启用”。 此选项将从工作区中删除已保存的搜索和作用域配置，并为向工作区报告的所有 Azure 和非 Azure 计算机启用该功能。 选中此项后，此操作将永久禁用“管理计算机”按钮，因为没有剩余的作用域配置。

5. 如果需要，可以通过重新添加初始的已保存搜索来添加作用域配置。 有关详细信息，请参阅[Limit 更改跟踪和清点部署范围](automation-scope-configurations-change-tracking.md)。

6. 若要为一台或多台计算机启用该功能，请选择“在所选计算机上启用”并单击每台计算机旁边的“添加”以启用该功能。 此任务会将所选计算机名称添加到计算机组为此功能保存的搜索查询。

## <a name="next-steps"></a>后续步骤

* 若要使用该功能，请参阅[管理更改跟踪和清单](change-tracking-file-contents.md)。
* 若要排查该功能的常见问题，请参阅[排查更改跟踪和清单问题](troubleshoot/change-tracking.md)。