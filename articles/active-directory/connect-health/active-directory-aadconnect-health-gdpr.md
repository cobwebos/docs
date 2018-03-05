---
title: "Azure AD Connect Health 和一般数据保护条例 | Microsoft Docs"
description: "本文介绍如何保证 Azure AD Connect 符合 GDPR 的规定。"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/18/2018
ms.author: billmath
ms.openlocfilehash: d66f717f546271a5e5c3c49d6cbaef1c190d18d8
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="gdpr-compliance-and-azure-ad-connect-health"></a>GDPR 符合性和 Azure AD Connect Health 

[一般数据保护条例 (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm) 是欧盟 (EU) 的一项数据保护和隐私法律。 GDPR 针对向欧盟居民提供产品及服务，或收集和分析欧盟居民相关信息的公司、政府机构、非营利组织和其他组织制定了新的规则。 

Microsoft 产品和服务可立即用于帮助你满足 GDPR 要求。 请访问[信任中心](https://www.microsoft.com/trustcenter)，深入了解 Microsoft 隐私策略。

Azure AD Connect Healt 可监视本地标识基础结构和同步服务。 它还提供见解并显示警报。 自 GDPR 于 2018 年 5 月开始实施以来，Microsoft 始终致力于保证云服务符合 GDPR，同时在合同承诺中提供 GDPR 相关保证。 

>[!NOTE] 
> 本文概述了 Azure AD Connect Health 中的 GDPR 符合性情况。 若要了解 Azure AD Connect 中的 GDPR 符合性情况，请参阅 [GDPR 符合性和 Azure AD Connect](../../active-directory/connect/active-directory-aadconnect-gdpr.md)。

## <a name="gdpr-classification"></a>GDPR 分类
Azure AD Connect Health 属于 GDPR 的数据处理器类别。 该服务作为数据处理器管道，向关键合作伙伴和最终客户提供数据处理服务。 Azure AD Connect Health 不生成用户数据，且不独立控制要收集的个人数据类型及其使用方式。 Azure AD Connect Health 基于现有本地数据进行数据检索、聚合、分析和报告。 

## <a name="data-retention-policy"></a>数据保留策略
Azure AD Connect Health 不生成报告、不进行分析，也不提供 30 天之前的见解。 因此，Azure AD Connect Health 不存储、处理或保留任何 30 天以前的数据。 此设计符合 GDPR 法规要求、Microsoft 隐私符合性规定和 Azure AD 数据保留策略。 

连续 30 天以上显示活动的“运行状况服务数据不是最新”**错误**警报的服务器表明在该时间范围内没有数据到达 Connect Health。 这些服务器将被禁用，并且不显示在 Connect Health 门户中。 若要重新启用这些服务器，必须卸载并[重新安装运行状况代理](active-directory-aadconnect-health-agent-install.md)。 请注意，这不适用于具有相同警报类型的**警告**。 警告指示向你发出警报的服务器中缺少部分数据。 
 
## <a name="disable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>在 Azure AD Connect Health 中禁用数据收集和监视
借助 Azure AD Connect Health，可针对所监视的单独服务器或所监视服务的实例停止数据收集功能。 例如，可针对使用 Azure AD Connect Health 进行监视的单独 ADFS（Active Directory 联合身份验证服务）服务器停止数据收集功能。 还可针对当前通过 Azure AD Connect Health 监视的整个 ADFS 实例停止数据收集功能。 选择停止此功能后，会在停止数据收集后从 Azure AD Connect Health 门户删除相应的服务器。 

>[!IMPORTANT]
> 需在 RBAC 具备 Azure AD 全局管理员特权或参与者角色，才能从 Azure AD Connect Health 中删除所监视的服务器。
>
> 从 Azure AD Connect Health 删除服务器或服务实例后，操作不可撤消。 

### <a name="what-to-expect"></a>要发生的情况
如果针对单个监视的服务器或所监视服务的实例停止数据收集和监视功能，请注意以下情况：

- 删除所监视服务的实例后，会从门户的 Azure AD Connect Health 监视服务列表中删除此实例。 
- 删除所监视的服务器或所监视服务的实例后，不会从服务器中卸载或删除运行状况代理。 运行状况代理已配置为不发送数据至 Azure AD Connect Health。 需要在之前监视的服务器上手动卸载运行状况代理。
- 如果在执行此步骤之前未卸载 Health 代理，则可能会在与 Health 代理相关的服务器上看到错误事件。
- 按照 Microsoft Azure 数据保留策略删除所监视服务的实例中的所有数据。

### <a name="disable-data-collection-and-monitoring-for-a-monitored-server"></a>对所监视服务器禁用数据收集和监视
请参阅[如何从 Azure AD Connect Health 中删除服务器](active-directory-aadconnect-health-operations.md#delete-a-server-from-the-azure-ad-connect-health-service)。

### <a name="disable-data-collection-and-monitoring-for-an-instance-of-a-monitored-service"></a>对所监视服务的实例禁用数据收集和监视
请参阅[如何删除 Azure AD Connect Health 中的服务实例](active-directory-aadconnect-health-operations.md#delete-a-service-instance-from-azure-ad-connect-health-service)。


## <a name="re-enable-data-collection-and-monitoring-in-azure-ad-connect-health"></a>重新在 Azure AD Connect Health 中启用数据收集和监视
对于之前已删除的被监视服务，若要在 Azure AD Connect Health 中重新启用监视，必须在所有服务器上卸载再[重新安装运行状况代理r](active-directory-aadconnect-health-agent-install.md)。


## <a name="next-steps"></a>后续步骤
* [查看信任中心上的 Microsoft 隐私策略](https://www.microsoft.com/trustcenter)
* [Azure AD Connect 和 GDPR](../../active-directory/connect/active-directory-aadconnect-gdpr.md)
* [Azure AD Connect Health 操作](active-directory-aadconnect-health-operations.md)
