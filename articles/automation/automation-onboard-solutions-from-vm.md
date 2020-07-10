---
title: 从 Azure VM 启用 Azure 自动化更新管理
description: 本文介绍如何从 Azure VM 启用更新管理。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: a1fe48b8bc776faf98f71eb882720e8180a2c573
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186021"
---
# <a name="enable-update-management-from-an-azure-vm"></a>从 Azure VM 启用“更新管理”

本文介绍如何使用 Azure VM 在其他计算机上启用[更新管理](automation-update-management.md)功能。 若要大规模启用 Azure VM，必须使用更新管理启用现有 VM。 

> [!NOTE]
> 在启用更新管理时，只有某些区域支持链接 Log Analytics 工作区和自动化帐户。 有关支持的映射对的列表，请参阅[自动化帐户和 Log Analytics 工作区的区域映射](how-to/region-mappings.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 用于管理计算机的[自动化帐户](./index.yml)。
* [虚拟机](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="enable-the-feature-for-deployment"></a>启用部署功能

1. 在 [Azure 门户](https://portal.azure.com)中，选择“虚拟机”，或在“主页”页上搜索并选择“虚拟机” 。

2. 选择要启用更新管理的 VM。 VM 可以位于任何区域，无论自动化帐户的位置如何。 你

3. 在 VM 页的“操作”下选择“更新管理”。

4. 必须拥有 `Microsoft.OperationalInsights/workspaces/read` 权限才能确定是否为工作区启用了 VM。 若要了解所需的其他权限，请参阅[启用计算机所需的权限](automation-role-based-access-control.md#feature-setup-permissions)。 若要了解如何一次启用多台计算机，请参阅[从自动化帐户启用更新管理](automation-onboard-solutions-from-automation-account.md)。

5. 选择 Log Analytics 工作区和自动化帐户，然后单击“启用”以启用更新管理。 安装最多需要 15 分钟才能完成。 

    ![启用更新管理](media/automation-tutorial-update-management/manageupdates-update-enable.png)

## <a name="next-steps"></a>后续步骤

* 若要将更新管理用于 VM，请参阅[管理 Azure VM 的更新和修补程序](automation-tutorial-update-management.md)。
* 若要对常规更新管理错误进行故障排除，请参阅[排查更新管理问题](troubleshoot/update-management.md)。
* 若要对 Windows 更新代理的问题进行故障排除，请参阅[排查 Windows 更新代理问题](troubleshoot/update-agent-issues.md)。
* 若要排查 Linux 更新代理的问题，请参阅[排查 Linux 更新代理问题](troubleshoot/update-agent-issues-linux.md)。
