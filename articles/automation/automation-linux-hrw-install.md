---
title: "Azure 自动化 Linux 混合 Runbook 辅助角色 | Microsoft Docs"
description: "本文介绍如何安装 Azure 自动化混合 Runbook 辅助角色，该角色可以用于在本地数据中心或云环境的基于 Linux 的计算机上运行 Runbook。"
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 88c052c3a22611b796559d4dd62c763445aa6210
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>如何部署 Linux 混合 Runbook 辅助角色
Azure 自动化中的 Runbook 无法访问其他云或本地环境中的资源，因为它们在 Azure 云中运行。  利用 Azure 自动化的混合 Runbook 辅助角色功能，既可以直接在托管角色的计算机上运行 Runbook，也可以对环境中的资源运行 Runbook，从而管理这些本地资源。 Runbook 在 Azure 自动化中进行存储和管理，然后发送到一个或多个指定计算机。  

下图说明了此功能：<br>  

![混合 Runbook 辅助角色概述](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

有关混合 Runbook 辅助角色的技术概述，请参阅[自动化体系结构概述](automation-offering-get-started.md#automation-architecture-overview)。 开始部署混合 Runbook 辅助角色之前，请先查看以下信息：[硬件和软件要求](automation-offering-get-started.md#hybrid-runbook-worker)以及[网络准备相关信息](automation-offering-get-started.md#network-planning)。  成功部署 Runbook 辅助角色后，请查看[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)，了解如何配置 Runbook，使本地数据中心或其他云环境中的过程实现自动化。     

## <a name="hybrid-runbook-worker-groups"></a>混合 Runbook 辅助角色组
每个混合 Runbook 辅助角色都是你在安装代理时指定的混合 Runbook 辅助角色组的成员。  一个组可以包含一个代理，但是可以在一个组中安装多个代理，以实现高可用性。

在混合 Runbook 辅助角色中启动 Runbook 时，可以指定该辅助角色会在其中运行的组。  组的成员会决定由哪个辅助角色来处理请求。  不能指定特定的辅助角色。

## <a name="installing-linux-hybrid-runbook-worker"></a>安装 Linux 混合 Runbook 辅助角色
若要在 Linux 计算机上安装和配置混合 Runbook 辅助角色，请按照一个非常简单的过程手动安装和配置此角色。   它需要启用 OMS 工作区中的“自动化混合辅助角色”解决方案，然后运行一组命令将计算机注册为辅助角色，并且将它添加至一个新组或现有的组中。 

继续前，需要记录自动化帐户关联的 Log Analytics 工作区及自动化帐户的主键。  通过选择自动化帐户，选择工作区 ID 的“工作区”及选择主键的“键”，可从门户中找到这两者。  

1.  启用 OMS 中的“自动化混合辅助角色”解决方案。 可通过以下任一方式实现：

   1. 从 [OMS 门户](https://mms.microsoft.com)中的解决方案库启用“自动化混合辅助角色”解决方案 
   2. 运行以下 cmdlet：

        ```powershell
         $null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

2.  在 Linux 计算机上运行以下命令来更改 -w 和 -k 参数的值。
    
    ```
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <OMSworkspaceId> -k <AutomationSharedKey> --groupname <hybridgroupname> -e <automationendpoint>
    ```
3. 此命令完成后，Azure 门户中的“混合辅助角色组”边栏选项卡会显示新组和成员数，或者会将成员数进行递增（如果组已存在）。  可以从“混合辅助角色组”边栏选项卡上的列表中选择组，并选择“混合辅助角色”磁贴。  在“混合辅助角色”边栏选项卡上，会列出组的每个成员。  

## <a name="next-steps"></a>后续步骤

* 查看[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)，了解如何配置 Runbook，使本地数据中心或其他云环境中的过程实现自动化。
* 若要获取有关如何删除混合 Runbook 辅助角色的说明，请参阅[删除 Azure 自动化混合 Runbook 辅助角色](automation-remove-hrw.md)