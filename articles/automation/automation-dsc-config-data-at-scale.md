---
title: 大规模配置数据 - Azure 自动化
description: 了解如何为 Azure 自动化中的 State Configuration 大规模配置数据。
keywords: dsc,powershell,配置,设置
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 644ea1c00af7e71ff56852298fff18e5293c137b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585569"
---
# <a name="configuration-data-at-scale"></a>大规模配置数据

> 适用于：Windows PowerShell 5.1

管理成百上千的服务器可能是一项挑战。
客户提供的反馈表明，最困难的方面实际上是管理[配置数据](/powershell/scripting/dsc/configurations/configdata)。
跨位置、类型和环境等逻辑结构组织信息。

> [!NOTE]
> 本文引用了一个由开放源代码社区维护的解决方案。
> 支持仅以 GitHub 协作形式提供，不适用于 Microsoft。

## <a name="community-project-datum"></a>社区项目：Datum

为了解决此难题，已创建一个名为 [Datum](https://github.com/gaelcolas/Datum) 的社区维护解决方案。
Datum 基于其他配置管理平台的出色构想，并为 PowerShell DSC 实现了相同类型的解决方案。
根据逻辑思想将信息[组织到文本文件中](https://github.com/gaelcolas/Datum#3-intended-usage)。
示例如下：

- 应全局应用的设置
- 应当应用于位置中所有服务器的设置
- 应当应用于所有数据库服务器的设置
- 单个服务器设置

这些信息以你喜欢的文件格式（JSON、Yaml 或 PSD1）进行组织。
然后，通过将每个文件中的[信息合并](https://github.com/gaelcolas/Datum#datum-tree)到服务器或服务器角色的单个视图中，提供 cmdlet 来生成配置数据文件。

生成数据文件后，可以在 [DSC 配置脚本](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)中使用这些文件生成 MOF 文件，并[将 MOF 文件上传到 Azure 自动化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然后从[本地](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)或[在 Azure 中](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms)注册服务器以拉取配置。

若要试用 Datum，请访问 [PowerShell 库](https://www.powershellgallery.com/packages/datum/)并下载解决方案，或单击“项目站点”以查看[文档](https://github.com/gaelcolas/Datum#2-getting-started--concepts)。

## <a name="next-steps"></a>后续步骤

- [Windows PowerShell Desired State Configuration 概述](/powershell/scripting/dsc/overview/overview)
- [DSC 资源](/powershell/scripting/dsc/resources/resources)
- [配置本地配置管理器](/powershell/scripting/dsc/managing-nodes/metaconfig)
