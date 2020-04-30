---
title: 将你的 Azure 自动化帐户移到另一个订阅
description: 本文介绍如何将自动化帐户移到另一个订阅。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2dbe7dc171b6e0ec81c99a460a4f997eeb9e27a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681895"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>将你的 Azure 自动化帐户移到另一个订阅

Azure 自动化允许将一些资源转移到新的资源组或订阅。 可以通过 Azure 门户、PowerShell、Azure CLI 或 REST API 移动资源。 若要了解有关此过程的详细信息，请参阅[将资源移到新的资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。

Azure 自动化帐户是可移动的资源之一。 在本文中，你将学习如何将自动化帐户移动到其他资源或订阅。 移动自动化帐户的高级步骤如下：

1. 删除解决方案。
2. 取消链接工作区。
3. 移动自动化帐户。
4. 删除并重新创建运行方式帐户。
5. 重新启用解决方案。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](../automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="remove-solutions"></a>删除解决方案

若要取消工作区与自动化帐户的链接，必须从工作区中删除这些解决方案：

- 更改跟踪和库存
- 更新管理
- 在非工作时间启动/停止 VM

1. 在 Azure 门户中查找资源组。
2. 找到每个解决方案，然后在 "删除资源" 页上单击 "**删除**"。

    ![从 Azure 门户中删除解决方案](../media/move-account/delete-solutions.png)

    如果愿意，可以使用[AzResource](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0) cmdlet 删除解决方案：

    ```azurepowershell-interactive
    $workspaceName = <myWorkspaceName>
    $resourceGroupName = <myResourceGroup>
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
    ```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>删除在非工作时间启动/停止 Vm 的警报规则解决方案

对于 "在非工作时间启动/停止 Vm" 解决方案，还需要删除解决方案创建的警报规则。

1. 在 Azure 门户中，请前往资源组，并选择 "**监视** > **警报** > " "**管理警报规则**"。

![显示管理警报规则选择的警报页](../media/move-account/alert-rules.png)

2. 在 "规则" 页上，应会看到该资源组中配置的警报的列表。 此解决方案创建以下规则：

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. 一次选择一个规则，然后单击 "**删除**" 将其删除。

    ![请求确认删除所选规则的规则页面](../media/move-account/delete-rules.png)

    > [!NOTE]
    > 如果在 "规则" 页上看不到任何警报规则，请将 "**状态**" 字段更改为 "已禁用" 以显示禁用的警报，因为可能已禁用了警报。

4. 删除警报规则时，必须删除为 "在非工作时间启动/停止 Vm" 解决方案通知中创建的操作组。 在 Azure 门户中，选择 "**监视** > " "**警报** > " "**管理操作组**"。

5. 选择**StartStop_VM_Notification**。 

6. 在 "操作组" 页上，选择 "**删除**"。

    ![操作组页面](../media/move-account/delete-action-group.png)

    如果愿意，可以使用[AzActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0) cmdlet 删除操作组：

    ```azurepowershell-interactive
    Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
    ```

## <a name="unlink-your-workspace"></a>取消链接工作区

现在，可以取消链接工作区：

1. 在 Azure 门户中，选择 "**自动化帐户** > **相关资源** > "**链接工作区**。 

2. 选择 "**取消链接工作区**" 以从自动化帐户取消链接工作区。

    ![取消工作区与自动化帐户的链接](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>移动自动化帐户

你现在可以移动你的自动化帐户及其 runbook。 

1. 在 Azure 门户中，浏览到自动化帐户的资源组。 选择 "**移动** > **到另一个订阅**"。

    ![资源组页，移到另一个订阅](../media/move-account/move-resources.png)

2. 选择要移动的资源组中的资源。 确保包含自动化帐户、runbook 和 Log Analytics 工作区资源。

## <a name="recreate-run-as-accounts"></a>重新创建运行方式帐户

[运行方式帐户](../manage-runas-account.md)在 Azure Active Directory 中创建一个服务主体，以便向 Azure 资源进行身份验证。 更改订阅后，Automation 帐户将不再使用现有的运行方式帐户。 重新创建运行方式帐户：

1. 在新订阅中转到自动化帐户，并选择 "**帐户设置**" 下的 "**运行方式帐户**"。 你会看到运行方式帐户现在显示为 "未完成"。

    ![运行方式帐户不完整](../media/move-account/run-as-accounts.png)

2. 使用 "属性" 页上的 "**删除**" 按钮，一次删除一个运行方式帐户。 

    > [!NOTE]
    > 如果你没有创建或查看运行方式帐户的权限，你将看到以下消息： `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.`若要了解配置运行方式帐户所需的权限，请参阅[配置运行方式帐户所需的权限](../manage-runas-account.md#permissions)。

3. 删除运行方式帐户后，请在 " **Azure 运行方式帐户**" 下选择 "**创建**"。 

4. 在 "添加 Azure 运行方式帐户" 页上，选择 "**创建**" 以创建运行方式帐户和服务主体。 

5. 在 Azure 经典运行方式帐户中重复上述步骤。

## <a name="enable-solutions"></a>启用解决方案

重新创建运行方式帐户后，必须重新启用在移动之前删除的解决方案： 

1. 若要打开更改跟踪和清单解决方案，请在自动化帐户中选择 "更改跟踪" 和 "清单"。 选择您移动的 Log Analytics 工作区，然后选择 "**启用**"。

2. 对于更新管理解决方案，请重复步骤1。

    ![重新启用移动的自动化帐户中的解决方案](../media/move-account/reenable-solutions.png)

3. 当你连接现有 Log Analytics 工作区时，将显示与你的解决方案载入的计算机。 若要打开 "在非工作时间启动/停止 Vm" 解决方案，则必须重新部署解决方案。 在 "**相关资源**" 下，选择 "**启动/停止 vm** > "**了解有关的详细信息并启用解决方案** > **创建**以启动部署。

4. 在 "添加解决方案" 页上，选择 Log Analytics 工作区和自动化帐户。

    ![添加解决方案菜单](../media/move-account/add-solution-vm.png)

5. 请参阅[在 Azure 自动化中的非工作时间启动/停止 vm 解决方案](../automation-solution-vm-management.md)中所述的解决方案。

## <a name="verify-the-move"></a>验证移动

移动完成后，验证以下列出的功能是否已启用。 

|功能|计数|疑难解答|
|---|---|---|
|Runbook|Runbook 可以成功运行并连接到 Azure 资源。|[排查 Runbook 问题](../troubleshoot/runbooks.md)
|源代码管理|您可以在源代码管理存储库中运行手动同步。|[源代码管理集成](../source-control-integration.md)|
|更改跟踪和清单|验证你是否看到了计算机的当前清单数据。|[更改跟踪疑难解答](../troubleshoot/change-tracking.md)|
|更新管理|验证你是否看到了计算机并且它们处于正常状态。</br>运行测试软件更新部署。|[更新管理疑难解答](../troubleshoot/update-management.md)|
|共享资源|验证你是否看到了所有共享资源，如[凭据](../shared-resources/credentials.md)、[变量](../shared-resources/variables.md)，等等。|

## <a name="next-steps"></a>后续步骤

若要详细了解如何在 Azure 中移动资源，请参阅[在 azure 中移动资源](../../azure-resource-manager/management/move-support-resources.md)。
