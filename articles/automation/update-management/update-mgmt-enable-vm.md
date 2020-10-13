---
title: 从 Azure VM 启用 Azure 自动化更新管理
description: 本文介绍如何从 Azure VM 启用更新管理。
services: automation
ms.date: 09/16/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: dead4649635bebd9f73f79937dea1d24565f273e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327778"
---
# <a name="enable-update-management-from-an-azure-vm"></a>从 Azure VM 启用“更新管理”

本文介绍如何使用 Azure VM 在其他计算机上启用[更新管理](update-mgmt-overview.md)功能。 若要大规模启用 Azure VM，必须使用更新管理启用现有 VM。

> [!NOTE]
> 在启用更新管理时，只有某些区域支持链接 Log Analytics 工作区和自动化帐户。 有关支持的映射对的列表，请参阅[自动化帐户和 Log Analytics 工作区的区域映射](../how-to/region-mappings.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 用于管理计算机的[自动化帐户](../index.yml)。
* [虚拟机](../../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="enable-the-feature-for-deployment"></a>启用相关功能来进行部署

1. 在 [Azure 门户](https://portal.azure.com)中，选择“虚拟机”，或在“主页”页上搜索并选择“虚拟机” 。

2. 选择要启用更新管理的 VM。 VM 可以位于任何区域，无论自动化帐户的位置如何。 你

3. 在 "VM" 页的 " **操作**" 下，选择 " **来宾 + 主机更新**"。

    ![在左侧窗格中选择 "来宾 + 主机更新"](media/update-mgmt-enable-vm/select-guest-and-os-updates.png)

4. 必须拥有 `Microsoft.OperationalInsights/workspaces/read` 权限才能确定是否为工作区启用了 VM。 若要了解所需的其他权限，请参阅[启用计算机所需的权限](../automation-role-based-access-control.md#feature-setup-permissions)。 若要了解如何一次启用多台计算机，请参阅[从自动化帐户启用更新管理](update-mgmt-enable-automation-account.md)。

5. 在 "启用更新管理" 页上，选择 "Log Analytics" 工作区和自动化帐户，然后单击 " **启用** " 以启用更新管理。 启用更新管理后，可能需要大约 15 分钟才能查看 VM 的更新评估。

    ![启用更新管理](media/update-mgmt-enable-vm/enable-update-management.png)

## <a name="next-steps"></a>后续步骤

* 若要将更新管理用于 VM，请参阅[管理 Azure VM 的更新和修补程序](update-mgmt-manage-updates-for-vm.md)。

* 若要对常规更新管理错误进行故障排除，请参阅[排查更新管理问题](../troubleshoot/update-management.md)。
