---
title: "介绍为 Azure 堆栈开发工具包 (ASDK) |Microsoft 文档"
description: "介绍 ASDK 和评估 Microsoft Azure 堆栈的常见用例。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 5a268a29c7a767084049bf56270aa8bc9d2ccc3f
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="what-is-the-azure-stack-development-kit"></a>什么是 Azure 堆栈开发工具包？
[Microsoft Azure 堆栈集成系统](.\.\azure-stack-poc.md)大小从 4-12 节点和硬件合作伙伴和 Microsoft 共同支持。 使用 Azure Stack集成系统为自己的生产工作负荷实现新方案。 如果你管理的集成的系统基础结构和提供服务 Azure 堆栈操作员，请参阅我们[运算符文档](https://docs.microsoft.com/azure/azure-stack)。

Azure 堆栈开发工具包 (ASDK) 是单节点部署的 Azure 堆栈，你可以下载和使用**免费**。 所有 ASDK 组件都安装在虚拟机在单个主机服务器计算机上运行，必须满足或超过[最低硬件要求](asdk-deploy-considerations.md#hardware)。 ASDK 旨在提供一种环境可以在其中评估 Azure 堆栈和开发现代应用程序使用的 Api 和工具与 Azure 一致*非生产*环境。 

> [!IMPORTANT]
> ASDK 不应直接使用或在生产环境中受支持。

因为所有 ASDK 组件部署到单个开发工具包主机计算机，有很有限的物理资源可用。 在 ASDK 部署时，Azure 堆栈基础结构 Vm 和租户 Vm 并存于相同的服务器计算机。 此配置不适用于规模或性能评估。

ASDK 旨在提供有关 Azure 一致的混合云体验：
- **管理员**（Azure 堆栈运算符）。 ASDK 是评估并了解有关可用 Azure 堆栈服务的重大资源。
- **开发人员**。 ASDK 可用来开发混合或现代应用程序本地 （开发/测试环境）。 这带来了可再现性之前，或与 Azure 堆栈生产部署的开发体验。 

观看此简短的视频，若要了解有关 ASDK 的详细信息：

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>ASDK 和多节点 Azure 堆栈差异
单节点 ASDK 部署与 Azure 堆栈的多节点部署在你应注意的几个重要方面不同。

|说明|ASDK|多节点 Azure 堆栈|
|-----|-----|-----|
|**缩放**|在单节点服务器计算机上安装所有组件。|范围可以是从 4-12 节点的大小。|
|**复原能力**|单节点配置不提供高可用性|[高可用性](.\.\azure-stack-key-features.md#high-availability-for-azure-stack)支持功能。|
|**网络**|ASDK 使用名为 AzS BGPNAT01 的 VM 路由所有 ASDK 网络流量。 没有其他交换机要求。|AzS BGPNAT01 VM 的多节点部署中不存在。 更复杂[网络路由基础结构](.\.\azure-stack-network.md#network-infrastructure)需要包括柜式 (TOR)、 基板管理控制器 (BMC) 和边框 （数据中心网络） 开关。|
|**修补程序和更新过程**|若要移动到新版本的 ASDK，你必须重新部署 ASDK 开发工具包主机计算机上。|[修补和更新](.\.\azure-stack-updates.md)用于更新安装的 Azure 堆栈版本的过程。|
|**支持**|MSDN Azure 堆栈论坛。 Microsoft 客户服务和支持 (CSS) 支持*不*可用于非生产环境。|[MSDN Azure 堆栈论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack)和完整 CSS 支持。|
| | |

## <a name="learn-about-available-services"></a>了解可用的服务
作为 Azure 堆栈操作员，你需要知道哪些服务可提供给你的用户。 Azure 堆栈支持一部分 Azure 服务和支持的服务的列表将继续随时间不断发展。

### <a name="foundational-services"></a>基本服务
默认情况下，Azure 堆栈包括以下"基本服务"当部署 ASDK:
- 计算
- 存储
- 网络
- Key Vault

有了这些基本服务，在向用户提供基础结构即服务 (IaaS) 时就可以尽量减少配置。

### <a name="additional-services"></a>其他服务
目前，支持以下的其他平台作为-服务 (PaaS) 服务：
- 应用服务
- Azure Functions
- SQL 和 MySQL 数据库

> [!NOTE]
> 这些服务之前你可以使它们可供你的用户需要其他配置和安装 ASDK 时不可用默认情况下。

## <a name="service-roadmap"></a>服务路线图
Azure 堆栈将继续添加其他 Azure 服务的支持。 若要了解有关即将推出 Azure 堆栈与下一步，请参阅[Azure 堆栈路线图](https://azure.microsoft.com/roadmap/?tag=azure-stack)。 


## <a name="next-steps"></a>后续步骤
若要开始评估 Azure 堆栈，你需要准备开发工具包宿主服务器计算机，然后[安装 ASDK](asdk-deploy.md)。 之后，可以在你登录的管理员和用户门户若要开始使用 Azure 堆栈。