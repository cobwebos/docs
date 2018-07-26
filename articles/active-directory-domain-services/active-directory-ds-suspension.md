---
title: Azure Active Directory 域服务：挂起的域 | Microsoft Docs
description: 托管域的挂起和删除
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2018
ms.author: ergreenl
ms.openlocfilehash: 99896b0f618151cd368a21c4a212b7d8e28b7a6f
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126087"
---
# <a name="suspended-domains"></a>挂起的域
当 Azure AD 域服务长时间无法为托管域提供服务时，托管域将处于挂起状态。 本文解释了托管域挂起的原因，以及如何修正挂起的域。


## <a name="states-your-managed-domain-can-be-in"></a>托管域可能处于的状态

![挂起的域的时间线](media\active-directory-domain-services-suspension\suspension-timeline.PNG)

上图概述了 Azure AD 域服务托管域可能处于的状态。

### <a name="running-state"></a>“正在运行”状态
正确配置并定期运行的托管域处于“正在运行”状态。

**可以预期的结果：**
* Microsoft 可以定期监视托管域的运行状况。
* 托管域的域控制器会定期修补和更新。
* Azure Active Directory 中的更改会定期同步到托管域。
* 定期备份托管域。


### <a name="needs-attention-state"></a>“需引起注意”状态
如果有一个或多个问题需要管理员采取措施，则托管域处于“需引起注意”状态。 托管域的运行状况页面将列出此状态下的一个或多个警报。 例如，如果你为虚拟网络配置了限制性 NSG，则 Microsoft 可能无法更新和监视托管域。 此无效配置会导致生成警报，并且托管域处于“需引起注意”状态。

每个警报都有一组解决步骤。 某些警报是暂时的，将由服务自动解决。 对于其他警报，你可以按照该警报的相应解决步骤中的说明予以解决。 若要解决某些严重警报，需要与 Microsoft 支持部门联系。

有关详细信息，请参阅[如何解决托管域上的警报](active-directory-ds-troubleshoot-alerts.md)。

**可以预期的结果：**

在某些情况下（例如，如果网络配置无效），可能无法访问托管域的域控制器。 Microsoft 无法保证在此状态下定期监视、修补、更新或备份托管域。

* 托管域处于不正常状态，并且可能会停止正在进行的运行状况监视，直到警报解除。
* 可能无法修补或更新托管域的域控制器。
* Azure Active Directory 中的更改可能无法同步到托管域。
* 如果可能，可能会备份托管域。
* 如果解决了影响托管域的警报，则有可能将托管域还原为“正在运行”状态。
* 对于 Microsoft 无法访问域控制器的配置问题，系统会触发严重警报。 如果此类警报未在 15 天内得到解决，则托管域将处于“挂起”状态。


### <a name="suspended-state"></a>“挂起”状态
由于以下原因，托管域处于“挂起”状态：
* 一个或多个严重警报未在 15 天内得到解决。 严重警报可能由阻止访问 Azure AD 域服务所需资源的错误配置引起。
    * 例如，如果托管域的警报 [AADDS104：网络错误](active-directory-ds-troubleshoot-nsg.md)超过 15 天未解决。
* Azure 订阅存在计费问题，或者 Azure 订阅已过期。

当 Microsoft 无法持续管理、监视、修补或备份托管域时，托管域将被挂起。

**可以预期的结果：**
* 将取消预配托管域的域控制器，并且无法在虚拟网络中访问这些控制器。
* 通过 Internet 对托管域进行的安全 LDAP 访问（如已启用）停止工作。
* 你会发现，对托管域进行身份验证、登录到已加入域的虚拟机以及通过 LDAP/LDAPS 进行连接时出现故障。
* 不再备份托管域。
* 停止与 Azure AD 同步。
* 解决导致托管域处于“挂起”状态的警报，然后联系支持部门。
* 仅当存在时间短于 30 天的备份时，支持部门才有可能还原托管域。

托管域处于挂起状态的时间只有 15 天。 若要恢复托管域，Microsoft 建议立即解决严重警报。


### <a name="deleted-state"></a>“已删除”状态
处于“挂起”状态 15 天的托管域将**被删除**。

**可以预期的结果：**
* 将删除托管域的所有资源和备份。
* 无法还原托管域，需要创建新的托管域才能使用 Azure AD 域服务。
* 删除后，无需为托管域付费。


## <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>如何知道托管域是否被挂起？
Azure 门户中的“Azure AD 域服务运行状况”页面上会显示一个[警报](active-directory-ds-troubleshoot-alerts.md)，声明域已挂起。 域的状态也显示“挂起”。


## <a name="restore-a-suspended-domain"></a>还原挂起的域
若要还原处于“挂起”状态的域，请完成以下步骤：

1. 导航到 Azure 门户上的 [Azure AD 域服务页面](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)
2. 单击该托管域。
3. 在左侧导航栏中，单击“运行状况”。
4. 单击警报。 警报 ID 将为 AADDS503 或 AADDS504，具体取决于挂起的原因。
5. 单击警报中提供的解决方案链接，按相关步骤解决警报。

托管域只能还原到上次备份的日期。 上次备份的日期显示在托管域的“运行状况”页面上。 系统不会还原上次备份后发生的任何更改。 托管域的备份最多存储 30 天。 超过 30 天的备份将被删除。


## <a name="next-steps"></a>后续步骤
- [解决托管域中的警报](active-directory-ds-troubleshoot-alerts.md)
- [详细了解 Azure AD 域服务](active-directory-ds-overview.md)
- [联系产品团队](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>联系我们
欢迎联系 Azure Active Directory 域服务产品团队[分享看法或请求支持](active-directory-ds-contact-us.md)。
