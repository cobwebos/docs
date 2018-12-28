---
title: 解决 Azure 自动化共享资源的错误
description: 了解如何解决 Azure 自动化共享资源的问题
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ce78c86cdae9a06100fd17d00e0229805e42983b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848453"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>解决共享资源的错误

本文讨论了用来解决在 Azure 自动化中使用共享资源时可能会遇到的问题的解决方案。

## <a name="modules"></a>模块

### <a name="module-stuck-importing"></a>场景：某个模块在导入时停滞

#### <a name="issue"></a>问题

在 Azure 自动化中导入或更新模块时，某个模块停滞在“正在导入”状态。

#### <a name="cause"></a>原因

导入 PowerShell 模块是一个复杂的多步骤过程。 在此过程中可能会有模块无法正确导入。 如果发生此问题，则导入的模块可能会停滞在某个暂时性状态。 若要详细了解此过程，请参阅[导入 PowerShell 模块]( /powershell/developer/module/importing-a-powershell-module#the-importing-process)。

#### <a name="resolution"></a>解决方法

若要解决此问题，必须使用 [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) cmdlet 删除停滞在“正在导入”状态的模块。 然后，可以重试导入模块。

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

## <a name="run-as-accounts"></a>运行方式帐户

### <a name="unable-create-update"></a>场景：无法创建或更新运行方式帐户

#### <a name="issue"></a>问题

在尝试创建或更新运行方式帐户时，接收到类似于以下错误消息的错误：

```error
You do not have permissions to create…
```

#### <a name="cause"></a>原因

没有创建或更新运行方式帐户所需的权限，或者资源在资源组级别锁定。

#### <a name="resolution"></a>解决方法

若要创建或更新运行方式帐户，必须对运行方式帐户使用的各种资源具有适当的权限。 要了解创建或更新运行方式帐户所需的权限，请参阅[运行方式帐户权限](../manage-runas-account.md#permissions)。

如果问题是由锁定引起的，请验证锁定是否可以删除并导航到已锁定的资源，右键单击该锁定并选择“删除”以删除锁定。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。