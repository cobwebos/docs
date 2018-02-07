---
title: "有关 Azure 堆栈的 azure 连接的部署决策集成系统 |Microsoft 文档"
description: "确定部署规划 Azure 堆栈 Azure 连接的多节点部署的决策。"
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
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: a2d4efc3a3e1480de71528144ae3f025f4879f07
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>规划 Azure 堆栈的决策的 azure 连接的部署集成系统
您已决定后[如何将将 Azure 堆栈集成到你的混合云环境](azure-stack-deployment-decisions.md)，然后才能最终完成 Azure 堆栈的部署决策。

连接到 Azure 的部署 Azure 堆栈意味着只能用于为标识存储有 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS)。 你还可以选择从任一计费模型： 为你的使用付费或基于容量的。 已连接的部署是默认选项，因为允许客户获得最大值超出了 Azure 堆栈，特别是对于涉及 Azure 和 Azure 堆栈的混合云方案。 

## <a name="choose-an-identity-store"></a>选择一个标识存储区
与连接部署，你可以选择 Azure AD 或 AD FS 为标识存储区之间。 断开连接的部署，未连接 internet，只能使用 AD FS。

标识存储自选租户虚拟机 (Vm) 上没有任何影响。 租户 Vm 可以选择他们想要根据配置方式连接到哪个标识存储： Azure AD 中，Windows Server Active Directory 加入域的工作组，等等。这是与 Azure 堆栈标识提供程序决策不相关。 

例如，如果你部署 IaaS 租户位于 Azure 堆栈顶部的 Vm，并且想要加入企业 Active Directory 域和使用来自那里的帐户，你仍然可以这样做。 不需要为这些帐户使用您在此处选择的 Azure AD 身份存储库。

### <a name="azure-ad-identity-store"></a>Azure AD 身份存储库
当你使用 Azure AD 为你的标识存储需要两个 Azure AD 帐户： 全局管理员帐户和计费帐户。 这些帐户可以是相同的帐户或不同的帐户。 而使用相同的用户帐户可能更简单且有用的如果你有有限的数量的 Azure 帐户，你的业务需求可能会建议使用两个帐户：

1. **全局管理员帐户**（仅需要连接的部署）。 这是用于在 Azure Active Directory 中创建应用程序和 Azure 堆栈基础结构服务的服务主体的 Azure 帐户。 此帐户必须具有到你的 Azure 堆栈系统将下部署的目录的目录管理员权限。 它将成为"云操作员"全局管理员的 Azure AD 租户，并将使用： 
    - 设置和委托应用程序和需要与 Azure Active Directory 和 Graph API 进行交互的所有 Azure 堆栈服务的服务主体。 
    - 作为服务管理员帐户。 这是默认提供程序订阅 （这可以在以后更改） 的所有者。 你可以登录到 Azure 堆栈管理员门户具有此帐户，并可以使用它创建提议和计划、 设置配额，并在 Azure 堆栈中执行其他管理功能。
2. **计费帐户**（必需连接和断开连接部署）。 此 Azure 帐户用于建立你 Azure 堆栈集成的系统和 Azure 商务后端之间的计费关系。 这是为 Azure 堆栈费用将会收取费用的帐户。 此帐户还将用于应用商店联合和其他混合方案。 

### <a name="ad-fs-identity-store"></a>AD FS 身份存储库
如果你想要使用您自己的标识存储，例如你公司的 Active Directory，为服务管理员帐户，请选择此选项。  

## <a name="choose-a-billing-model"></a>选择计费模型
你可以任选一个**作为你的使用付费**或**容量**计费模型。 为你的使用付费计费模型部署必须能够通过与 Azure 的连接每 30 天至少一次报告使用情况，因此，如果连接将不可用，则容量计费模型是唯一的选项。 

### <a name="pay-as-you-use"></a>为你的使用付费
使用作为你的使用付费计费模型，到 Azure 订阅计费使用情况。 你只需付费时使用的 Azure 堆栈服务。 如果这是你决定的模型，你将需要 Azure 订阅和与该订阅关联的帐户 ID (例如， serviceadmin@contoso.onmicrosoft.com)。 支持 EA、 CSP 和 CSL 订阅。 使用情况报告期间配置[Azure 堆栈注册](azure-stack-registration.md)。

> [!NOTE]
> 在大多数情况下，企业客户将会使用 EA 订阅和服务提供商将使用 CSP 或 CSL 订阅。

如果想要使用 CSP 订阅，请查看下为确定哪种 CSP 订阅若要使用，如的正确方法取决于具体的 CSP 方案表：

|方案|域和订阅选项|
|-----|-----|
|你是**直接 CSP 合作伙伴**或**间接 CSP 提供程序**，和将运行 Azure 堆栈|使用 CSL （通用服务层） 订阅。<br>     或<br>创建 Azure AD 租户中合作伙伴中心的描述性名称。 例如&lt;你的组织 > CSPAdmin Azure CSP 订阅与之关联。|
|你是**间接 CSP 分销商**，和将运行 Azure 堆栈|向间接 CSP 提供商创建一个与它使用合作伙伴中心关联的 Azure CSP 订阅与你的组织的 Azure AD 租户。|

### <a name="capacity-based-billing"></a>容量基于的计费
如果你决定使用容量计费模型，你必须购买 Azure 堆栈容量计划 SKU 基于你的系统的容量。 你将需要知道你购买的正确数量的 Azure 堆栈中的物理内核数。 

容量计费需要企业协议 (EA) 注册的 Azure 订阅。 原因是注册设置需要 Azure 订阅的联合。 订阅不用于 Azure 堆栈使用。

## <a name="learn-more"></a>了解详细信息
- 有关用例、 购买、 合作伙伴和 OEM 硬件供应商的信息，请参阅[Azure 堆栈](https://azure.microsoft.com/overview/azure-stack/)产品页。
- 有关 Azure 堆栈信息的路线图和地域可用性集成的系统，请参阅白皮书： [Azure 堆栈： Azure 的扩展](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)。 
- 若要了解有关 Microsoft Azure 堆栈打包和定价的详细信息[下载.pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)。 

## <a name="next-steps"></a>后续步骤
[数据中心网络集成](azure-stack-network.md)