---
title: Azure AD 域服务中的挂起域 | Microsoft Docs
description: 了解 Azure AD DS 托管域的各种运行状况，以及如何还原挂起的域。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 79b5f4492d05880e263f8d489a64ba0cc218d355
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86223391"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>了解 Azure Active Directory 域服务中的运行状况并解决挂起的域

当 Azure Active Directory 域服务 (Azure AD DS) 长时间无法为托管域提供服务时，它会将托管域置于挂起状态。 如果托管域仍处于挂起状态，则会自动删除该域。 若要使 Azure AD DS 托管域保持正常运行并避免挂起，请尽快解决任何警报。

本文解释了托管域挂起的原因，以及如何恢复挂起的域。

## <a name="overview-of-managed-domain-states"></a>托管域状态概述

在托管域的整个生命周期中，有不同的状态指示其运行状况。 如果托管域报告了问题，请快速解决根本原因，以防止状况继续恶化。

![托管域在挂起过程中的状态进展](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

托管域可能会处于下列状态之一：

* [正在运行](#running-state)
* [需要注意](#needs-attention-state)
* [已挂起](#suspended-state)
* [已删除](#deleted-state)

## <a name="running-state"></a>“正在运行”状态

已正确配置且无问题的托管域处于“正在运行”状态。 这是期望的托管域状态。

### <a name="what-to-expect"></a>期望

* Azure 平台可以定期监视托管域的运行状况。
* 托管域的域控制器会定期修补和更新。
* Azure Active Directory 中的更改会定期同步到托管域。
* 托管域会定期备份。

## <a name="needs-attention-state"></a>“需要注意”状态

有一个或多个问题需要修复的托管域处于“需要注意”状态。 托管域的运行状况页会列出警报，并指示出现问题的位置。

某些警报是暂时性的，会由 Azure 平台自动解决。 对于其他警报，你可以按照提供的解决方法步骤来解决问题。 如果有严重警报，请[提交 Azure 支持请求][azure-support]来获取更多的排除故障帮助。

存在受限制的网络安全组是一个警报示例。 在此配置中，Azure 平台可能无法更新和监视托管域。 将生成一个警报，并且状态会变为“需要注意”。

有关详细信息，请参阅[如何解决托管域的警报][resolve-alerts]。

### <a name="what-to-expect"></a>期望

当托管域处于“需要注意”状态时，Azure 平台可能无法定期监视、修补、更新或备份数据。 在某些情况下（例如当网络配置无效时），可能无法访问托管域的域控制器。

* 托管域处于不正常状态，并且可能会停止正在进行的运行状况监视，直到警报解除。
* 可能无法修补或更新托管域的域控制器。
* Azure Active Directory 中的更改可能无法同步到托管域。
* 可能不会创建托管域的备份。
* 如果你解决了影响托管域的非严重警报，则运行状况应当恢复为“正在运行”状态。
* 当存在导致 Azure 平台无法访问域控制器的配置问题时，会触发严重警报。 如果这些严重警报在 15 天内未得到解决，则托管域将进入“已挂起”状态。

## <a name="suspended-state"></a>“已挂起”状态

托管域会由于下列原因之一而进入“已挂起”状态：

* 一个或多个严重警报未在 15 天内得到解决。
    * 严重警报可能由阻止访问 Azure AD DS 所需资源的错误配置引起。 例如，托管域中的警报 [AADDS104: 网络错误][alert-nsg]超过 15 天未解决。
* Azure 订阅存在计费问题，或者 Azure 订阅已过期。

当 Azure 平台无法管理、监视、修补或备份托管域时，托管域会被挂起。 托管域停留在“已挂起”状态的时间为 15 天。 若要保持对托管域的访问，请立即解决严重警报。

### <a name="what-to-expect"></a>期望

当托管域处于“已挂起”状态时，将出现以下行为：

* 将取消预配托管域的域控制器，并且无法在虚拟网络中访问这些控制器。
* 通过 Internet 对托管域进行的安全 LDAP 访问（如已启用）停止工作。
* 向托管域进行身份验证、登录到已加入域的 VM 或通过 LDAP/LDAPS 进行连接时出现故障。
* 不再备份托管域。
* 停止与 Azure AD 同步。

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>如何知道托管域是否被挂起？

Azure 门户中的“Azure AD DS 运行状况”页上会显示一个[警报][resolve-alerts]，指明域已挂起。 域的状态也显示为“已挂起”。

### <a name="restore-a-suspended-domain"></a>还原挂起的域

若要还原处于“已挂起”状态的托管域的运行状况，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择“域服务”。
1. 从列表中选择你的托管域（例如 aaddscontoso.com），然后选择“运行状况”。
1. 根据挂起原因选择警报，例如 AADDS503 或 AADDS504。
1. 选择警报中提供的解决方法链接，按相关步骤来解决警报。

托管域只能还原到上次备份的日期。 上次备份的日期显示在托管域的“运行状况”页面上。 系统不会还原上次备份后发生的任何更改。 托管域的备份最多存储 30 天。 超过 30 天的备份将被删除。

在你解决了托管域处于“已挂起”状态时的警报后，请[提交 Azure 支持请求][azure-support]，以便将托管域恢复为正常运行状态。 如果有 30 天内的备份，Azure 支持可以还原托管域。

## <a name="deleted-state"></a>“已删除”状态

停留在“已挂起”状态达 15 天的托管域将被删除。 此过程不可恢复。

### <a name="what-to-expect"></a>期望

当托管域进入“已删除”状态时，将出现以下行为：

* 将删除托管域的所有资源和备份。
* 你不能还原托管域。 必须创建替换托管域才能重用 Azure AD DS。
* 删除后，无需为托管域付费。

## <a name="next-steps"></a>后续步骤

若要使托管域保持正常运行，并将其进入已挂起状态的风险降到最低，请了解如何[解决托管域的警报][resolve-alerts]。

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
