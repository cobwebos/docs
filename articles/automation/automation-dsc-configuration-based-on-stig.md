---
title: 要在 State Configuration 中使用的基于 STIG 的配置 - Azure 自动化
description: 了解基于 STIG 的配置以用于 Azure 自动化中的 State Configuration。
keywords: dsc,powershell,配置,安装程序
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d6d257198fcae54b1214d77f6b905d876d2687f5
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585532"
---
# <a name="configuration-based-on-stig"></a>基于 STIG 的配置

> 适用于： Windows 电源外壳 5.1

首次创建配置内容可能很困难。
在许多情况下，目标是按照希望符合行业建议的“基线”自动配置服务器。

> [!NOTE]
> 本文引用了一个由开放源代码社区维护的解决方案。
> 支持仅以 GitHub 协作的形式提供，而不是从 Microsoft 获得。

## <a name="community-project-powerstig"></a>社区项目：PowerSTIG

一个名为 [PowerSTIG](https://github.com/microsoft/powerstig) 的社区项目旨在通过基于提供的关于 STIG（安全技术实施指南）的[公共信息](https://public.cyber.mil/stigs/)生成 DSC 内容来解决此问题，

处理基线比听起来要复杂得多。
许多组织需要[记录规则的例外](https://github.com/microsoft/powerstig#powerstigdata)并大规模管理该数据。
PowerSTIG 通过提供[复合资源](https://github.com/microsoft/powerstig#powerstigdsc)来处理配置的每个区域，而不是尝试在一个大文件中处理整个设置范围，从而解决了此问题。

生成配置后，可以使用 [DSC 配置脚本](/powershell/scripting/dsc/configurations/configurations)生成 MOF 文件，并[将 MOF 文件上传到 Azure 自动化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然后从[本地](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)或[在 Azure 中](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms)注册服务器以拉取配置。

若要试用 PowerSTIG，请访问 [PowerShell 库](https://www.powershellgallery.com)并下载解决方案，或单击“项目站点”以查看[文档](https://github.com/microsoft/powerstig)。

## <a name="next-steps"></a>后续步骤

- [Windows PowerShell Desired State Configuration 概述](/powershell/scripting/dsc/overview/overview)
- [DSC 资源](/powershell/scripting/dsc/resources/resources)
- [配置本地配置管理器](/powershell/scripting/dsc/managing-nodes/metaconfig)
