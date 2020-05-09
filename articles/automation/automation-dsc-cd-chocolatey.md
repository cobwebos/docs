---
title: Azure 自动化状态配置持续部署与 Chocolatey
description: 介绍如何使用 Azure 自动化状态配置和 Chocolatey 包管理器进行 DevOps 持续部署。 包含完整 JSON 资源管理器模板和 PowerShell 源的示例。
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.openlocfilehash: 278c6ee05fdf78cbfa8653381b65233fbb513593
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996122"
---
# <a name="provide-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>使用自动化状态配置和 Chocolatey 为虚拟机提供持续部署

在 DevOps 领域中，有许多工具可帮助实现持续集成管道中的各个点。 Azure 自动化[状态配置](automation-dsc-overview.md)是 DevOps 团队可使用的选项的欢迎新添加。 

Azure 自动化是 Microsoft Azure 中的一种托管服务，可让你使用 runbook、节点和共享资源（例如凭据、计划和全局变量）来自动执行各种任务。 Azure 自动化状态配置扩展了此自动化功能，以包括 PowerShell Desired State Configuration （DSC）工具。 下面是一个很好的[概述](automation-dsc-overview.md)。

本文演示如何为 Windows 计算机设置持续部署（CD）。 您可以轻松扩展该技术，使其包含在角色中所需的多个 Windows 计算机（例如，网站），并从该角色中转到其他角色。

![IaaS VM 的持续部署](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>概括而言

这里有相当多的一点，但幸运的是，它可以分为两个主要过程：

- 编写并测试代码，针对系统的主要和次要版本创建并发布安装包。
- 创建和管理在包中安装和执行代码的虚拟机。  

完成这两个核心过程后，在创建和部署新版本时，可以轻松地在 Vm 上自动更新包。

## <a name="component-overview"></a>组件概述

程序包管理器（如[apt](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) ）在 Linux 世界上非常著名，但在 Windows 领域中也不是如此。
[Chocolatey](https://chocolatey.org/)是这样的事，Scott Hanselman 的[博客文章](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx)是一项很好的介绍。 简而言之，Chocolatey 允许你使用命令行从中央存储库将包安装到 Windows 操作系统。 可以创建和管理自己的存储库，Chocolatey 可以从指定的任何数量的存储库来安装包。

[POWERSHELL DSC](/powershell/scripting/dsc/overview/overview)是一个 powershell 工具，可用于声明计算机所需的配置。 例如，如果你想要安装 Chocolatey，安装了 IIS，打开了端口80，并且安装了网站的1.0.0 版，则 DSC 本地 Configuration Manager （LCM）将实现该配置。 DSC 请求服务器包含计算机配置的存储库。 每台计算机上的 LCM 定期检查计算机的配置是否与存储的配置匹配。 它可以报告状态，也可以尝试让计算机恢复到与存储的配置匹配。 可以编辑“拉”服务器上存储的配置，使一台计算机或一组计算机与更改的配置匹配。

DSC 资源是具有特定功能的代码模块，例如管理网络、Active Directory 或 SQL Server。 Chocolatey DSC 资源知道如何访问 NuGet 服务器（以及其他组件）、下载包、安装包，等等。 [PowerShell 库](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)中有许多其他 DSC 资源。 将这些模块安装在 Azure 自动化状态配置请求服务器上以供配置使用。

资源管理器模板提供了一种声明性的方式来生成基础结构，例如网络、子网、网络安全性和路由、负载均衡器、Nic、Vm 等等。 以下是将资源管理器部署模型（声明性）与 Azure 服务管理（ASM 或经典）部署模型（命令式）进行比较的一[篇文章](../azure-resource-manager/management/deployment-models.md)。 本文介绍核心资源提供程序：计算、存储和网络。

资源管理器模板的一项重要功能是能够在预配 vm 时将 VM 扩展安装到 VM 中。 VM 扩展具有特定功能，例如运行自定义脚本、安装防病毒软件和运行 DSC 配置脚本。 有许多其他类型的 VM 扩展。

## <a name="quick-trip-around-the-diagram"></a>示意图速览

从顶部开始，你编写代码，生成代码，对其进行测试，然后创建安装包。 Chocolatey 可以处理各种类型的安装包，例如 MSI、MSU、ZIP。 如果 Chocolatey 的本机功能不是最新的，则可以使用 PowerShell 的全部功能来执行实际安装。 将包放入可访问的位置 – 包存储库。 本用例使用 Azure Blob 存储帐户中的公共文件夹，但它可以位于任何位置。 Chocolatey 原生可配合 NuGet 服务器和其他某些工具一起管理包元数据。 [本文](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) 介绍了相应的选项。 用法示例使用 NuGet。 Nuspec 是包的元数据。 Nuspec 信息编译到 NuPkg 中，并存储在 NuGet 服务器上。 当配置通过名称请求包并引用 NuGet 服务器时，VM 上的 Chocolatey DSC 资源会获取包并进行安装。 也可以请求特定版本的包。

在图片的左下角有一个 Azure 资源管理器模板。 在此示例中，VM 扩展以节点的形式向 Azure 自动化状态配置请求服务器注册 VM。 此配置存储在拉取服务器两次：一次是纯文本，并编译为 MOF 文件。 在 Azure 门户中，MOF 表示节点配置，而与简单配置不同。 这是与节点相关联的项目，因此节点将知道其配置。 以下详细信息演示如何将节点配置分配给节点。

创建 Nuspec、对其进行编译并将其存储在 NuGet 服务器中是一小小事情。 此外，你已在管理 VM。 

采取连续部署的下一步需要设置请求服务器一次，将节点注册一次，并在服务器上创建和存储初始配置。 升级包并将其部署到存储库后，只需根据需要刷新请求服务器上的配置和节点配置。

如果不是从资源管理器模板开始，那就没关系。 提供了 PowerShell 命令来帮助你向请求服务器注册 Vm。 有关详细信息，请参阅[通过 Azure 自动化状态配置载入计算机进行管理](automation-dsc-onboarding.md)。

## <a name="about-the-usage-example"></a>关于使用示例

本文中的用法示例从 Azure 库中的通用 Windows Server 2012 R2 映像开始。 可以从任何存储的映像开始，并使用 DSC 配置对其进行调整。
不过，更改已刻入映像的配置要比使用 DSC 动态更新配置难得多。

将此技巧运用于 VM 时，不需要使用资源管理器模板和 VM 扩展。 即使 VM 不在 Azure 上，也能由 CD 管理。 只需在 VM 上安装 Chocolatey 并配置 LCM，以使其知道“拉”服务器的所在位置即可。

在生产中的 VM 上更新包时，需要在安装更新时将该 VM 从轮换中取出。 具体的操作根据情况而有很大的差异。 例如，如果 VM 在 Azure 负载均衡器后面，则可以添加自定义探测。 更新 VM 时，让探测终结点返回 400。 可在配置中进行所需的调整来造成这种更改，但更新完成时，调整将切换为返回 200。

GitHub 上的[此 Visual Studio 项目](https://github.com/sebastus/ARM/tree/master/CDIaaSVM)中提供了本用例的完整源代码。

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>步骤1：设置请求服务器和自动化帐户

在经过身份验证的 (`Connect-AzAccount`) PowerShell 命令行中：（如果设置请求服务器，则可能需要几分钟时间）

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

你可以将自动化帐户放入以下任何区域（也称为位置）：美国东部2、美国中南部、US Gov 弗吉尼亚州、西欧、东南亚、日本东部、印度中部、澳大利亚东南部、加拿大中部、北欧。

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>步骤2：将 VM 扩展调整到资源管理器模板

此 [Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)提供了 VM 注册（使用 PowerShell DSC VM 扩展）的详细信息。
此步骤将新的 VM 注册到“拉”服务器的 State Configuration 节点列表中。 此注册的一部分指定要应用到节点的节点配置。 此节点配置尚无需存在于请求服务器中，因此，第4步是第一次执行此操作。 但在步骤 2 中，需要确定节点名称和配置名称。 在本用例中，节点名称为“isvbox”，配置名称为“ISVBoxConfig”。 因此，节点配置名称（会在 DeploymentTemplate.json 中指定）为“ISVBoxConfig.isvbox”。

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>步骤3：将所需的 DSC 资源添加到请求服务器

PowerShell 库自动将 DSC 资源安装到 Azure 自动化帐户。
导航到所需的资源，并单击“部署到 Azure 自动化”按钮。

![PowerShell 库示例](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

最近添加到 Azure 门户的另一种方法允许您请求新模块或更新现有模块。 依次单击 "自动化帐户" 资源、"资产" 磁贴和 "模块" 磁贴。 通过 "浏览库" 图标可以查看库中的模块列表，向下钻取详细信息，并最终导入自动化帐户。 这是让模块随时保持最新状态的绝佳方法。 而且，导入功能会检查与其他模块的依赖性，以确保所有模块都保持同步。

另外还有一种手动方法，只对每个资源使用一次，除非以后再升级。 有关创作 PowerShell 集成模块的详细信息，请参阅[为 Azure 自动化创作集成模块](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)。

>[!NOTE]
>Windows 计算机的 PowerShell 集成模块的文件夹结构与 Azure 自动化所需的文件夹结构稍有不同。 

1. 安装[Windows Management Framework v5](https://aka.ms/wmf5latest) （windows 10 不需要）。

2. 安装集成模块。

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. 将模块文件夹从**C:\Program Files\WindowsPowerShell\Modules\MODULE-NAME**复制到临时文件夹中。

4. 删除主文件夹中的示例和文档。

5. 压缩主文件夹，将 ZIP 文件命名为该文件夹的名称。

6. 将 ZIP 文件放到可访问的 HTTP 位置，例如 Azure 存储帐户中的 blob 存储。

7. 运行以下命令。

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

所包含的示例将为 cChoco 和 xNetworking 实现这些步骤。 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>步骤4：将节点配置添加到请求服务器

首次将配置导入到“拉”服务器并进行编译并没有什么特别之处。 所有之后的相同配置的导入或编译看起来完全相同。 每次更新包且需要向外推送到生产环境时，在确保配置文件（包括包的新版本）正确之后，就可以执行此步骤。 下面是配置文件**isvboxconfig.isvbox**：

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

下面是**New-ConfigurationScript**脚本（已修改为使用 Az module）：

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

这些步骤将导致在请求服务器上放置名为**isvboxconfig.isvbox. isvbox**的新节点配置。 节点配置名称生成为`configurationName.nodeName`。

## <a name="step-5-create-and-maintain-package-metadata"></a>步骤5：创建和维护包元数据

对于放入包存储库中的每个包，都需要一个描述它的 Nuspec。 它必须编译并存储在 NuGet 服务器上。 [此处](https://docs.nuget.org/create/creating-and-publishing-a-package)对该过程进行了说明。 

你可以使用**MyGet.org**作为 NuGet 服务器。 你可以购买此服务，但三个是免费的入门 SKU。 在[NuGet](https://www.nuget.org/)中，你将找到有关为专用包安装自己的 NuGet 服务器的说明。

## <a name="step-6-tie-it-all-together"></a>步骤6：将所有内容全部关联起来

每次版本传递 QA 并批准部署时，都会创建包，并更新 nuspec 和 nupkg 并将其部署到 NuGet 服务器。 还必须更新配置（步骤4），以同意新的版本号。 然后必须将其发送到请求服务器并进行编译。

然后，依赖于该配置的 VM 将提取并安装更新。 其中的每个更新都很简单-只需一两个 PowerShell 即可。 对于 Azure DevOps，其中的一些将封装在生成任务中，这些任务可在内部构建。 [本文](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery)提供更多详细信息。 此[GitHub](https://github.com/Microsoft/vso-agent-tasks)存储库详细介绍了可用的生成任务。

## <a name="related-articles"></a>相关文章
* [Azure Automation DSC 概述](automation-dsc-overview.md)
* [载入计算机以便通过 Azure 自动化 DSC 进行管理](automation-dsc-onboarding.md)

## <a name="next-steps"></a>后续步骤

- 有关概述，请参阅[Azure 自动化状态配置](automation-dsc-overview.md)。
- 若要开始，请参阅[Azure 自动化状态配置](automation-dsc-getting-started.md)入门。
- 若要了解如何编译 DSC 配置，以便可以将其分配给目标节点，请参阅[在 Azure 自动化状态配置中编译配置](automation-dsc-compile.md)。
- 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 有关定价信息，请参阅[Azure 自动化状态配置定价](https://azure.microsoft.com/pricing/details/automation/)。
- 若要查看在持续部署管道中使用 Azure 自动化状态配置的示例，请参阅[使用 Azure 自动化状态配置和 Chocolatey 进行连续部署](automation-dsc-cd-chocolatey.md)。
