---
title: 解决 Azure 自动化共享资源的错误
description: 了解如何解决 Azure 自动化共享资源的问题
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/05/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 385d2969e65647ab0b5c5e21c07b127104587e7e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263412"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>解决共享资源的错误

本文讨论了用来解决在 Azure 自动化中使用共享资源时可能会遇到的问题的解决方案。

## <a name="modules"></a>模块

### <a name="module-stuck-importing"></a>场景：某个模块在导入时停滞

#### <a name="issue"></a>问题

在 Azure 自动化中导入或更新你的模块时，发现某个模块停滞在“正在导入”状态。

#### <a name="error"></a>错误

导入 PowerShell 模块是一个复杂的多步骤过程。 在此过程中可能会有模块无法正确导入。 如果发生此情况，则你导入的模块可能会停滞在某个暂时性状态。 若要详细了解此过程，请参阅[导入 PowerShell 模块]( /powershell/developer/module/importing-a-powershell-module#the-importing-process)。

#### <a name="resolution"></a>解决方法

若要解决此问题，必须使用 [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) cmdlet 删除停滞在“正在导入”状态的模块。 然后，可以重试导入模块。

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。