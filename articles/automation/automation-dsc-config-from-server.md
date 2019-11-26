---
title: 从现有服务器创建配置-Azure 自动化
description: 了解如何在 Azure 自动化的现有服务器中创建配置。
keywords: dsc，powershell，配置，安装程序
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 35f967e946854c3ca097db379015a7ee0bbe2f3d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231691"
---
# <a name="create-configurations-from-existing-servers"></a>从现有服务器创建配置

> 适用于： Windows PowerShell 5。1

从现有服务器创建配置可能是一件非常困难的任务。
您可能不希望进行*所有*设置，只是您关心的设置。
即使这样，也需要了解设置的应用顺序，以便成功地应用配置。

> [!NOTE]
> 本文引用由开源社区维护的解决方案。
> 支持仅以 GitHub 协作形式提供，不适用于 Microsoft。

## <a name="community-project-reversedsc"></a>社区项目： ReverseDSC

已创建一个名为[ReverseDSC](https://github.com/microsoft/reversedsc)的社区维护解决方案，以便在此区域中开始使用 SharePoint。

此解决方案在[SharePointDSC 资源](https://github.com/powershell/sharepointdsc)的基础上进行扩展，并将其扩展为从现有 SharePoint 服务器中[收集信息](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use)。
最新版本具有多个[提取模式](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes)，以确定要包含的信息级别。

使用该解决方案的结果是生成要用于 SharePointDSC 配置脚本的[配置数据](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data)。

生成数据文件后，可以将其与[DSC 配置脚本](/powershell/scripting/dsc/overview/overview)一起使用，以生成 mof 文件并[将 mof 文件上传到 Azure 自动化](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)。
然后从[本地](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws)或[Azure 中](/azure/automation/automation-dsc-onboarding#azure-virtual-machines)将服务器注册到请求配置。

若要试用 ReverseDSC，请访问[PowerShell 库](https://www.powershellgallery.com/packages/ReverseDSC/)并下载解决方案，或者单击 "项目网站" 查看[文档](https://github.com/Microsoft/sharepointDSC.reverse)。

## <a name="next-steps"></a>后续步骤

- [Windows PowerShell Desired State Configuration 概述](/powershell/scripting/dsc/overview/overview)
- [DSC 资源](/powershell/scripting/dsc/resources/resources)
- [配置本地 Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)
