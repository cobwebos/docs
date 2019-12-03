---
title: Azure AD 域服务中的挂起域 |Microsoft Docs
description: 了解 Azure AD DS 托管域的不同运行状况状态，以及如何还原挂起的域。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iainfou
ms.openlocfilehash: 106cd870a8ba52917ecabe6266bbb2c376296d79
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74704274"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>了解运行状况状态和解决 Azure Active Directory 域服务中挂起的域

当 Azure Active Directory 域服务 (Azure AD DS) 长时间无法为托管域提供服务时，它会将托管域置于挂起状态。 如果托管域仍处于挂起状态，它将被自动删除。 若要保持 Azure AD DS 托管域正常运行并避免挂起，请尽快解决任何警报。

本文介绍挂起托管域的原因，以及如何恢复挂起的域。

## <a name="overview-of-managed-domain-states"></a>托管域状态概述

通过 Azure AD DS 托管域的生命周期，有不同的状态指示其运行状况。 如果托管域报告了问题，请快速解决导致停止状态的根本原因。

![Azure AD DS 托管域的状态进展](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Azure AD DS 托管域可处于以下状态之一：

* [正在运行](#running-state)
* [需要注意](#needs-attention-state)
* [状态](#suspended-state)
* [已删除](#deleted-state)

## <a name="running-state"></a>运行状态

配置正确且运行不正常的 Azure AD DS 托管域处于*运行*状态。 这是托管域的所需状态。

### <a name="what-to-expect"></a>你的期望

* Azure 平台会定期监视托管域的运行状况。
* 托管域的域控制器会定期进行修补和更新。
* Azure Active Directory 的更改会定期同步到托管域。
* 为托管域执行常规备份。

## <a name="needs-attention-state"></a>需要注意状态

具有一个或多个需要解决的问题的 Azure AD DS 托管域位于 "*需要注意*" 状态。 托管域的 "运行状况" 页列出了警报，并指示出现问题的位置。 某些警报是暂时性的，由 Azure 平台自动解决。 对于其他警报，你可以按照提供的解决方法步骤来解决此问题。 这是一个严重警报，请[打开 Azure 支持请求][azure-support]，以获取额外的故障排除帮助。

警报的一个示例就是有一个限制的网络安全组。 在此配置中，Azure 平台可能无法更新和监视托管域。 生成警报后，状态会更改为 "*需要注意*"。

有关详细信息，请参阅[如何对 AZURE AD DS 托管域的警报进行故障排除][resolve-alerts]。

### <a name="what-to-expect"></a>你的期望

当 Azure AD DS 托管域处于*需要注意*状态时，Azure 平台可能不会定期监视、修补、更新或备份数据。 在某些情况下（如使用无效网络配置），可能无法访问托管域的域控制器。

* 托管域处于不正常状态，并且在警报解决之前，正在进行的运行状况监视可能会停止。
* 不能修补或更新托管域的域控制器。
* Azure Active Directory 中的更改可能不会同步到托管域。
* 可能不会创建托管域的备份。
* 如果您解决了影响托管域的非关键警报，则运行状况应返回到 "*正在运行*" 状态。
* 对于 Azure 平台无法访问域控制器的配置问题，将触发关键警报。 如果在15天内未解决这些关键警报，托管域将进入 "*挂起*" 状态。

## <a name="suspended-state"></a>挂起状态

由于以下原因之一，Azure AD DS 托管域进入**挂起**状态：

* 一个或多个严重警报未在 15 天内得到解决。
    * 严重警报可能由阻止访问 Azure AD DS 所需资源的错误配置引起。 例如，如果托管域中的警报 [AADDS104: 网络错误][alert-nsg]超过 15 天未解决。
* Azure 订阅存在计费问题，或 Azure 订阅已过期。

当 Azure 平台无法管理、监视、修补或备份域时，托管域将挂起。 托管域处于*挂起*状态15天。 若要保持对托管域的访问，请立即解决关键警报。

### <a name="what-to-expect"></a>你的期望

当 Azure AD DS 托管域处于*挂起*状态时，将出现以下行为：

* 托管域的域控制器已取消预配，无法在虚拟网络中访问。
* 安全 LDAP 通过 internet 访问托管域（如果已启用），将停止工作。
* 对托管域进行身份验证时出现故障，登录到已加入域的 Vm，或者通过 LDAP/LDAPS 连接。
* 不再执行托管域的备份。
* 停止与 Azure AD 同步。

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>如何知道托管域是否被挂起？

你将在 "Azure AD DS 运行状况" 页上看到一个[警报][resolve-alerts]，指出域已挂起的 Azure 门户。 域的状态还显示为 "已*挂起*"。

### <a name="restore-a-suspended-domain"></a>还原挂起的域

若要还原处于*挂起*状态 Azure AD DS 托管域的运行状况，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择 "**域服务**"。
1. 从列表中选择 Azure AD DS 托管域（如*aadds.contoso.com*），然后选择 "**运行状况**"。
1. 选择警报，如*AADDS503*或*AADDS504*，具体取决于挂起的原因。
1. 选择警报中提供的解决方法链接，然后按照步骤进行解决。

托管域只能还原到上次备份的日期。 上次备份的日期显示在托管域的 "**运行状况**" 页上。 系统不会还原上次备份后发生的任何更改。 托管域的备份最多存储 30 天。 超过 30 天的备份将被删除。

当你在托管域处于*挂起*状态时解决了警报之后，请[打开 Azure 支持请求][azure-support]以恢复到正常状态。 如果备份不到30天，Azure 支持人员可以还原托管域。

## <a name="deleted-state"></a>删除状态

如果 Azure AD DS 托管域在15天内保持*挂起*状态，则会将其删除。 此进程不可恢复。

### <a name="what-to-expect"></a>你的期望

当 Azure AD DS 托管域进入*已删除*状态时，将出现以下行为：

* 将删除托管域的所有资源和备份。
* 不能还原托管域，而需要创建替换托管域以重用 Azure AD DS。
* 删除后，无需为托管域付费。

## <a name="next-steps"></a>后续步骤

若要保持 Azure AD DS 托管域的运行状况，并将其挂起的风险降到最低，请了解如何[解决托管域的警报][resolve-alerts]。

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
