---
title: 基于要在状态配置中使用的 STIG 的配置-Azure 自动化
description: 了解 Azure 自动化中基于 STIG 的配置的配置。
keywords: dsc, powershell, 配置, 安装程序
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 967a4542b3910b563dc30bde674e12fffdd12f48
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559447"
---
# <a name="configuration-based-on-stig"></a>基于 STIG 的配置

> 适用于：Windows PowerShell 5.1

首次创建配置内容可能很困难。
在许多情况下, 其目标是在 "基准" 下自动配置服务器, 这希望与行业建议保持一致。

> [!NOTE]
> 本文引用由开源社区维护的解决方案。
> 支持仅以 GitHub 协作形式提供, 不适用于 Microsoft。

## <a name="community-project-powerstig"></a>社区项目:PowerSTIG

名为[PowerSTIG](https://github.com/microsoft/powerstig)的社区项目旨在根据提供的有关 STIG 的[公共信息](https://public.cyber.mil/stigs/)(安全技术实现指南) 生成 DSC 内容来解决此问题。

处理基线比声音更复杂。
许多组织都需要[记录例外](https://github.com/microsoft/powerstig#powerstigdata)规则并大规模管理这些数据。
PowerSTIG 通过提供[复合资源](https://github.com/microsoft/powerstig#powerstigdsc)来处理配置的每个区域, 而不是尝试在一个大文件中对整个范围内的设置进行寻址, 来解决此问题。

生成配置后, 可以使用[DSC 配置脚本](/powershell/dsc/configurations)来生成 mof 文件, 并将[mof 文件上传到 Azure 自动化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然后从[本地](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws)或[Azure 中](/azure/automation/automation-dsc-onboarding#azure-virtual-machines)将服务器注册到请求配置。

若要试用 PowerSTIG, 请访问[PowerShell 库](http://www.powershellgallery.com)并下载解决方案, 或者单击 "项目网站" 查看[文档](https://github.com/microsoft/powerstig)。

## <a name="next-steps"></a>后续步骤

- [Windows PowerShell Desired State Configuration 概述](/powershell/dsc/overview/overview)
- [DSC 资源](/powershell/dsc/resources/resources)
- [配置本地 Configuration Manager](/powershell/dsc/managing-nodes/metaconfig)
