---
title: Azure Stack 集成系统连接模型 | Microsoft Docs
description: 确定多节点 Azure Stack 的部署规划决策。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 9760e6b9cdcd6f03f4377277f3426189b1fe0a61
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182011"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Azure Stack 集成系统连接模型
如果有兴趣购买 Azure Stack 集成系统，您需要了解[多个数据中心集成注意事项](azure-stack-datacenter-integration.md)为 Azure Stack 部署确定系统将如何适应你的数据中心。 此外，你将需要决定如何将将 Azure Stack 集成到混合云环境。 本文概述了这些主要决策，包括 Azure 连接决策、标识存储决策和计费模型决策。

如果你决定购买一个集成系统，原始设备制造商 (OEM) 硬件供应商可帮助更详细地指导你完成大部分规划过程。 他们还将执行实际部署。

## <a name="choose-an-azure-stack-deployment-connection-model"></a>选择 Azure Stack 部署连接模型
可以选择在连接到 Internet（和 Azure）时还是断开连接时部署 Azure Stack。 若要从 Azure Stack（包括 Azure Stack 和 Azure 之间的混合方案）获得最大效益，建议在连接到 Azure 时进行部署。 此选项定义了哪些选项可用于标识存储（Azure Active Directory 或 Active Directory 联合身份验证服务）和计费模型（基于“使用才支付”计费或基于容量计费），如以下图和表中所总结： 

![Azure Stack 部署和计费方案](media/azure-stack-connection-models/azure-stack-scenarios.png)  
  
> [!IMPORTANT]
> 这是关键决策点！ 选择 Active Directory 联合身份验证服务 (AD FS) 还是 Azure Active Directory (Azure AD) 是在部署时必须进行的一次性决策。 以后，除非重新部署整个系统，否则将无法更改此设置。  


|选项|已连接到 Azure|已与 Azure 断开连接|
|-----|-----|-----|
|Azure AD|![支持](media/azure-stack-connection-models/check.png)| |
|AD FS|![支持](media/azure-stack-connection-models/check.png)|![支持](media/azure-stack-connection-models/check.png)|
|基于使用的计费|![支持](media/azure-stack-connection-models/check.png)| |
|基于容量的计费|![支持](media/azure-stack-connection-models/check.png)|![支持](media/azure-stack-connection-models/check.png)|
|将更新包直接下载到 Azure Stack|![支持](media/azure-stack-connection-models/check.png)|  |

选定 Azure Stack 部署要使用的 Azure 连接模型后，必须对标识存储和计费方法做出其他与连接相关的决策。 

## <a name="next-steps"></a>后续步骤

[已连接 Azure 的 Azure Stack 部署决策](azure-stack-connected-deployment.md)

[与 Azure 断开连接的 Azure Stack 部署决策](azure-stack-disconnected-deployment.md)
