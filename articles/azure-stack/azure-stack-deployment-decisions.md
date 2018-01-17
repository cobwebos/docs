---
title: "Azure 堆栈的部署决策集成系统 |Microsoft 文档"
description: "确定部署规划多节点 Azure 堆栈的决策。"
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
ms.topic: article
ms.date: 01/16/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: d4aaf5af4fdb9ff5d185ef14333db4e204b9a955
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2018
---
# <a name="deployment-planning-decisions-for-azure-stack-integrated-systems"></a>部署规划 Azure 堆栈的决策集成系统
如果你感兴趣的集成的 Azure 堆栈系统，你将需要了解[几个规划注意事项](azure-stack-deployment-planning.md)Azure 堆栈部署，然后确定系统将如何适应你的数据中心。 此外，你将需要决定完全你将如何集成 Azure 堆栈到混合云环境。 本文概述了这些主要决定包括 Azure 连接，标识存储和计费模型的决策。

如果你决定购买一个集成的系统，原始设备制造商 (OEM) 硬件供应商可帮助指导你完成大部分详细的规划过程。 它们将执行实际的部署。

## <a name="choose-an-azure-stack-deployment-connection-model"></a>选择 Azure 堆栈部署连接模型
你可以选择部署 Azure 堆栈连接到 internet （和 Azure） 或断开连接。 若要从 Azure 堆栈，包括 Azure 堆栈和 Azure 之间的混合方案中获取最大益处你想要部署连接到 Azure。 选择此选项时定义了用于标识应用商店 （Azure Active Directory 或 Active Directory 联合身份验证服务） 和计费模型可用的选项 (当你使用基于付费计费或基于容量的计费) 下面的图和表中进行了总结： 

![Azure 堆栈部署和计费方案](media/azure-stack-deployment-decisions/azure-stack-scenarios.png)   
  
> [!IMPORTANT]
> 这是一个关键决策点 ！ 选择 Active Directory 联合身份验证服务 (AD FS) 或 Azure Active Directory (Azure AD) 是在部署时必须进行一次性决策。 你不能以后更改无需重新部署整个系统。  


|选项|连接到 Azure|从 Azure 断开连接|
|-----|-----|-----|
|Azure AD|![支持](media/azure-stack-deployment-decisions/check.png)| |
|AD FS|![支持](media/azure-stack-deployment-decisions/check.png)|![支持](media/azure-stack-deployment-decisions/check.png)|
|基于消费的计费|![支持](media/azure-stack-deployment-decisions/check.png)| |
|基于容量的计费|![支持](media/azure-stack-deployment-decisions/check.png)|![支持](media/azure-stack-deployment-decisions/check.png)|
|直接向 Azure 堆栈下载更新包|![支持](media/azure-stack-deployment-decisions/check.png)|  |

您已决定在 Azure 的连接模型上要用于 Azure 堆栈部署后，必须标识应用商店应用和计费方法进行其他、 连接相关的决策。 

## <a name="next-steps"></a>后续步骤
- 详细了解[Azure 连接 Azure 堆栈部署决策](azure-stack-connected-deployment.md)
- 详细了解[Azure 断开连接 Azure 堆栈部署决策](azure-stack-disconnected-deployment.md)
