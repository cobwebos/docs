---
title: "Azure 自动化 DSC 概述 | Microsoft Docs"
description: "对 Azure 自动化所需状态配置 (DSC) 及其术语和已知问题的概述"
services: automation
documentationcenter: dev-center-name
author: sdwheeler
manager: carmonm
editor: tysonn
keywords: "powershell dsc, 所需状态配置, powershell dsc azure"
ms.assetid: fd40cb68-c1a6-48c3-bba2-710b607d1555
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 05/10/2016
ms.author: magoedte;sewhee
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 6ec8e6cc2019a4819e68e574e7d7b3bbd50d030c


---
# <a name="azure-automation-dsc-overview"></a>Azure 自动化 DSC 概述
## <a name="what-is-azure-automation-dsc"></a>什么是 Azure 自动化 DSC？
部署和维持你服务器和应用程序的所需状态可能乏味且易于出错。 借助 Azure 自动化所需状态配置 (DSC)，你能够以云规模持续部署、可靠监视和自动更新你的所有 IT 源。 以 PowerShell DSC 为基础，自动化 DSC 可在云中或本地跨使用 Windows 或 Linux 的物理计算机和虚拟机 (VM) 将计算机配置与特定状态相匹配。 你可实现持续的 IT 服务交付与一致的控制，且轻松地在多样化混合 IT 环境内管理快速更改。

Azure 自动化 DSC 是在 PowerShell DSC 中已引入基本功能的基础上构建的，将为用户提供更轻松的配置管理体验。 Azure 自动化 DSC 向 [PowerShell Desired State Configuration](https://msdn.microsoft.com/powershell/dsc/overview) 提供的管理层与目前的 Azure 自动化为 PowerShell 脚本提供的相同。

使用 Azure 自动化 DSC，可以[创作和管理 PowerShell 所需状态配置](https://technet.microsoft.com/library/dn249918.aspx)、导入 [DSC 资源](https://technet.microsoft.com/library/dn282125.aspx)并生成 DSC 节点配置（MOF 文档），所有这一切均在云中完成。 这些 DSC 项会被放置在 Azure 自动化 [DSC 请求服务器](https://technet.microsoft.com/library/dn249913.aspx)上，方便云中或本地的目标节点（例如物理机和虚拟机）拾取它们，自动符合其所指定的所需状态，并回过头来将其符合所需状态的情况报告给 Azure 自动化。

你更喜欢观看视频而不喜欢阅读文档？ 请观看以下在 2015 年 5 月发布的视频，这是首次发布 Azure 自动化 DSC 的时间。 **注意：**尽管这段视频中所述的概念和生命周期都是正确的，但 Azure 自动化 DSC 自从这段视频录制以来已有很大的发展。 它现已正式发布，在 Azure 门户中具有更丰富的 UI，并支持更多功能。

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]
> 
> 

## <a name="azure-automation-dsc-terms"></a>Azure 自动化 DSC 术语
### <a name="configuration"></a>配置
PowerShell DSC 引入了名为“配置”的新概念。 使用配置，你可以通过 PowerShell 语法来定义你的环境所需的状态。 若要使用 DSC 配置你的环境，请先使用配置关键字来定义 Windows PowerShell 脚本块，然后使用标识符来跟踪它，最后再使用大括号 ({}) 来分隔该块。

![替换文字](./media/automation-dsc-overview/AADSC_1.png)

在配置块内，你可以定义节点配置块，以便指定你的环境中应配置为完全相同的一组节点（计算机）的所需配置。 节点配置通过这种方式来表示一个或多个节点需要采用的一种“角色”。 节点配置块以节点关键字开头。 紧跟此关键字的是角色的名称，可以是变量或表达式。 在角色名称之后，使用大括号 {} 来分隔节点配置块。

![替换文字](./media/automation-dsc-overview/AADSC_2.png)

在节点配置模块中，你可以定义用于配置特定 DSC 资源的资源块。 资源块以资源名称开头，后跟需要为该块指定的标识符，然后是用来分隔块的大括号 {}。

![替换文字](./media/automation-dsc-overview/AADSC_3.png)

如需有关配置关键字的更详细信息，请参阅：[Understanding Configuration Keyword in Desired State Configuration](http://blogs.msdn.com/b/powershell/archive/2013/11/05/understanding-configuration-keyword-in-desired-state-configuration.aspx "Understanding Configuration Keyword in Desired State Configuration")（了解 Desired State Configuration 中的关键字“CONFIGURATION”）

运行（编译）DSC 配置将生成一个或多个 DSC 节点配置（MOF 文档），DSC 节点需要应用这些配置才能符合所需状态。

Azure 自动化 DSC 允许你在 Azure 自动化中导入、创作和编译 DSC 配置，这与你可以在 Azure 自动化中导入、创作和启动 Runbook 类似。

> [!IMPORTANT]
> 在 Azure 自动化 DSC 中，一项配置只应包含一个配置块，块名称与配置名称相同。 
> 
> 

### <a name="node-configuration"></a>节点配置
编译 DSC 配置时，会生成一个或多个节点配置，具体取决于配置中的节点块。 节点配置等同于“MOF”或"配置文档"（如果你熟悉这些 PS DSC 术语的话），表示"角色"（例如 Web 服务器或辅助角色），即一个或多个节点应采用的所需状态，或检查其兼容性。 Azure 自动化 DSC 中的节点配置名称采用 “Configuration Name.NodeConfigurationBlockName”格式。

PS DSC 节点现在可以感知那些应通过 DSC 推送或拉取方法来执行的节点配置。 Azure 自动化 DSC 依赖于 DSC 拉取方法，在该方法中，节点会从 Azure 自动化 DSC 拉取服务器中请求应该应用的节点配置。 由于节点会向 Azure 自动化 DSC 发出请求，因此这些节点可能会位于防火墙之后，也可能会关闭所有入站端口，等等。它们只需要对 Internet 的出站访问权限（直接访问或通过代理）。

### <a name="node"></a>节点
DSC 节点是由 DSC 管理其配置的任何计算机。 可以是 Windows 或 Linux Azure VM、本地 VM/物理主机或其他公有云上的计算机。 节点会执行节点配置，以便始终符合其所定义的所需状态，并且还会回过头来向报告服务器报告其配置状态以及是否符合所需状态。

Azure 自动化 DSC 可以在登记节点后对其进行轻松的管理，并允许更改分配给每个服务器端节点的节点配置，这样当某个节点下一次检查服务器上是否有说明时，它就会采取不同的角色并更改其配置方式及其应该报告的合规状态。

### <a name="resource"></a>资源
DSC 资源是可用于定义 Windows PowerShell 所需状态配置 (DSC) 配置的构建基块。 DSC 附带了一组内置资源，例如，用于文件和文件夹、服务器功能和角色、注册表设置、环境变量以及服务和进程的资源。 若要了解内置 DSC 资源的完整列表以及如何使用它们，请参阅 [Built-In Windows PowerShell Desired State Configuration Resources](https://technet.microsoft.com/library/dn249921.aspx)（内置 Windows PowerShell Desired State Configuration 资源）。

还可以将 DSC 资源作为 PowerShell 模块的一部分导入，以便扩展内置 DSC 资源的集合。 如果 DSC 节点要执行的节点配置包含对非默认资源的引用，则该节点会从 DSC 拉取服务器中拉取这些资源。 若要了解如何创建自定义资源，请参阅[Build Custom Windows PowerShell Desired State Configuration Resources](https://technet.microsoft.com/library/dn249927.aspx)（构建自定义 Windows PowerShell Desired State Configuration 资源）。

Azure 自动化 DSC 附带的内置 DSC 资源与 PS DSC 附带的完全一样。 通过将包含额外资源的 PowerShell 模块导入 Azure 自动化中，可以向 Azure 自动化 DSC 添加这些资源。

### <a name="compilation-job"></a>编译作业
Azure 自动化 DSC 中的编译作业是配置的编译实例，用于创建一个或多个节点配置。 它们类似于 Azure 自动化 Runbook 作业，但实际上并不执行任何任务，只是创建节点配置。 编译作业创建的任何节点配置会自动放置在 Azure 自动化 DSC“拉”服务器上，并覆盖以前版本的节点配置（如果此配置存在以前的版本）。 编译作业所生成的节点配置的名称采用“ConfigurationName.NodeConfigurationBlockName”格式。 例如，编译以下配置将生成名为“MyConfiguration.webserver”的单节点配置

![替换文字](./media/automation-dsc-overview/AADSC_5.png)

> [!NOTE]
> 就像 Runbook 一样，配置可以进行发布。 这与将 DSC 项目放置到 Azure 自动化 DSC 拉取服务器上无关。 编译作业导致 DSC 项目被放置到 Azure 自动化 DSC 拉取服务器上。 有关如何在 Azure 自动化中“发布”内容的详细信息，请参阅[发布 Runbook](https://msdn.microsoft.com/library/dn903765.aspx)。
> 
> 

## <a name="azure-automation-dsc-lifecycle"></a>Azure 自动化 DSC 生命周期
从空的自动化帐户转到一组受管的正确配置的节点涉及一系列流程，包括定义配置、将这些配置转变成节点配置、将节点加入 Azure 自动化 DSC 中和这些节点配置中。 下图说明了 Azure 自动化 DSC 生命周期：

![替换文字](./media/automation-dsc-overview/DSCLifecycle.png)

下图演示了 DSC 生命周期的详细分步过程。 其中包含配置导入和应用到 Azure 自动化中的节点的不同方式，以及为使本地计算机支持 DSC 与不同组件之间交互所需的组件。 

![DSC 体系结构](./media/automation-dsc-overview/dsc-architecture.png)     

## <a name="gotchas-known-issues"></a>难题/已知问题：
* 升级到 WMF 5 RTM 时，如果计算机已注册为 Azure 自动化 DSC 的节点，请从 Azure 自动化 DSC 将其注销，并且在 WMF 5 RTM 升级之后重新注册。
* 目前，Azure 自动化 DSC 不支持进行部分式的或复合式的 DSC 配置。 但是，可以导入 DSC 复合资源并像在本地 PowerShell 中一样在 Azure 自动化 DSC 配置中使用，使配置可重复使用。
* 必须先安装最新版的 WMF 5，然后适用于 Windows 的 PowerShell DSC 代理才能与 Azure 自动化通信。 只有安装了适用于 Linux 的 PowerShell DSC 代理的最新版本，Linux 才能与 Azure 自动化通信。
* 传统的 PowerShell DSC 拉取服务器需要将模块 zip 文件置于拉取服务器上，采用 **ModuleName_Version.zip”** 格式。 Azure 自动化要求在导入 PowerShell 模块时，其名称采用 **ModuleName.zip** 格式。 请参阅[此博客文章](https://azure.microsoft.com/blog/2014/12/15/authoring-integration-modules-for-azure-automation/)，了解将模块导入 Azure 自动化中时需要的集成模块格式的详细信息。 
* 已导入 Azure 自动化中的 PowerShell 模块不能包含 .doc 或 .docx 文件。 某些包含 DSC 资源的 PowerShell 模块包含这些文件，可用作帮助文件。 在导入到 Azure 自动化中之前，这些文件应从模块中删除。
* 将某个节点首次注册到 Azure 自动化帐户时，或者将节点更改为映射到服务器端的其他节点配置时，其状态将为“兼容”，即使该节点的状态实际上并不符合它现在所映射到的节点配置。 注册以后或者节点配置映射更改以后，如果该节点执行了首个拉取操作并发送了首个报告，则节点状态将是可以信任的。
* 登记 Azure Windows VM 以让 Azure 自动化 DSC 使用任何直接登记方法进行管理时，最多可能需要一小时 VM 才会在 Azure 自动化中显示为 DSC 节点。 Azure VM DSC 扩展将在 VM 上安装 Windows Management Framework 5.0，以便将 VM 登记到 Azure 自动化 DSC。
* 在注册之后，每个节点将自动协商唯一的身份验证证书，该证书于一年之后过期。 目前，当证书即将过期时，PowerShell DSC 注册协议无法自动续订证书，因此你需要在一年之后重新注册这些节点。 在重新注册之前，请确保每个节点正在运行 Windows Management Framework 5.0 RTM。 如果节点的身份验证证书过期并且该节点尚未注册，则该节点将无法与 Azure 自动化通信，并将标记为“无响应”。 重新注册的执行方式与最初注册节点时相同。 与证书过期时间相距 90 天或更短时间内执行的注册，或者在证书过期时间之后任何时间点执行的注册，将生成新的证书并提供使用。
* 升级到 WMF 5 RTM 时，如果计算机已注册为 Azure 自动化 DSC 的节点，请从 Azure 自动化 DSC 将其注销，并且在 WMF 5 RTM 升级之后重新注册。 在重新注册之前，请删除 $env:windir\system32\configuration\DSCEngineCache.mof 文件。
* 如果将 WMF 5 RTM 安装在 WMF 5 Production Preview 上，PowerShell DSC cmdlet 可能无法正常运行。 若要解决此问题，请在提升权限的 PowerShell 会话（以管理员身份）中运行以下命令：`mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`

## <a name="related-articles"></a>相关文章
* [载入用于根据 Azure 自动化 DSC 管理的计算机](automation-dsc-onboarding.md)
* [在 Azure 自动化 DSC 中编译配置](automation-dsc-compile.md)
* [Azure 自动化 DSC cmdlet](https://msdn.microsoft.com/library/mt244122.aspx)
* [Azure 自动化 DSC 定价](https://azure.microsoft.com/pricing/details/automation/)
* [使用 Azure 自动化 DSC 和 Chocolatey 向 IaaS VM 持续部署](automation-dsc-cd-chocolatey.md)




<!--HONumber=Nov16_HO3-->


