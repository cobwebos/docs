---
title: "使用 OMS 更新管理中的 SCCM 集合确定更新目标 | Microsoft Docs"
description: "本文的目标是帮助你使用此解决方案配置 System Center Configuration Manager 以管理 SCCM 托管计算机的更新。"
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: eslesar
ms.openlocfilehash: ec97432cd14c6289928f0419c242e1ccc2c8d876
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="integrate-system-center-configuration-manager-with-oms-update-management"></a>将 System Center Configuration Manager 与 OMS 更新管理集成

在软件更新管理 (SUM) 周期中，已经投资购买 System Center Configuration Manager 来管理电脑、服务器和移动设备的客户还可以依赖其在管理软件更新方面的优势和成熟度。  

基于 OMS 与 Configuration Manager 之间目前已有的集成，可以通过在 Configuration Manager 中创建和预暂存软件更新部署来报告和更新托管 Windows 服务器，并使用[更新管理解决方案](../operations-management-suite/oms-solution-update-management.md)获取已完成的更新部署的详细状态。 如果使用 Configuration Manager 提供 Windows 服务器的更新合规性报告而不使用它管理更新部署，则可以继续向 Configuration Manager 进行报告，而使用 OMS 更新管理解决方案管理安全更新。

## <a name="prerequisites"></a>先决条件

* 必须将[更新管理解决方案](../operations-management-suite/oms-solution-update-management.md)添加到 Log Analytics 工作区并将其与同一资源组和区域中的自动化帐户链接在一起。   
* 当前由 System Center Configuration Manager 环境管理的 Windows 服务器还需要向也启用了更新管理解决方案的 Log Analytics 工作区进行报告。  
* 此功能支持 System Center Configuration Manager 当前分支版本 1606 和更高版本。  若要将 Configuration Manager 中心管理站点或独立主站点与 Log Analytics 和重要集合进行集成，请查看[将 Configuration Manager 连接到 Log Analytics](../log-analytics/log-analytics-sccm.md)。  
* 如果 Windows 代理不从 Configuration Manager 接收安全更新，则它们必须配置为与 Windows Server Update Services (WSUS) 服务器进行通信或有权访问 Microsoft 更新。   

如何使用现有 Configuration Manager 环境管理 Azure IaaS 中托管的客户端主要取决于已在 Azure 数据中心与基础结构之间建立的连接。 此连接会影响你可能需要对 Configuration Manager 基础结构所做的任何设计更改，还会影响与支持这些必要更改相关的成本。  若要了解在继续操作之前需要评估哪些规划注意事项，请查看 [Azure 上的 Configuration Manager - 常见问题解答](https://docs.microsoft.com/sccm/core/understand/configuration-manager-on-azure#networking)。    

## <a name="configuration"></a>配置

### <a name="manage-software-updates-from-configuration-manager"></a>从 Configuration Manager 管理软件更新 

如果打算继续从 Configuration Manager 管理更新部署，请执行以下步骤。  OMS 连接到 Configuration Manager 来向连接到 Log Analytics 工作区的客户端计算机应用更新。 可以从客户端计算机缓存获取更新内容，就像部署是由 Configuration Manager 管理的一样。  

1. 使用 [deploy software update process](https://docs.microsoft.com/sccm/sum/deploy-use/deploy-software-updates)（部署软件更新过程）中介绍的过程从 Configuration Manager 层次结构中的顶层站点创建软件更新部署。  与标准部署不同的必须配置的唯一设置是选项“不安装软件更新”，此选项用于控制部署包的下载行为。 此行为是通过在下一步骤中创建计划的更新部署而通过 OMS 更新管理解决方案管理的。  

1. 在 OMS 门户中，打开“更新管理”仪表板。  根据[创建更新部署](../operations-management-suite/oms-solution-update-management.md#creating-an-update-deployment)中介绍的步骤创建一个新部署，并从下拉列表中选择表示为 OMS 计算机组的相应 Configuration Manager 集合。  请记住以下要点：
    1. 如果为所选的 Configuration Manager 设备集合定义了维护窗口，则它将存储在集合的成员中而不是存储在 OMS 中的计划的部署中定义的“持续时间”设置中。
    1. 目标集合的成员必须连接到 Internet（直接连接、通过代理服务器或者通过 OMS 网关）。  

使用 OMS 解决方案完成更新部署后，属于所选计算机组的成员的目标计算机将按计划的时间从本地客户端缓存中安装更新。  可以[查看更新部署状态](../operations-management-suite/oms-solution-update-management.md#viewing-update-deployments)来监视部署结果。  


### <a name="manage-software-updates-from-oms"></a>从 OMS 管理软件更新

若要从是 Configuration Manager 客户端的 Windows Server VM 管理更新，需要配置客户端策略来为由此解决方案管理的所有客户端禁用软件更新管理功能。  默认情况下，客户端设置以层次结构中的所有设备为应用目标。  有关此策略设置以及如何配置此设置的详细信息，请查看[如何在 System Center Configuration Manager 中配置客户端设置](https://docs.microsoft.com/sccm/core/clients/deploy/configure-client-settings)。  

在执行此配置更改后，根据[创建更新部署](../operations-management-suite/oms-solution-update-management.md#creating-an-update-deployment)中介绍的步骤创建一个新部署，并从下拉列表中选择表示为 OMS 计算机组的相应 Configuration Manager 集合。 

