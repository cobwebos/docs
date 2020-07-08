---
title: 从 Azure 门户启用 Azure 自动化更新管理
description: 本文介绍如何从 Azure 门户启用更新管理。
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 6eab9b543bfec90c0c338bdc758d690499f083e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84204916"
---
# <a name="enable-update-management-from-the-azure-portal"></a>从 Azure 门户启用“更新管理”

本文介绍如何通过浏览 Azure 门户来为 VM 启用[更新管理](automation-update-management.md)功能。 若要大规模启用 Azure VM，必须使用更新管理启用现有 VM。 

可用于管理 VM 的资源组数量受限于[资源管理器部署限制](../azure-resource-manager/templates/cross-resource-group-deployment.md)。 每个资源管理器部署（不要与更新部署相混淆）限制为五个资源组。 其中保留了两个资源组，以便配置 Log Analytics 工作区、自动化帐户和相关资源。 因此，还剩下三个资源组可供选择用于更新管理进行管理。 此限制仅适用于同时设置，而不适用于可通过自动化功能管理的资源组数。

> [!NOTE]
> 在启用更新管理时，仅支持某些区域链接 Log Analytics 工作区和自动化帐户。 有关受支持的映射对的列表，请参阅[自动化帐户和 Log Analytics 工作区的区域映射](how-to/region-mappings.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 用于管理计算机的[自动化帐户](automation-offering-get-started.md)。
* [虚拟机](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure。

## <a name="enable-update-management"></a>启用更新管理

1. 在 Azure 门户中，导航到“虚拟机”。

2. 使用复选框选择要添加到更新管理的 VM。 一次可以添加最多三个不同资源组的计算机。 Azure VM 可以位于任何区域中，无论自动化帐户的位置如何。

    ![VM 列表](media/automation-onboard-solutions-from-browse/vmlist.png)

    > [!TIP]
    > 使用筛选器控件从不同的订阅、位置和资源组中选择 VM。 可以单击顶部的复选框来选择列表中的所有虚拟机。

    ![启用更新管理](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

3. 单击“服务”，然后为更新管理功能选择“更新管理” 。 

4. 虚拟机的列表已经过筛选，仅显示了位于相同订阅和位置的虚拟机。 如果你的虚拟机位于三个以上资源组中，则会选择前三个资源组。

5. 默认情况下会选择一个现有的 Log Analytics 工作区和自动化帐户。 若要使用不同的 Log Analytics 工作区和自动化帐户，请单击“自定义”以从“自定义配置”页选择它们。 选择 Log Analytics 工作区时，系统会执行一项检查来确定它是否与某个自动化帐户相链接。 如果找到了链接的自动化帐户，则会出现以下屏幕。 完成后单击“确定”。

    ![选择工作区和帐户](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

6. 如果所选的工作区未链接到自动化帐户，则会出现以下屏幕。 选择一个自动化帐户，完成后单击“确定”。

    ![无工作区](media/automation-onboard-solutions-from-browse/no-workspace.png)

7. 取消选择不想启用的任何虚拟机旁边的复选框。 已取消选择无法启用的 VM。

8. 单击“启用”以启用所选功能。 完成安装最多需要 15 分钟时间。

## <a name="next-steps"></a>后续步骤

* 若要将更新管理用于 VM，请参阅[管理 Azure VM 的更新和修补程序](automation-tutorial-update-management.md)。
* 若要对常规更新管理错误进行故障排除，请参阅[排查更新管理问题](troubleshoot/update-management.md)。
* 若要对 Windows 更新代理的问题进行故障排除，请参阅[排查 Windows 更新代理问题](troubleshoot/update-agent-issues.md)。
* 若要排查 Linux 更新代理的问题，请参阅[排查 Linux 更新代理问题](troubleshoot/update-agent-issues-linux.md)。
