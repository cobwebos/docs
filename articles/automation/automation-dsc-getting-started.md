---
title: Azure Automation State Configuration 入门
description: Azure Automation State Configuration (DSC) 中最常见任务的说明和示例
services: automation
ms.service: automation
ms.component: dsc
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9a18855d11c0b367b7d58ffb0f4c62e752c05b89
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004226"
---
# <a name="getting-started-with-azure-automation-state-configuration"></a>Azure Automation State Configuration 入门

本文说明如何使用 Azure Automation State Configuration 执行最常见的任务，例如创建、导入、编译配置，加入要管理的计算机和查看报告。 有关 Azure Automation State Configuration 的概述，请参阅 [Azure Automation State Configuration 概述](automation-dsc-overview.md)。 有关 Desired State Configuration (DSC) 文档，请参阅 [Windows PowerShell Desired State Configuration 概述](/powershell/dsc/overview)。

本文提供有关如何使用 Azure Automation State Configuration 的分步指南。 如果需要一个已经设置好的示例环境，而不想按照本文所述步骤进行操作，则可使用以下资源管理器模板：[Azure 自动化托管节点模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration)。 此模板设置了一个完整的 Azure Automation State Configuration 环境，包括一个由 Azure Automation State Configuration 管理的 Azure VM。

## <a name="prerequisites"></a>先决条件

若要完成本文中的示例，需要具备以下条件：

- 一个 Azure 自动化帐户。 有关如何创建 Azure 自动化运行方式帐户的说明，请参阅 [Azure 运行方式帐户](automation-sec-configure-azure-runas-account.md)。
- 一个运行 Windows Server 2008 R2 或更高版本的 Azure 资源管理器 VM（非经典）。 如需创建 VM 的说明，请参阅[在 Azure 门户中创建第一个 Windows 虚拟机](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>创建 DSC 配置

将创建一个简单的 [DSC 配置](/powershell/dsc/configurations)，以确保存在（或不存在）**Web-Server** Windows 功能 (IIS)，具体取决于分配节点的方式。

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
1. 将文件另存为 `TestConfig.ps1`。

此配置调用每个节点块中的一个资源（[WindowsFeature 资源](/powershell/dsc/windowsfeatureresource)），以确保存在（或不存在）**Web-Server** 功能。

## <a name="importing-a-configuration-into-azure-automation"></a>将配置导入 Azure 自动化

接下来，会将配置导入自动化帐户。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”，并单击自动化帐户的名称。
1. 在“自动化帐户”页上的“配置管理”下，选择“State configuration (DSC)”。
1. 在“State configuration (DSC)”页上，依次单击“配置”选项卡、“+ 添加”。
1. 在“导入配置”页中，浏览到计算机上的 `TestConfig.ps1` 文件。

   ![**导入配置**边栏选项卡的屏幕截图](./media/automation-dsc-getting-started/AddConfig.png)

1. 单击“确定”。

## <a name="viewing-a-configuration-in-azure-automation"></a>查看 Azure 自动化中的配置

在导入配置以后，即可在 Azure 门户中查看该配置。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”，并单击自动化帐户的名称。
1. 在“自动化帐户”页上的“配置管理”下，选择“State configuration (DSC)”。
1. 在“State configuration (DSC)”页上单击“配置”选项卡，然后单击“TestConfig”（这是在前一过程中导入的配置的名称）。
1. 在“TestConfig 配置”页中，单击“查看配置源”。

   ![“TestConfig 配置”边栏选项卡的屏幕快照](./media/automation-dsc-getting-started/ViewConfigSource.png)

   此时会打开“TestConfig 配置源”页面，其中显示配置的 PowerShell 代码。

## <a name="compiling-a-configuration-in-azure-automation"></a>编译 Azure 自动化中的配置

在将所需状态应用到某个节点之前，必须将定义该状态的 DSC 配置编译成一个或多个节点配置（MOF 文档），然后放置在自动化 DSC 拉取服务器上。 如需在 Azure Automation State Configuration 中进行配置编译的更详细说明，请参阅[在 Azure Automation State Configuration 中编译配置](automation-dsc-compile.md)。
有关编译配置的详细信息，请参阅 [DSC 配置](/powershell/dsc/configurations)。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”，并单击自动化帐户的名称。
1. 在“自动化帐户”页上的“配置管理”下，单击“State configuration (DSC)”。
1. 在“State configuration (DSC)”页上单击“配置”选项卡，然后单击“TestConfig”（前面导入的配置的名称）。
1. 在“TestConfig 配置”页中，单击“编译”，并单击“是”。 此时会启动编译作业。

   ![“TestConfig 配置”页的屏幕截图，其中突出显示“编译”按钮](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> 在 Azure 自动化中编译配置时，会自动将任何创建的节点配置 MOF 部署到拉取服务器。

## <a name="viewing-a-compilation-job"></a>查看编译作业

开始编译后，即可在“配置”页面的“编译作业”磁贴中查看该作业。 “编译作业”磁贴显示当前正在运行的、已完成的以及失败的作业。 打开编译作业页面时，会显示有关该作业的信息，包括遇到的任何错误或警告、在配置中使用的输入参数以及编译日志。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”，并单击自动化帐户的名称。
1. 在“自动化帐户”页上的“配置管理”下，单击“State configuration (DSC)”。
1. 在“State configuration (DSC)”页上单击“配置”选项卡，然后单击“TestConfig”（前面导入的配置的名称）。
1. 在“编译作业”下，选择要查看的编译作业。 此时会打开“编译作业”页，其中标记了启动编译作业的日期。

   ![“编译作业”页的屏幕截图](./media/automation-dsc-getting-started/CompilationJob.png)

1. 单击“编译作业”页中的任意磁贴，了解有关该作业的更多详细信息。

## <a name="viewing-node-configurations"></a>查看节点配置

成功完成编译作业以后，会创建一个或多个新的节点配置。 节点配置是一个 MOF 文档，该文档部署到拉取服务器上，可供一个或多个节点拉取和应用。 可以在“State Configuration (DSC)”页中查看自动化帐户中的节点配置。 节点配置的名称格式为 *ConfigurationName*.*NodeName*。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”，并单击自动化帐户的名称。
1. 在“自动化帐户”边栏选项卡上的“配置管理”下，单击“State configuration (DSC)”。
1. 在“State configuration (DSC)”页上，单击“编译的配置”选项卡。

   ![“编译的配置”选项卡屏幕截图](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>加入使用 Azure Automation State Configuration 管理的 Azure VM

可以使用 Azure 自动化状态配置来管理 Azure VM（包括经典 VM 和资源管理器 VM）、本地 VM、Linux 计算机、AWS VM，以及本地物理机。 本文介绍如何仅登记 Azure 资源管理器 VM。 有关加入其他类型的计算机的信息，请参阅[加入由 Azure 自动化状态配置管理的计算机](automation-dsc-onboarding.md)。

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>加入由 Azure Automation State Configuration 管理的 Azure 资源管理器 VM

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”，并单击自动化帐户的名称。
1. 在“自动化帐户”边栏选项卡上的“配置管理”下，单击“State configuration (DSC)”。
1. 在“State Configuration (DSC)”页上的“节点”选项卡中，单击“+ 添加”。

   ![“DSC 节点”页的屏幕截图，其中突出显示“添加 Azure VM”按钮](./media/automation-dsc-getting-started/OnboardVM.png)

1. 在“虚拟机”页上，选择自己的 VM。
1. 在“虚拟机”详细信息页上，单击“+ 连接”。

   > [!IMPORTANT]
   > 该 VM 必须是运行 Windows Server 2008 R2 或更高版本的 Azure 资源管理器 VM。

1. 在“注册”页上的“节点配置名称”框中，选择要应用到 VM 的节点配置的名称。 可以选择在此时提供名称。 可以在登记节点以后更改分配的节点配置。
   选中“根据需要重新启动节点”，并单击“确定”。

   ![“注册”边栏选项卡的屏幕快照](./media/automation-dsc-getting-started/RegisterVM.png)

   指定的节点配置将按“配置模式频率”指定的时间间隔应用到 VM，而 VM 则会按“刷新频率”指定的时间间隔检查节点配置的更新。 有关如何使用这些值的详细信息，请参阅[配置本地配置管理器](https://msdn.microsoft.com/PowerShell/DSC/metaConfig)。
1. 在“添加 Azure VM”边栏选项卡中，单击“创建”。

Azure 将启动登记 VM 的过程。 完成后，VM 会显示在自动化帐户的“State Configuration (DSC)”页上的“节点”选项卡中。

## <a name="viewing-the-list-of-managed-nodes"></a>查看托管节点的列表

在“State Configuration (DSC)”页上的“节点”选项卡中，可以查看已在自动化帐户中加入管理的所有计算机的列表。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”，并单击自动化帐户的名称。
1. 在“自动化帐户”边栏选项卡上的“配置管理”下，单击“State configuration (DSC)”。
1. 在“State Configuration (DSC)”页上，单击“节点”选项卡。

## <a name="viewing-reports-for-managed-nodes"></a>查看托管节点的报告

每当 Azure Automation State Configuration 在托管节点上执行一致性检查时，该节点会将状态报告发送回拉取服务器。 可以在该节点的页上查看这些报告。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”，并单击自动化帐户的名称。
1. 在“自动化帐户”边栏选项卡上的“配置管理”下，单击“State configuration (DSC)”。
1. 在“State Configuration (DSC)”页上，单击“节点”选项卡。在此处，可以查看配置状态的概述，以及每个节点的详细信息。

   ![“节点”页的屏幕截图](./media/automation-dsc-getting-started/NodesTab.png)

1. 在“节点”选项卡上，单击节点记录打开报告。 单击要在其中查看其他报告详细信息的报告。

   ![“报告”边栏选项卡的屏幕快照](./media/automation-dsc-getting-started/NodeReport.png)

在单个报告的边栏选项卡中，可以看到对应的一致性检查的下列状态信息：

- 报告状态 - 节点是否“合规”、配置是否“失败”、节点是否“不合规”（当节点处于 **ApplyandMonitor** 模式且计算机不在所需状态时）。
- 一致性检查的开始时间。
- 一致性检查的总运行时间。
- 一致性检查的类型。
- 任何错误，包括错误代码和错误消息。
- 在配置中使用的任何 DSC 资源，以及每个资源的状态（节点是否处于该资源的期望状态）- 可以单击每个资源以获取该资源的更多详细信息。
- 节点的名称、IP 地址和配置模式。

还可以通过单击“查看原始报告”来查看节点发送到服务器的实际数据。
有关如何使用该数据的详细信息，请参阅[使用 DSC 报表服务器](/powershell/dsc/reportserver)。

在登记节点以后，可能需要一段时间才会提供第一个报告。 在登记节点以后，可能需要等待长达 30 分钟的时间才能获得第一个报告。

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>为节点重新分配其他节点配置

为节点分配配置时，可以让其使用不同于初始配置的节点配置。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”，并单击自动化帐户的名称。
1. 在“自动化帐户”边栏选项卡上的“配置管理”下，单击“State configuration (DSC)”。
1. 在“State Configuration (DSC)”页上，单击“节点”选项卡。
1. 在“节点”选项卡中，单击要重新分配的节点的名称。
1. 在该节点的页面上，单击“分配节点配置”。

    ![“节点详细信息”页的屏幕截图，其中突出显示了“分配节点配置”按钮](./media/automation-dsc-getting-started/AssignNode.png)

1. 在“分配节点配置”页中，选择要向其分配节点的节点配置，并单击“确定”。

    ![“分配节点配置”页面的屏幕截图](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>注销节点

如果不想让某个节点继续受 Azure Automation DSC 管理，则可注销该节点。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧，单击“所有资源”，并单击自动化帐户的名称。
1. 在“自动化帐户”边栏选项卡上的“配置管理”下，单击“State configuration (DSC)”。
1. 在“State Configuration (DSC)”页上，单击“节点”选项卡。
1. 在“节点”选项卡上，单击要注销的节点的名称。
1. 在该节点的页中，单击“注销”。

    ![“节点详细信息”页的屏幕截图，其中突出显示了“注销”按钮](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>相关文章

- [Azure Automation State Configuration 概述](automation-dsc-overview.md)
- [加入 Azure Automation State Configuration 管理的计算机](automation-dsc-onboarding.md)
- [Windows PowerShell Desired State Configuration 概述](/powershell/dsc/overview)
- [Azure Automation State Configuration cmdlet](/powershell/module/azurerm.automation/#automation)
- [Azure Automation State Configuration 定价](https://azure.microsoft.com/pricing/details/automation/)