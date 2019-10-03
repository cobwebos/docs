---
title: 大规模的配置数据-Azure 自动化
description: 了解如何在 Azure 自动化中的状态配置的规模上配置数据。
keywords: dsc，powershell，配置，安装程序
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f6bb89370c85389d6c41306ed224d27d710cd7c8
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559522"
---
# <a name="configuration-data-at-scale"></a>大规模配置数据

> 适用于：Windows PowerShell 5.1

管理成百上千的服务器可能是一项挑战。
客户提供的反馈是最困难的方面是实际管理[配置数据](/powershell/dsc/configurations/configdata)。
跨逻辑构造（如位置、类型和环境）组织信息。

> [!NOTE]
> 本文引用由开源社区维护的解决方案。
> 支持仅以 GitHub 协作形式提供，不适用于 Microsoft。

## <a name="community-project-datum"></a>社区项目：选取

[已创建](https://github.com/gaelcolas/Datum)一个社区维护解决方案，用于解决这一难题。
基准是从其他配置管理平台中获得极佳创意，并为 PowerShell DSC 实现了相同类型的解决方案。
信息根据逻辑创意[组织到文本文件中](https://github.com/gaelcolas/Datum#3-intended-usage)。
示例如下：

- 应全局应用的设置
- 应该应用于位置中所有服务器的设置
- 应该应用于所有数据库服务器的设置
- 单个服务器设置

此信息按你喜欢的文件格式（JSON、Yaml 或 PSD1）进行组织。
然后，通过将每个文件中[的信息合并](https://github.com/gaelcolas/Datum#datum-tree)到服务器或服务器角色的单一视图中，提供 cmdlet 以生成配置数据文件。

生成数据文件后，可以将其与[DSC 配置脚本](/powershell/dsc/configurations/write-compile-apply-configuration)一起使用，以生成 mof 文件并[将 mof 文件上传到 Azure 自动化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然后从[本地](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws)或[Azure 中](/azure/automation/automation-dsc-onboarding#azure-virtual-machines)将服务器注册到请求配置。

若要试用基准，请访问[PowerShell 库](https://www.powershellgallery.com/packages/datum/)并下载解决方案，或者单击 "项目网站" 查看[文档](https://github.com/gaelcolas/Datum#2-getting-started--concepts)。

## <a name="next-steps"></a>后续步骤

- [Windows PowerShell Desired State Configuration 概述](/powershell/dsc/overview/overview)
- [DSC 资源](/powershell/dsc/resources/resources)
- [配置本地 Configuration Manager](/powershell/dsc/managing-nodes/metaconfig)
