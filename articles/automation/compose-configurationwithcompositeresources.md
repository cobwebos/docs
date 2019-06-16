---
title: 使用复合资源在 Azure Automation State Configuration (DSC) 中编译 DSC 配置
description: 了解如何使用复合资源在 Azure Automation State Configuration (DSC) 中撰写配置
keywords: powershell dsc, 所需状态配置, powershell dsc azure, 复合资源
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/21/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 64588829cec964e52dcb44465869e0090f36f9f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61303940"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>使用复合资源在 Azure Automation State Configuration (DSC) 中编译 DSC 配置

当需要使用多个单个的所需状态配置 (DSC) 配置来托管资源时，最佳方法是使用[复合资源](/powershell/dsc/authoringresourcecomposite)。 复合资源是嵌套的参数化配置，在另一个配置中用作 DSC 资源。 这允许创建复杂配置，同时允许单独托管和构建基础复合资源（参数化配置）。

Azure 自动化可以[导入和编译复合资源](automation-dsc-compile.md#composite-resources)。 将复合资源导入自动化帐户后，即可在“状态配置 (DSC)”页面中使用撰写配置经验   。

## <a name="composing-a-configuration-from-composite-resources"></a>从复合资源撰写配置

必须先撰写配置，才能分配从 Azure 门户的复合资源撰写的配置。 使用“状态配置 (DSC)”页面上的“配置”或“已编译配置”选项卡的“撰写配置”，即可完成此操作     。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”  ，并单击自动化帐户的名称。
1. 在“自动化帐户”页上的“配置管理”下，选择“State configuration (DSC)”。   
1. 在“状态配置 (DSC)”页面上，单击“配置”或“已编译配置”选项卡，然后单击页面顶部菜单中的“撰写配置”     。
1. 在“基本”中，提供一个新的配置名称（必填）并单击所需复合资源行，将其包含在新配置中，然后单击“下一步”或单击“源代码”    。 在下面步骤中，我们选择了 PSExecutionPolicy 和 RenameAndDomainJoin 复合资源   。
   ![撰写配置页面的基本部分的屏幕截图](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. 源代码步骤显示所选复合资源的组合配置  。 可以看到所有参数的合并以及将它们传递给复合资源的方式。 查看所有新的源代码后，单击“下一步”或单击“参数”   。
   ![撰写配置页面的源代码部分的屏幕截图](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. 在“参数”中，公开了每个复合资源所具有的参数，以便可以提供它们  。 如果参数有说明，将显示在参数字段旁边。 如果某个字段是 PSCredential 类型的参数，要配置的下拉框会提供当前自动化帐户中 Credential 对象的列表   。 也可以使用“+ 添加凭据”选项  。 提供所有必需的参数后，单击“保存和编译”  。
   ![撰写配置页面的参数部分的屏幕截图](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

保存新配置后，将它提交以进行编译。 可以像任何导入的配置一样查看编译作业的状态。 有关详细信息，请参阅[查看编译作业](automation-dsc-getting-started.md#viewing-a-compilation-job)。

编译成功完成后，新配置显示在“已编译配置”选项卡  。显示在此选项卡中后，可以使用[为节点重新分配其他节点配置](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration)中的步骤将其分配给托管节点。

## <a name="next-steps"></a>后续步骤

- 有关入门信息，请参阅 [Azure Automation State Configuration 入门](automation-dsc-getting-started.md)
- 要了解如何登记节点，请参阅[登记由 Azure Automation State Configuration 管理的计算机](automation-dsc-onboarding.md)
- 若要了解如何编译 DSC 配置，以便将它们分配给目标节点，请参阅[在 Azure Automation State Configuration 中编译配置](automation-dsc-compile.md)
- 有关 PowerShell cmdlet 参考，请参阅 [Azure Automation State Configuration cmdlet](/powershell/module/azurerm.automation/#automation)
- 有关定价信息，请参阅 [Azure Automation State Configuration 定价](https://azure.microsoft.com/pricing/details/automation/)
- 若要查看在持续部署管道中使用 Azure Automation State Configuration 的示例，请参阅[使用 Azure Automation State Configuration 和 Chocolatey 进行持续部署](automation-dsc-cd-chocolatey.md)
