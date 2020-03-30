---
title: 将配置转换为用于状态配置的复合资源 - Azure 自动化
description: 了解如何在 Azure 自动化中将配置转换为用于状态配置的复合资源。
keywords: dsc,powershell,配置,安装程序
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 05f0a81a738688df15ea9060071d9e266b54b7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136649"
---
# <a name="convert-configurations-to-composite-resources"></a>将配置转换为复合资源

> 适用于： Windows 电源外壳 5.1

开始创作配置以后，即可快速创建用于管理设置组的“方案”。
示例有：

- 创建 Web 服务器
- 创建 DNS 服务器
- 创建 SharePoint 服务器
- 配置 SQL 群集
- 管理防火墙设置
- 管理密码设置

若要将此工作与他人共享，最佳选项是将配置打包为[复合资源](/powershell/scripting/dsc/resources/authoringresourcecomposite)。
首次创建复合资源可能很困难。

> [!NOTE]
> 本文引用了一个由开放源代码社区维护的解决方案。
> 支持仅以 GitHub 协作的形式提供，而不是从 Microsoft 获得。

## <a name="community-project-compositeresource"></a>社区项目：综合资源

为了解决此难题，已创建一个名为 [CompositeResource](https://github.com/microsoft/compositeresource) 的社区维护解决方案。

CompositeResource 会自动完成从配置创建新模块的过程。
一开始可在工作站（或生成服务器）上对配置脚本进行[点源操作](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/)，将其加载到内存中。
接下来，使用 CompositeResource 模块提供的函数自动进行转换，而不是运行此配置来生成 MOF 文件。
此 cmdlet 会加载配置的内容、获取参数列表，以及生成包含所需的一切内容的新模块。

生成模块以后，每次进行更改并将其发布到你自己的 [PowerShellGet 存储库](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo)时，就可以将版本递增并添加发行说明。

创建包含一个或多个配置的复合资源模块以后，即可在 Azure 中的[可组合创作体验](/azure/automation/compose-configurationwithcompositeresources)中使用它们，或者将它们添加到 [DSC 配置脚本](/powershell/scripting/dsc/configurations/configurations)，以便生成 MOF 文件并[将 MOF 文件上传到 Azure 自动化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然后从[本地](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)或[在 Azure 中](/azure/automation/automation-dsc-onboarding#azure-virtual-machines)注册服务器以拉取配置。
对项目的最新更新也发布了 [runbook](https://www.powershellgallery.com/packages?q=DscGallerySamples)，供 Azure 自动化自动完成从 PowerShell 库导入配置的过程。

若要尝试自动创建用于 DSC 的复合资源，请访问 [PowerShell 库](https://www.powershellgallery.com/packages/compositeresource/)并下载解决方案，或单击“项目站点”以查看[文档](https://github.com/microsoft/compositeresource)。

## <a name="next-steps"></a>后续步骤

- [Windows PowerShell Desired State Configuration 概述](/powershell/scripting/dsc/overview/overview)
- [DSC 资源](/powershell/scripting/dsc/resources/resources)
- [配置本地配置管理器](/powershell/scripting/dsc/managing-nodes/metaconfig)
