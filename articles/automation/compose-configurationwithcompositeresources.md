---
title: 编写 DSC 配置
description: 本文介绍如何使用复合资源在 Azure Automation State Configuration 中撰写配置。
keywords: powershell dsc, 所需状态配置, powershell dsc azure, 复合资源
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 9c5524d2847f59dda1f2c24f67e1e18f18d49b1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185562"
---
# <a name="compose-dsc-configurations"></a>编写 DSC 配置

当需要使用多个 Desired State Configuration (DSC) 来托管资源时，最佳方法是使用[复合资源](/powershell/scripting/dsc/resources/authoringresourcecomposite)。 复合资源是嵌套的参数化配置，在另一个配置中用作 DSC 资源。 使用复合资源可创建复杂配置，同时允许单独托管和构建基础复合资源。

Azure 自动化可以[导入和编译复合资源](automation-dsc-compile.md)。 将复合资源导入自动化帐户后，可以通过 Azure 门户中的“状态配置(DSC)”功能使用 Azure 自动化状态配置。

## <a name="compose-a-configuration"></a>撰写配置

必须先撰写配置，才能分配从 Azure 门户的复合资源撰写的配置。 通过“State configuration (DSC)”页面上的“配置”或“已编译配置”选项卡的“撰写配置”进行撰写  。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”，并单击自动化帐户的名称。
1. 在“自动化帐户”页上的“配置管理”下，选择“State configuration (DSC)” 。
1. 在“State configuration (DSC)”页上，单击“配置”或“已编译配置”选项卡，然后单击页面顶部菜单中的“撰写配置”  。
1. 在“基本”中，提供一个新的配置名称（必填）并单击所需复合资源行，将其包含在新配置中，然后单击“下一步”或单击“源代码”  。 在下面步骤中，我们选择了 `PSExecutionPolicy` 和 `RenameAndDomainJoin` 复合资源。
   ![撰写配置页面的基本部分的屏幕截图](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. 源代码步骤显示所选复合资源的组合配置。 可以看到所有参数的合并以及将它们传递给复合资源的方式。 查看所有新的源代码后，单击“下一步”或单击“参数” 。
   ![撰写配置页面的源代码部分的屏幕截图](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. 在“参数”步骤中，公开了每个复合资源的参数，以便可以提供值。 如果参数有说明，将显示在参数字段旁边。 如果某个参数为 `PSCredential` 类型，则下拉列表将提供当前自动化帐户中 Credential 对象的列表。 也可以使用“+ 添加凭据”选项。 提供所有必需的参数后，单击“保存和编译”。
   ![撰写配置页面的参数部分的屏幕截图](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

## <a name="submit-the-configuration-for-compilation"></a>提交用于编译的配置

保存新配置后，将它提交以进行编译。 你可以像查看任何导入的配置一样查看编译作业的状态。 有关详细信息，请参阅[查看编译作业](automation-dsc-getting-started.md#view-a-compilation-job)。

编译成功完成后，新配置显示在“已编译配置”选项卡。然后可以使用[为节点重新分配其他节点配置](automation-dsc-getting-started.md#reassign-a-node-to-a-different-node-configuration)中的步骤将配置分配给托管节点。

## <a name="next-steps"></a>后续步骤

- 要了解如何启用节点，请参阅[启用 Azure Automation State Configuration](automation-dsc-onboarding.md)。
- 若要了解如何编译 DSC 配置，以便将它们分配给目标节点，请参阅[在 Azure Automation State Configuration 中编译 DSC 配置](automation-dsc-compile.md)。
- 若要查看在持续部署管道中使用 Azure Automation State Configuration 的示例，请参阅[使用 Chocolatey 设置持续部署](automation-dsc-cd-chocolatey.md)。
- 有关定价信息，请参阅 [Azure Automation State Configuration 定价](https://azure.microsoft.com/pricing/details/automation/)。
- 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation)。
