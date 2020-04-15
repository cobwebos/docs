---
title: Azure 自动化状态 配置 持续部署与巧克力
description: 使用 Azure 自动化状态配置与巧克力包管理器描述 DevOps 连续部署。 包括包含完整的 JSON 资源管理器模板和 PowerShell 源的示例。
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 706ab128af4379a56223ff65fb12f29d37b524f7
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383274"
---
# <a name="provide-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>使用自动化状态配置和巧克力提供虚拟机的持续部署

在 DevOps 世界中，有许多工具可帮助处理连续集成管道中的各个点。 Azure 自动化[状态配置](automation-dsc-overview.md)是 DevOps 团队可以使用的选项的可喜新补充。 

Azure 自动化是 Microsoft Azure 中的托管服务，允许您使用 Runbook、节点和共享资源（如凭据、计划和全局变量）自动执行各种任务。 Azure 自动化状态配置扩展了此自动化功能，以包括 PowerShell 所需的状态配置 （DSC） 工具。 这里是一个伟大的[概述](automation-dsc-overview.md)。

本文演示如何为 Windows 计算机设置连续部署 （CD）。 您可以轻松地扩展技术，以在角色中根据需要包括尽可能多的 Windows 计算机，例如网站，然后从那里转到其他角色。

![IaaS VM 的持续部署](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="at-a-high-level"></a>概括而言

这里发生了相当多的事情，但幸运的是，它可以分解为两个主要过程：

- 编写并测试代码，针对系统的主要和次要版本创建并发布安装包。
- 创建和管理在包中安装和执行代码的 VM。  

这两个核心进程都到位后，在创建和部署新版本时，可以轻松地在 VM 上自动更新包。

## <a name="component-overview"></a>组件概述

[像apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool)这样的包管理器在Linux世界中是众所周知的，但在Windows世界却不是那么广为人知。
[巧克力](https://chocolatey.org/)就是这样的东西，斯科特·汉塞尔曼的[博客上](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx)关于这个话题是一个很好的介绍。 简而言之，Chocolatey 允许您使用命令行将包从中央存储库安装到 Windows 操作系统上。 可以创建和管理自己的存储库，Chocolatey 可以从指定的任何数量的存储库来安装包。

[PowerShell DSC](/powershell/scripting/dsc/overview/overview)是一种 PowerShell 工具，允许您声明所需的机器配置。 例如，如果您希望安装巧克力、安装 IIS、打开端口 80 以及安装网站版本 1.0.0，DSC 本地配置管理器 （LCM） 实现该配置。 DSC 拉取服务器为您的计算机保存配置存储库。 每台计算机上的 LCM 定期检查计算机的配置是否与存储的配置匹配。 它可以报告状态，也可以尝试让计算机恢复到与存储的配置匹配。 可以编辑“拉”服务器上存储的配置，使一台计算机或一组计算机与更改的配置匹配。

DSC 资源是具有特定功能的代码模块，例如管理网络、活动目录或 SQL Server。 Chocolatey DSC 资源知道如何访问 NuGet 服务器（以及其他组件）、下载包、安装包，等等。 [PowerShell 库](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)中有许多其他 DSC 资源。 在 Azure 自动化状态配置拉取服务器上安装这些模块，供配置使用。

资源管理器模板提供了生成基础结构的声明性方法，例如网络、子网、网络安全和路由、负载均衡器、NIC、VM 等。 本文将资源管理器部署[article](../azure-resource-manager/management/deployment-models.md)模型（声明性）与 Azure 服务管理 （ASM 或经典） 部署模型（命令）进行比较。 本文包括核心资源提供程序（计算、存储和网络）的讨论。

资源管理器模板的一个关键功能是，它在预配时能够将 VM 扩展安装到 VM 中。 VM 扩展具有特定功能，例如运行自定义脚本、安装防病毒软件和运行 DSC 配置脚本。 有许多其他类型的 VM 扩展。

## <a name="quick-trip-around-the-diagram"></a>示意图速览

从顶部开始，您可以编写代码、构建代码、测试代码，然后创建一个安装包。 Chocolatey 可以处理各种类型的安装包，例如 MSI、MSU、ZIP。 如果 Chocolatey 的本机功能不能满足，则 PowerShell 完全有能力进行实际安装。 将包放入可访问的位置 – 包存储库。 本用例使用 Azure Blob 存储帐户中的公共文件夹，但它可以位于任何位置。 Chocolatey 原生可配合 NuGet 服务器和其他某些工具一起管理包元数据。 [本文](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed)介绍了相应的选项。 使用示例使用 NuGet。 Nuspec 是包的元数据。 Nuspec 信息被编译为 NuPkg 并存储在 NuGet 服务器上。 当配置按名称请求包并引用 NuGet 服务器时，VM 上的巧克力 DSC 资源会抓取包并安装它。 也可以请求特定版本的包。

在图片的左下角，有一个 Azure 资源管理器模板。 在此使用示例中，VM 扩展将 VM 注册为 Azure 自动化状态配置拉取服务器作为节点。 配置存储在拉取服务器中两次：一次为纯文本，一次编译为 MOF 文件。 在 Azure 门户中，MOF 表示节点配置，而不是简单的配置。 它是与节点关联的项目，因此节点将了解其配置。 以下详细信息演示如何将节点配置分配给节点。

创建 Nuspec、编译它并将其存储在 NuGet 服务器中是一件小事。 此外，你已在管理 VM。 

采取持续部署的下一步需要设置一次拉取服务器，一次注册节点，并在服务器上创建和存储初始配置。 升级包并部署到存储库后，只需根据需要刷新拉取服务器上的配置和节点配置。

如果您不是从资源管理器模板开始，这很好。 有 PowerShell 命令可帮助您在拉取服务器注册 VM。 有关详细信息，请参阅[载入计算机，以便由 Azure 自动化状态配置进行管理](automation-dsc-onboarding.md)。

## <a name="about-the-usage-example"></a>关于使用示例

本文中的使用示例从 Azure 库中的通用 Windows Server 2012 R2 映像中的 VM 开始。 可以从任何存储的映像开始，并使用 DSC 配置对其进行调整。
不过，更改已刻入映像的配置要比使用 DSC 动态更新配置难得多。

将此技巧运用于 VM 时，不需要使用资源管理器模板和 VM 扩展。 即使 VM 不在 Azure 上，也能由 CD 管理。 只需在 VM 上安装 Chocolatey 并配置 LCM，以使其知道“拉”服务器的所在位置即可。

在正在生产的 VM 上更新包时，需要在安装更新时使该 VM 退出旋转。 具体的操作根据情况而有很大的差异。 例如，如果 VM 在 Azure 负载均衡器后面，则可以添加自定义探测。 更新 VM 时，让探测终结点返回 400。 可在配置中进行所需的调整来造成这种更改，但更新完成时，调整将切换为返回 200。

GitHub 上的[此 Visual Studio 项目](https://github.com/sebastus/ARM/tree/master/CDIaaSVM)中提供了本用例的完整源代码。

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>第 1 步：设置拉取服务器和自动化帐户

在经过身份验证的 (`Connect-AzAccount`) PowerShell 命令行中：（如果设置请求服务器，则可能需要几分钟时间）

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

您可以将自动化帐户放入以下任意区域（也称为位置）：美国东部 2、美国中南部、美国弗吉尼亚州、西欧、东南亚、日本东部、印度中部和澳大利亚东南部、加拿大中部、北欧。

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>第 2 步：对资源管理器模板进行 VM 扩展调整

此 [Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)提供了 VM 注册（使用 PowerShell DSC VM 扩展）的详细信息。
此步骤将新的 VM 注册到“拉”服务器的 State Configuration 节点列表中。 此注册的一部分指定要应用到节点的节点配置。 此节点配置尚不一定存在于拉取服务器中，因此步骤 4 是首次完成该步骤的位置，这很好。 但在步骤 2 中，需要确定节点名称和配置名称。 在本用例中，节点名称为“isvbox”，配置名称为“ISVBoxConfig”。 因此，节点配置名称（会在 DeploymentTemplate.json 中指定）为“ISVBoxConfig.isvbox”。

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>第 3 步：将所需的 DSC 资源添加到拉取服务器

PowerShell 库自动将 DSC 资源安装到 Azure 自动化帐户。
导航到所需的资源，并单击“部署到 Azure 自动化”按钮。

![PowerShell 库示例](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

最近添加到 Azure 门户的另一种技术允许您提取新模块或更新现有模块。 单击"自动化帐户资源"、"资产"磁贴，最后单击"模块"磁贴。 "浏览库"图标允许您查看库中的模块列表，深入了解详细信息并最终导入自动化帐户。 这是让模块随时保持最新状态的绝佳方法。 而且，导入功能会检查与其他模块的依赖性，以确保所有模块都保持同步。

还有手动方法。 除非以后要升级，否则每个资源只使用此方法一次。 有关创作 PowerShell 集成模块的详细信息，请参阅[为 Azure 自动化创作集成模块](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)。

>[!NOTE]
>Windows 计算机的 PowerShell 集成模块的文件夹结构与 Azure 自动化预期的文件夹结构略有不同。 

1. 安装[Windows 管理框架 v5（Windows](https://aka.ms/wmf5latest) 10 不需要）。

2. 安装集成模块。

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. 将模块文件夹从**c：\程序文件\WindowsPowerShell_Module_MODULE-NAME**复制到临时文件夹。

4. 从主文件夹中删除示例和文档。

5. 压缩主文件夹，使用文件夹的名称命名 ZIP 文件。

6. 将 ZIP 文件放入可访问的 HTTP 位置，例如 Azure 存储帐户中的 Blob 存储。

7. 运行以下命令。

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

包含的示例为 cChoco 和 xNetworking 实现这些步骤。 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>步骤 4：将节点配置添加到拉取服务器

首次将配置导入到“拉”服务器并进行编译并没有什么特别之处。 所有以后导入或编译相同配置的外观完全相同。 每次更新包且需要向外推送到生产环境时，在确保配置文件（包括包的新版本）正确之后，就可以执行此步骤。 这里是配置文件**ISVBoxConfig.ps1**：

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

下面是**新配置脚本.ps1**脚本（修改为使用 Az 模块）：

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

这些步骤导致将名为**ISVBoxConfig.isvbox**的新节点配置放置在拉取服务器上。 节点配置名称作为 生成为`configurationName.nodeName`。

## <a name="step-5-create-and-maintain-package-metadata"></a>第 5 步：创建和维护包元数据

对于放入包存储库中的每一个包，需要使用 nuspec 来描述它。
该 nuspec 必须经过编译并存储在 NuGet 服务器中。 [此处](https://docs.nuget.org/create/creating-and-publishing-a-package)对该过程进行了说明。 可以使用 MyGet.org 作为 NuGet 服务器。 他们销售这种服务，但也提供免费的入门 SKU。 在NuGet.org，您将找到有关为私有软件包安装自己的 NuGet 服务器的说明。

## <a name="step-6-tie-it-all-together"></a>第6步：把一切都绑在一起

每次版本通过 QA 并获得批准进行部署时，都会创建包，并将 nuspec 和 nupkg 更新并部署到 NuGet 服务器。 还必须更新配置（上面的步骤 4）以符合新版本号。 然后，必须将其发送到拉取服务器并进行编译。

然后，依赖于该配置的 VM 将提取并安装更新。 每个更新都很简单 - 只需一两行 PowerShell。 对于 Azure DevOps，其中一些可以封装在生成任务中，这些任务可以链接在一起生成。 [本文](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery)提供更多详细信息。 此[GitHub 存储库](https://github.com/Microsoft/vso-agent-tasks)详细介绍了可用的生成任务。

## <a name="related-articles"></a>相关文章
* [Azure Automation DSC 概述](automation-dsc-overview.md)
* [Azure 自动化 DSC cmdlet](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [载入计算机以便通过 Azure 自动化 DSC 进行管理](automation-dsc-onboarding.md)

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[Azure 自动化状态配置](automation-dsc-overview.md)。
- 要开始，请参阅[开始使用 Azure 自动化状态配置](automation-dsc-getting-started.md)。
- 要了解如何编译 DSC 配置以便将它们分配给目标节点，请参阅[在 Azure 自动化状态配置中编译配置](automation-dsc-compile.md)。
- 有关 PowerShell cmdlet 引用，请参阅[Az.自动化](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 有关定价信息，请参阅[Azure 自动化状态配置定价](https://azure.microsoft.com/pricing/details/automation/)。
- 要查看在连续部署管道中使用 Azure 自动化状态配置的示例，请参阅[使用 Azure 自动化状态配置进行持续部署和巧克力](automation-dsc-cd-chocolatey.md)。
