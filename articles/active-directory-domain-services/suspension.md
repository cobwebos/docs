---
title: Azure AD 域服务中的挂起域 |微软文档
description: 了解 Azure AD DS 托管域的不同运行状况状态以及如何还原挂起的域。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 0b6e28fc92361fe20fc34e92386bf17199b19617
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519023"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>了解运行状况状态并解析 Azure 活动目录域服务中的挂起域

当 Azure Active Directory 域服务 (Azure AD DS) 长时间无法为托管域提供服务时，它会将托管域置于挂起状态。 如果托管域保持挂起状态，则会自动删除它。 要保持 Azure AD DS 托管域正常运行并避免挂起，请尽可能快速解决任何警报。

本文解释托管域挂起的原因以及如何恢复挂起的域。

## <a name="overview-of-managed-domain-states"></a>托管域状态概述

在 Azure AD DS 托管域的生命周期中，有不同状态指示其运行状况。 如果托管域报告问题，请快速解决根本原因以阻止状态继续降级。

![Azure AD DS 托管域需要挂起状态的进度](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Azure AD DS 托管域可以处于以下状态之一：

* [正在运行](#running-state)
* [需要注意](#needs-attention-state)
* [Suspended](#suspended-state)
* [删除](#deleted-state)

## <a name="running-state"></a>运行状态

正确配置且运行且没有问题的 Azure AD DS 托管域处于 *"正在运行"* 状态。 这是托管域所需的状态。

### <a name="what-to-expect"></a>期望

* Azure 平台可以定期监视托管域的运行状况。
* 托管域的域控制器会定期进行修补和更新。
* Azure 活动目录的更改定期同步到托管域。
* 对托管域进行定期备份。

## <a name="needs-attention-state"></a>需要注意状态

具有一个或多个需要修复问题的 Azure AD DS 托管域处于 *"需要注意"* 状态。 托管域的运行状况页列出警报，并指示哪里有问题。 某些警报是暂时性的，由 Azure 平台自动解析。 对于其他警报，您可以按照提供的解决方案步骤来解决此问题。 它有一个严重警报，打开[Azure 支持请求][azure-support]以获取其他故障排除帮助。

警报的一个示例是，当有一个限制性的网络安全组。 在此配置中，Azure 平台可能无法更新和监视托管域。 将生成警报，状态更改为 *"需要注意*"。

有关详细信息，请参阅[如何对 Azure AD DS 托管域的警报进行故障排除][resolve-alerts]。

### <a name="what-to-expect"></a>期望

当 Azure AD DS 托管域处于 *"需要注意"* 状态时，Azure 平台可能无法定期监视、修补、更新或备份数据。 在某些情况下，例如网络配置无效，托管域的域控制器可能无法到达。

* 托管域处于不正常状态，在警报解决之前，可能会停止正在进行的运行状况监视。
* 无法修补或更新托管域的域控制器。
* Azure 活动目录的更改可能不会同步到托管域。
* 可能无法执行托管域的备份。
* 如果解决影响托管域的非关键警报，运行状况应返回到 *"正在运行"* 状态。
* 对于 Azure 平台无法到达域控制器的配置问题，将触发严重警报。 如果这些关键警报在 15 天内未解决，则托管域将进入*挂起*状态。

## <a name="suspended-state"></a>挂起状态

Azure AD DS 托管域进入**挂起**状态的原因如下：

* 一个或多个严重警报未在 15 天内得到解决。
    * 严重警报可能由阻止访问 Azure AD DS 所需资源的错误配置引起。 例如，如果托管域中的警报 [AADDS104: 网络错误][alert-nsg]超过 15 天未解决。
* Azure 订阅存在计费问题，或者 Azure 订阅已过期。

当 Azure 平台无法管理、监视、修补或备份域时，托管域将挂起。 托管域处于*挂起*状态 15 天。 要维护对托管域的访问，立即解决关键警报。

### <a name="what-to-expect"></a>期望

当 Azure AD DS 托管域处于*挂起*状态时，将经历以下行为：

* 托管域的域控制器已取消预配，并且无法在虚拟网络中进行。
* 安全 LDAP 通过互联网对托管域的访问（如果启用）将停止工作。
* 身份验证到托管域、登录到域加入的 VM 或通过 LDAP/LDAPS 进行连接时存在失败。
* 不再执行托管域的备份。
* 停止与 Azure AD 同步。

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>如何知道托管域是否被挂起？

在 Azure 门户中，Azure AD DS 运行状况页上会显示[一条警报][resolve-alerts]，指出域已挂起。 域的状态还显示*挂起*。

### <a name="restore-a-suspended-domain"></a>还原挂起的域

要还原处于*挂起*状态的 Azure AD DS 托管域的运行状况，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择**域服务**。
1. 从列表中选择 Azure AD DS 托管域，如*aaddscontoso.com*，然后选择 **"运行状况**"。
1. 根据挂起的原因选择警报，如*AADDS503*或*AADDS504。*
1. 选择警报中提供的解决方法链接，然后按照步骤进行解析。

只能将托管域还原到上次备份的日期。 上次备份的日期显示在托管域的 **"运行状况**"页上。 系统不会还原上次备份后发生的任何更改。 托管域的备份最多存储 30 天。 超过 30 天的备份将被删除。

在托管域处于*挂起*状态时解决警报后，打开 Azure[支持请求][azure-support]以返回到正常状态。 如果备份少于 30 天，Azure 支持可以还原托管域。

## <a name="deleted-state"></a>已删除状态

如果 Azure AD DS 托管域处于*挂起*状态 15 天，则将其删除。 此过程不可恢复。

### <a name="what-to-expect"></a>期望

当 Azure AD DS 托管域进入 *"已删除"* 状态时，可以看到以下行为：

* 将删除托管域的所有资源和备份。
* 无法还原托管域，需要创建替换托管域以重用 Azure AD DS。
* 删除后，无需为托管域付费。

## <a name="next-steps"></a>后续步骤

要保持 Azure AD DS 托管域的健康并尽量减少其挂起的风险，请了解如何[解决托管域的警报][resolve-alerts]。

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
