---
title: 将配置转换为用于状态配置的复合资源-Azure 自动化
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
ms.openlocfilehash: c060462a84b197cdd0aec1beecc0b4d4807feb49
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030110"
---
# <a name="convert-configurations-to-composite-resources"></a>将配置转换为复合资源

> 适用于： Windows PowerShell 5。1

开始创作配置后，你可以快速创建 "方案" 来管理设置组。
示例有：

- 创建 web 服务器
- 创建 DNS 服务器
- 创建 SharePoint 服务器
- 配置 SQL 群集
- 管理防火墙设置
- 管理密码设置

如果你有兴趣与他人共享此操作，最好的方法是将配置打包为[复合资源](/powershell/scripting/dsc/resources/authoringresourcecomposite)。
首次创建复合资源可能会很困难。

> [!NOTE]
> 本文引用由开源社区维护的解决方案。
> 支持仅以 GitHub 协作形式提供，不适用于 Microsoft。

## <a name="community-project-compositeresource"></a>社区项目： CompositeResource

已创建一个名为[CompositeResource](https://github.com/microsoft/compositeresource)的社区维护解决方案来解决这一难题。

CompositeResource 自动执行从配置创建新模块的过程。
首先，在工作站（或生成服务器）上以[点](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/)为起点，使其加载到内存中。
接下来，请使用 CompositeResource 模块提供的函数自动执行转换，而不是运行配置以生成 MOF 文件。
该 cmdlet 将加载配置的内容，获取参数列表，并使用所需的所有内容生成新的模块。

生成模块后，你可以在每次进行更改时递增版本并添加发行说明，并将其发布到自己的[PowerShellGet 存储库](https://kevinmarquette.github.io/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo)。

创建包含配置（或多个配置）的复合资源模块后，可以在 Azure 中的可[撰写创作体验](/azure/automation/compose-configurationwithcompositeresources)中使用它们，或将其添加到[DSC 配置脚本](/powershell/scripting/dsc/configurations/configurations)以生成 mof 文件，并将[Mof 文件上传到 Azure 自动化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然后从[本地](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)或[Azure 中](/azure/automation/automation-dsc-onboarding#azure-virtual-machines)将服务器注册到请求配置。
项目的最新更新还发布了适用于 Azure 自动化的[runbook](https://www.powershellgallery.com/packages?q=DscGallerySamples) ，以便自动从 PowerShell 库导入配置。

若要尝试自动创建 DSC 的复合资源，请访问[PowerShell 库](https://www.powershellgallery.com/packages/compositeresource/)并下载解决方案，或单击 "项目网站" 查看[文档](https://github.com/microsoft/compositeresource)。

## <a name="next-steps"></a>后续步骤

- [Windows PowerShell Desired State Configuration 概述](/powershell/scripting/dsc/overview/overview)
- [DSC 资源](/powershell/scripting/dsc/resources/resources)
- [配置本地配置管理器](/powershell/scripting/dsc/managing-nodes/metaconfig)
