---
title: Azure 自动化状态配置入门
description: Azure 自动化状态配置中最常见任务的说明和示例
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 45d56daee20d95230f77db249028883165acb951
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995780"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Azure 自动化状态配置入门

本文详细介绍了如何使用 Azure 自动化状态配置执行最常见的任务，例如创建、导入和编译配置，使计算机能够管理和查看报表。 有关概述状态配置，请参阅[状态配置概述](automation-dsc-overview.md)。 有关 Desired State Configuration (DSC) 文档，请参阅 [Windows PowerShell Desired State Configuration 概述](/powershell/scripting/dsc/overview/overview)。

如果希望在未按照本文中所述的步骤的情况下设置示例环境，可以使用[Azure 自动化托管节点模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration)。 此模板设置一个完整的状态配置（DSC）环境，包括由状态配置（DSC）管理的 Azure VM。

## <a name="prerequisites"></a>先决条件

若要完成本文中的示例，需要具备以下条件：

- 一个 Azure 自动化帐户。 有关如何创建 Azure 自动化运行方式帐户的说明，请参阅 [Azure 运行方式帐户](automation-sec-configure-azure-runas-account.md)。
- 一个运行[支持的操作系统](automation-dsc-overview.md#operating-system-requirements)的 Azure 资源管理器 VM（非经典）。 如需创建 VM 的说明，请参阅[在 Azure 门户中创建第一个 Windows 虚拟机](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="create-a-dsc-configuration"></a>创建 DSC 配置

将创建一个简单的 [DSC 配置](/powershell/scripting/dsc/configurations/configurations)，以确保存在（或不存在）**Web-Server** Windows 功能 (IIS)，具体取决于分配节点的方式。

1. 启动 [VSCode](https://code.visualstudio.com/docs)（或任何文本编辑器）。
1. 键入以下文本：

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. 将该文件另存为**TestConfig**。

此配置在每个节点块（node.js[资源](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource)）中调用一个资源。 此资源可确保存在或缺少**Web 服务器**功能。

## <a name="import-a-configuration-into-azure-automation"></a>将配置导入 Azure 自动化

接下来，会将配置导入自动化帐户。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”  ，并单击自动化帐户的名称。
1. 在“自动化帐户”页上的“配置管理”下，选择“State configuration (DSC)”。********
1. 在 "状态配置（DSC）" 页上，单击 "**配置**" 选项卡，然后单击 "**添加**"。
1. 在 "导入配置" 窗格上， `TestConfig.ps1`浏览到计算机上的文件。

   ![**导入配置**边栏选项卡的屏幕截图](./media/automation-dsc-getting-started/AddConfig.png)

1. 单击 **“确定”** 。

## <a name="view-a-configuration-in-azure-automation"></a>查看 Azure 自动化中的配置

在导入配置以后，即可在 Azure 门户中查看该配置。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”  ，并单击自动化帐户的名称。
1. 在 "自动化帐户" 页上，选择 "**配置管理**" 下的**状态配置（DSC）** 。
1. 在 "状态配置（DSC）" 页上，单击 "**配置**" 选项卡，然后单击 " **TestConfig**"。 这是你在上一过程中导入的配置的名称。
1. 在 TestConfig 配置窗格中，单击 "**查看配置源**"。

   ![“TestConfig 配置”边栏选项卡的屏幕快照](./media/automation-dsc-getting-started/ViewConfigSource.png)

   此时将打开 TestConfig 配置源窗格，其中显示了配置的 PowerShell 代码。

## <a name="compile-a-configuration-in-azure-automation"></a>在 Azure 自动化中编译配置

在将所需状态应用到某个节点之前，必须将定义该状态的 DSC 配置编译成一个或多个节点配置（MOF 文档），然后放置在自动化 DSC 拉取服务器上。 有关在状态配置（DSC）中编译配置的更详细说明，请参阅[在 Azure 自动化状态配置中编译配置](automation-dsc-compile.md)。
有关编译配置的详细信息，请参阅 [DSC 配置](/powershell/scripting/dsc/configurations/configurations)。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”****，并单击自动化帐户的名称。
1. 在“自动化帐户”页上的“配置管理”下，单击“State configuration (DSC)”。********
1. 在 "状态配置（DSC）" 页上，单击 "**配置**" 选项卡，然后单击 " **TestConfig**"。 这是以前导入的配置的名称。
1. 在 TestConfig 配置窗格中，单击 "**编译**"，然后单击 **"是"**。 此时会启动编译作业。

   ![“TestConfig 配置”页的屏幕截图，其中突出显示“编译”按钮](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> 在 Azure 自动化中编译配置时，会自动将任何创建的节点配置 MOF 文件部署到请求服务器。

## <a name="view-a-compilation-job"></a>查看编译作业

在启动编译后，可以在 "**配置**" 页的 "**编译作业**" 磁贴中查看它。 “编译作业”**** 磁贴显示当前正在运行的、已完成的以及失败的作业。 当你打开 "编译作业" 窗格时，它会显示有关该作业的信息，包括遇到的任何错误或警告、配置中使用的输入参数以及编译日志。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”****，并单击自动化帐户的名称。
1. 在“自动化帐户”页上的“配置管理”下，单击“State configuration (DSC)”。********
1. 在 "状态配置（DSC）" 页上，单击 "**配置**" 选项卡，然后单击 " **TestConfig**"。 这是以前导入的配置的名称。
1. 在 "**编译作业**" 下，选择要查看的编译作业。 此时会打开 "编译作业" 窗格，其中标记了编译作业的开始日期。

   ![“编译作业”页的屏幕截图](./media/automation-dsc-getting-started/CompilationJob.png)

1. 单击 "编译作业" 窗格中的任意磁贴可查看有关该作业的更多详细信息。

## <a name="view-node-configurations"></a>查看节点配置

成功完成编译作业以后，会创建一个或多个新的节点配置。 节点配置是一个 MOF 文档，该文档部署到拉取服务器上，可供一个或多个节点拉取和应用。 可以在 "状态配置（DSC）" 页上查看自动化帐户中的节点配置。 节点配置具有形式`ConfigurationName.NodeName`的名称。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”****，并单击自动化帐户的名称。
1. 在“自动化帐户”页上的“配置管理”下，单击“State configuration (DSC)”。********
1. 在“State configuration (DSC)”页上，单击“编译的配置”选项卡。****

   ![“编译的配置”选项卡屏幕截图](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="enable-an-azure-resource-manager-vm-for-management-with-state-configuration"></a>启用 Azure 资源管理器 VM 以便使用状态配置进行管理

你可以使用状态配置来管理 Azure Vm （经典和资源管理器）、本地 Vm、Linux 计算机、AWS Vm 和本地物理计算机。 本文介绍如何仅启用 Azure 资源管理器 Vm。 有关启用其他类型的计算机的信息，请参阅[启用虚拟机以通过 Azure Automation 状态配置进行管理](automation-dsc-onboarding.md)。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”****，并单击自动化帐户的名称。
1. 在“自动化帐户”页上的“配置管理”下，单击“State configuration (DSC)”。********
1. 在 "状态配置（DSC）" 页上，选择 "**节点**" 选项卡，然后单击 " **+ 添加**"。

   ![“DSC 节点”页的屏幕截图，其中突出显示“添加 Azure VM”按钮](./media/automation-dsc-getting-started/OnboardVM.png)

1. 在 "虚拟机" 窗格中，选择你的 VM。
1. 在 "虚拟机详细信息" 窗格中，单击 " **+ 连接**"。

   > [!IMPORTANT]
   > VM 必须是运行[受支持的操作系统](automation-dsc-overview.md#operating-system-requirements)的 AZURE 资源管理器 vm。

2. 在 "注册" 页上的 "**节点配置名称**" 字段中，选择要应用于 VM 的节点配置的名称。 可以选择在此时提供名称。 启用节点后，可以更改分配的节点配置。

3. 选中“根据需要重新启动节点”****，并单击“确定”****。

   ![“注册”边栏选项卡的屏幕快照](./media/automation-dsc-getting-started/RegisterVM.png)

   指定的节点配置将按为**配置模式频率**提供的值指定的时间间隔应用到 VM。 VM 会按 "**刷新频率**" 值指定的时间间隔检查节点配置的更新。 有关如何使用这些值的详细信息，请参阅[配置本地配置管理器](/powershell/scripting/dsc/managing-nodes/metaConfig)。

Azure 会开始启用 VM 的过程。 完成后，VM 会显示在自动化帐户的“State Configuration (DSC)”页上的“节点”选项卡中****。

## <a name="view-the-list-of-managed-nodes"></a>查看托管节点的列表

您可以在 "状态配置（DSC）" 页的 "**节点**" 选项卡中查看您的自动化帐户中启用了管理的所有计算机的列表。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”****，并单击自动化帐户的名称。
1. 在“自动化帐户”页上的“配置管理”下，单击“State configuration (DSC)”。********
1. 在“State Configuration (DSC)”页上，单击“节点”选项卡。****

## <a name="view-reports-for-managed-nodes"></a>查看托管节点的报表

每次状态配置在托管节点上执行一致性检查时，该节点会将状态报告发送回请求服务器。 可以在该节点的页上查看这些报告。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”****，并单击自动化帐户的名称。
1. 在“自动化帐户”页上的“配置管理”下，单击“State configuration (DSC)”。********
1. 在 "状态配置（DSC）" 页上，单击 "**节点**" 选项卡。在这里，你可以看到配置状态概述以及每个节点的详细信息。

   ![“节点”页的屏幕截图](./media/automation-dsc-getting-started/NodesTab.png)

1. 在“节点”选项卡上，单击节点记录打开报告。**** 单击要在其中查看其他报告详细信息的报告。

   ![“报告”边栏选项卡的屏幕快照](./media/automation-dsc-getting-started/NodeReport.png)

在单个报告的边栏选项卡中，可以看到对应的一致性检查的下列状态信息：

- 报表状态。 可能的值为：
    * 合规-节点符合检查条件。
   * 失败-配置未通过检查。
   * 不符合-节点处于`ApplyandMonitor`模式，并且计算机未处于所需的状态。
- 一致性检查的开始时间。
- 一致性检查的总运行时间。
- 一致性检查的类型。
- 任何错误，包括错误代码和错误消息。
- 在配置中使用的任何 DSC 资源，以及每个资源的状态（节点是否处于该资源的所需状态）。 可以单击每个资源以获取该资源的更多详细信息。
- 节点的名称、IP 地址和配置模式。

还可以通过单击“查看原始报告”**** 来查看节点发送到服务器的实际数据。
有关如何使用该数据的详细信息，请参阅[使用 DSC 报表服务器](/powershell/scripting/dsc/pull-server/reportserver)。

在启用节点之前，可能需要一段时间才能使用第一个报表。 启用节点后，最多可能需要等待30分钟。

## <a name="reassign-a-node-to-a-different-node-configuration"></a>将节点重新分配到另一个节点配置

为节点分配配置时，可以让其使用不同于初始配置的节点配置。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”****，并单击自动化帐户的名称。
1. 在“自动化帐户”页上的“配置管理”下，单击“State configuration (DSC)”。********
1. 在“State Configuration (DSC)”页上，单击“节点”选项卡。****
1. 在“节点”选项卡中，单击要重新分配的节点的名称****。
1. 在该节点的页面上，单击“分配节点配置”****。

    ![“节点详细信息”页的屏幕截图，其中突出显示了“分配节点配置”按钮](./media/automation-dsc-getting-started/AssignNode.png)

1. 在“分配节点配置”页中，选择要向其分配节点的节点配置，并单击“确定”****。

    ![“分配节点配置”页面的屏幕截图](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregister-a-node"></a>取消注册节点

如果不再希望节点由状态配置进行管理，可以将其注销。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”****，并单击自动化帐户的名称。
1. 在“自动化帐户”页上的“配置管理”下，单击“State configuration (DSC)”。********
1. 在“State Configuration (DSC)”页上，单击“节点”选项卡。****
1. 在“节点”选项卡上，单击要注销的节点的名称****。
1. 在该节点的窗格上，单击 "**注销**"。

    ![“节点详细信息”页的屏幕截图，其中突出显示了“注销”按钮](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>相关文章

- [Azure Automation State Configuration 概述](automation-dsc-overview.md)
- [加入 Azure Automation State Configuration 管理的计算机](automation-dsc-onboarding.md)
- [Windows PowerShell Desired State Configuration 概述](/powershell/scripting/dsc/overview/overview)
- [Azure Automation State Configuration 定价](https://azure.microsoft.com/pricing/details/automation/)
- [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)