---
title: 从现有服务器为 Azure Automation State Configuration 创建配置
description: 本文介绍了如何从现有服务器为 Azure Automation State Configuration 创建配置。
keywords: dsc,powershell,配置,安装程序
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8f376fb383e50a39f0f12d45cf9b5ae47ad6fcbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186497"
---
# <a name="create-configurations-from-existing-servers"></a>从现有服务器创建配置

> 适用于：Windows PowerShell 5.1

从现有服务器创建配置可能是一项有难度的任务。
你可能不想要配置所有设置，而只想配置你所关心的设置。
即使这样，你也需要知道必须按什么顺序应用设置，才能成功应用配置。

> [!NOTE]
> 本文引用了由开源社区维护的一个解决方案。
> 支持仅以 GitHub 协作形式提供，而不是由 Microsoft 提供。

## <a name="community-project-reversedsc"></a>社区项目：ReverseDSC

已创建一个名为 [ReverseDSC](https://github.com/microsoft/reversedsc) 的、由社区维护的解决方案，启动 SharePoint 后，该解决方案可在此区域中运行。

该解决方案是基于 [SharePointDSC 资源](https://github.com/powershell/sharepointdsc)构建的，并扩展了该资源，以从现有的 SharePoint 服务器[收集信息](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use)。
最新版本提供了多种[提取模式](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes)来确定要包含的信息级别。

使用该解决方案后，会生成可与 SharePointDSC 配置脚本配合使用的[配置数据](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data)。

生成数据文件后，可将其与 [DSC 配置脚本](/powershell/scripting/dsc/overview/overview)配合使用，以生成 MOF 文件，并[将 MOF 文件上传到 Azure 自动化](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation)。
然后，在[本地](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines)或[在 Azure 中](./automation-dsc-onboarding.md#enable-azure-vms)注册服务器以拉取配置。

若要试用 ReverseDSC，请访问 [PowerShell 库](https://www.powershellgallery.com/packages/ReverseDSC/)并下载该解决方案，或者单击“项目站点”以查看[文档](https://github.com/Microsoft/sharepointDSC.reverse)。

## <a name="next-steps"></a>后续步骤

- 若要了解 PowerShell DSC，请参阅 [Windows PowerShell Desired State Configuration 概述](/powershell/scripting/dsc/overview/overview)。
- 在 [DSC 资源](/powershell/scripting/dsc/resources/resources)中了解 PowerShell DSC 资源。
- 有关本地 Configuration Manager 配置的详细信息，请参阅[配置本地 Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)。
