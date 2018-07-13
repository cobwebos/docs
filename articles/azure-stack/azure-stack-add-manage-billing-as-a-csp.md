---
title: 管理充当云服务提供商的 Azure Stack 的使用情况和计费 | Microsoft Docs
description: 逐步完成 Azure Stack 注册为云提供商 (CSP) 并添加客户进行计费。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: c4a266644bf5288e51e01523b6033082400387d4
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009482"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>管理充当云服务提供商的 Azure Stack 的使用情况和计费 

*适用于：Azure Stack 集成系统*

本文详细介绍如何将 Azure Stack 注册为云提供商 (CSP) 并添加客户。

作为 CSP 你可以与使用 Azure Stack 的各种客户。 每个客户在 Azure 中有一个 CSP 订阅。 你将需要将每个用户订阅从 Azure Stack 的使用情况。

下图显示了将需要选择共享的服务帐户和已使用 Azure Stack 帐户注册 Azure 帐户的步骤。 注册，即可载入最终客户。

**以 CSP 身份添加使用情况跟踪的步骤**

![以云服务提供商身份启用使用情况跟踪和管理功能的过程。](media\azure-stack-add-manage-billing-as-a-csp\process-add-useage-as-a-csp.png)

## <a name="create-a-csp-or-cspss-subscription"></a>创建 CSP 或 CSPSS 订阅

### <a name="cloud-service-provider-subscription-types"></a>云服务提供商订阅类型

需要选择共享服务帐户的类型，该帐户用于 Azure Stack。 可以用来注册多租户 Azure Stack 的订阅类型为：

 - 云服务提供商 
 - 合作伙伴共享服务订阅 

#### <a name="csp-shared-services"></a>CSP 共享服务

在直接 CSP 或 CSP 分销商运营 Azure Stack 的情况下，应首选云服务提供商共享服务 (CSPSS) 订阅进行注册。

CSPSS 订阅与共享服务租户相关联。 注册 Azure Stack 时，需提供帐户的凭据，该帐户是订阅的所有者。 用于注册 Azure Stack 的帐户可以不同于用于部署的管理员帐户。 此外，两个帐户执行操作*不*必须属于同一个域。 换言之，可以使用已经使用过的租户进行部署。 例如，可以使用 ContosoCSP.onmicrosoft.com 进行部署，然后使用另一租户（例如 IURContosoCSP.onmicrosoft.com）进行注册。 需要记住在一天。 为此 Azure Stack 管理时，使用 ContosoCSP.onmicrosoft.com 登录。 需要执行注册操作时，则使用 IURContosoCSP.onmicrosoft.com 登录到 Azure。

请参阅下文，以便获取 CSPSS 订阅的说明以及如何创建订阅的指导：[添加 Azure 合作伙伴共享服务](https://msdn.microsoft.com/partner-center/shared-services)。

#### <a name="csp-subscriptions"></a>CSP 订阅

在 CSP 经销商或最终客户运营 Azure Stack 的情况下，应首选云服务提供商 (CSP) 订阅进行注册。

## <a name="register-azure-stack"></a>注册 Azure Stack

若要注册 Azure Stack，请参阅[将 Azure Stack 注册到 Azure 订阅](azure-stack-registration.md)。

## <a name="add-end-customer"></a>添加最终客户

若要配置 Azure Stack，以便在新租户使用资源时，系统会将其使用情况报告给云服务提供商 (CSP) 订阅，请参阅[将租户添加到 Azure Stack 以获取使用情况和计费信息](azure-stack-csp-howto-register-tenants.md)。

## <a name="charge-the-right-subscriptions"></a>对正确的订阅收费

Azure Stack 使用名为“注册”的功能。 注册是在 Azure 中存储的对象。 注册对象记录了要用于对给定的 Azure Stack 收费的 Azure 订阅。 本部分讲述注册的重要性。

Azure Stack 可以通过注册执行以下操作：
 - 将 Azure Stack 使用情况数据转发到 Azure Commerce，并对 Azure 订阅计费。
 - 使用多租户 Azure Stack 部署时，在另一订阅上报告每个客户的使用情况。 使用多租户时，Azure Stack 可以在同一 Azure Stack 实例上支持不同的组织。

对于每个 Azure Stack，还有一个默认的订阅，许多租户订阅。 默认订阅。 如果没有特定于租户的订阅收费的 Azure 订阅 它必须是订阅注册到的第一个。 若要使用多租户使用情况报告，订阅必须是 CSP 或 CSPSS 订阅。

然后，对于每个将要使用 Azure Stack 的租户，使用 Azure 订阅对注册进行更新。 租户订阅必须是 CSP 类型，并且必须汇总到拥有默认订阅的合作伙伴处。 换言之，无法注册其他人的客户。

当 Azure Stack 将使用情况信息转发到全球版 Azure 时，Azure 中的一项服务会查阅该注册，并将每个租户的使用情况映射到相应的租户订阅。 如果租户尚未注册，则该使用情况会转发到租户源自的 Azure Stack 实例的默认订阅。

由于租户订阅是 CSP 订阅，其账单将发送至 CSP 合作伙伴，其使用情况信息对最终客户不可见。

## <a name="next-steps"></a>后续步骤

 - 若要详细了解 CSP 计划，请参阅[云解决方案提供商计划](https://partnercenter.microsoft.com/en-us/partner/programs)。
 - 若要详细了解如何从 Azure Stack 检索资源使用情况信息，请参阅 [Azure Stack 中的使用情况和计费](azure-stack-billing-and-chargeback.md)。
