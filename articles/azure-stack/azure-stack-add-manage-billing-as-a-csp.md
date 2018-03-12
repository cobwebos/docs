---
title: "管理使用情况和计费 Azure 堆栈为云服务提供程序 |Microsoft 文档"
description: "为云提供程序注册 Azure 堆栈和添加客户演练。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 23e3a675e6a464c92d26df220c8126c970f590a0
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>管理使用情况和计费 Azure 堆栈为云服务提供程序 

*适用于：Azure Stack 集成系统*

本文将指导你完成注册 Azure 堆栈作为云提供程序 (CSP) 和添加客户。

作为 CSP，你很可能有许多不同的客户使用 Azure 堆栈。 每个客户拥有 CSP 订阅在 Azure 中，且你将需要直接从 Azure 堆栈的使用情况与每个用户的订阅。

下图显示将需要选择共享的服务帐户和已使用此帐户注册 azure 帐户的步骤。 当已完成此操作时，可以导入最终客户。

**添加用量跟踪与 CSP 的步骤**

![启用使用情况和作为云服务提供商的管理的过程。](media\azure-stack-add-manage-billing-as-a-csp\process-add-useage-as-a-csp.png)

## <a name="create-a-csp-or-cspss-subscription"></a>创建 CSP 或 CSPSS 订阅

### <a name="cloud-service-provider-subscription-types"></a>云服务提供商订阅类型

你将需要选择的用于 Azure 堆栈的共享的服务帐户的类型。 可以用于多租户 Azure 堆栈的注册的订阅的类型包括：

 - 云服务提供商 
 - 合作伙伴共享服务订阅 

#### <a name="csp-shared-services"></a>CSP 共享服务

云服务提供程序共享服务 (CSPSS) 订阅的首选注册时直接 CSP 或 CSP 分发服务器运行 Azure 堆栈。

CSPSS 订阅都与共享服务租户相关联。 注册 Azure 堆栈时，你需要是订阅的所有者的帐户提供凭据。 你用于注册 Azure 堆栈的帐户可以是不同于你用于部署; 客户端的管理员帐户这两个执行*不*必须属于同一个域。 换而言之，您可以部署使用已用的租户。 例如，你可能使用 ContosoCSP.onmicrosoft.com，然后使用不同的租户，例如 IURContosoCSP.onmicrosoft.com 注册。你将需要记住您登录使用 ContosoCSP.onmicrosoft.com 天执行 Azure 堆栈管理执行操作时。 当你登录到 Azure 中使用 IURContosoCSP.onmicrosoft.com 时需要执行注册操作。

请参阅以下有关 CSPSS 订阅和说明的说明如何创建订阅[添加 Azure 合作伙伴共享服务](https://msdn.microsoft.com/partner-center/shared-services)。

#### <a name="csp-subscriptions"></a>CSP 订阅

云服务提供程序 (CSP) 订阅注册时 CSP 分销商的首选或最终用户操作 Azure 堆栈。

## <a name="register-azure-stack"></a>注册 Azure 堆栈

若要注册 Azure 堆栈，请参阅[与你的 Azure 订阅注册 Azure 堆栈](azure-stack-registration.md)。

## <a name="add-end-customer"></a>添加结束客户

若要配置 Azure 堆栈，以便新租户使用的资源时将将其使用情况报告给其云服务提供程序 (CSP) 订阅，请参阅[添加租户使用情况和计费到 Azure 堆栈](azure-stack-csp-howto-register-tenants.md)。

## <a name="charge-the-right-subscriptions"></a>收费正确的订阅

Azure 堆栈使用一种称为注册功能。 注册是存储在 Azure 中，记录用于为给定的 Azure 堆栈收费的 Azure 订阅的对象。 本部分讲述注册的重要性。

使用注册 Azure 堆栈可以：
 - 将 Azure 堆栈使用情况数据转发给 Azure 商务和计费的 Azure 订阅。
 - 报告与多租户 Azure 堆栈部署不同的订阅的每个客户的使用情况。 多租户模式使 Azure 堆栈，以在相同的 Azure 堆栈实例上支持不同的组织。

对于每个 Azure 堆栈，没有一个默认订阅和许多租户订阅，根据需要。 默认订阅。 如果没有特定于租户的订阅收费的 Azure 订阅 它必须是要注册的第一个。 对于多租户使用情况报告来工作，订阅必须是 CSP 或 CSPSS 订阅。

然后，注册使用 Azure 订阅更新为要使用 Azure 堆栈的每个租户。 租户订阅的 CSP 类型必须为，并且必须汇总到拥有默认订阅的合作伙伴。 换而言之，你无法注册其他人的客户。

当 Azure 堆栈将使用情况信息转发到全球 Azure 时，Azure 中的服务向注册并映射到相应的租户订阅的每个租户的使用情况。 如果尚未注册租户，该使用情况将转到它源自的 Azure 堆栈实例的默认订阅。

由于租户订阅 CSP 订阅，其清单发送到 CSP 合作伙伴，并且使用情况信息不给最终用户可见。



## <a name="next-steps"></a>后续步骤

 - 若要了解有关 CSP 计划的更多信息，请参阅[云解决方案提供商计划](https://partnercenter.microsoft.com/en-us/partner/programs)。
 - 若要了解有关如何从 Azure 堆栈中检索资源使用情况信息的详细信息，请参阅[使用情况和 Azure 堆栈中的计费](/azure-stack-billing-and-chargeback.md)。
