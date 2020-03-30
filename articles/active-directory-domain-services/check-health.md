---
title: 检查 Azure 活动目录域服务的运行状况 |微软文档
description: 了解如何检查 Azure 活动目录域服务 （Azure AD DS） 托管域的运行状况，以及使用 Azure 门户了解状态消息。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: fa1cba2d791cd40a46f8ad182c123a726143faec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614249"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>检查 Azure 活动目录域服务托管域的运行状况

Azure 活动目录域服务 （Azure AD DS） 运行一些后台任务，以保持托管域的正常运行和最新。 这些任务包括执行备份、应用安全更新和同步 Azure AD 中的数据。 如果 Azure AD DS 托管域出现问题，则这些任务可能无法成功完成。 要查看和解决任何问题，可以使用 Azure 门户检查 Azure AD DS 托管域的运行状况状态。

本文介绍如何查看 Azure AD DS 运行状况状态并了解显示的信息或警报。

## <a name="view-the-health-status"></a>查看运行状况

使用 Azure 门户查看 Azure AD DS 托管域的运行状况状态。 可以看到有关上次备份时间和与 Azure AD 同步的信息，以及指示托管域运行状况有问题的任何警报。 要查看 Azure AD DS 托管域的运行状况状态，完成以下步骤：

1. 在 Azure 门户中，搜索并选择**Azure AD 域服务**。
1. 选择 Azure AD DS 托管域，如*aaddscontoso.com*。
1. 在 Azure AD DS 资源窗口的左侧，选择 **"运行状况**"。 以下示例屏幕截图显示了一个健康的 Azure AD DS 托管域以及上次备份和 Azure AD 同步的状态：

    ![Azure 门户中的运行状况页概述，显示 Azure 活动目录域服务状态](./media/check-health/health-page.png)

运行状况页*的最后一个计算*时间戳显示上次检查 Azure AD DS 托管域的时间。 Azure AD DS 托管域的运行状况每小时评估一次。 如果对 Azure AD DS 托管域进行任何更改，请等到下一个评估周期查看更新的运行状况。

右上角的状态指示 Azure AD DS 托管域的总体运行状况。 状态因素会影响域上的所有现有警报。 下表详细介绍了可用状态指示器：

| 状态 | 图标 | 说明 |
| --- | :----: | --- |
| 正在运行 | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Azure AD DS 托管域运行正常，没有任何严重或警告警报。 域可能具有信息警报。 |
| 需要注意（警告） | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Azure AD DS 托管域上没有严重警报，但应解决一个或多个警告警报。 |
| 需要注意（关键） | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | Azure AD DS 托管域上存在一个或多个关键警报，必须加以解决。 您可能还有警告和/或信息警报。 |
| 正在部署 | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | 正在部署 Azure AD DS 域。 |

## <a name="understand-monitors-and-alerts"></a>了解监视器和警报

Azure AD DS 托管域的运行状况显示两种类型的信息 -*监视器*和*警报*。 监视器显示核心后台任务完成的时间。 警报提供信息或建议以提高托管域的稳定性。

### <a name="monitors"></a>监视器

监视器是定期检查的 Azure AD DS 托管域的区域。 如果 Azure AD DS 托管域有任何活动警报，则可能导致其中一个监视器报告问题。 Azure AD 域服务当前具有以下区域的监视器：

* 备份
* 与 Azure AD 同步

#### <a name="backup-monitor"></a>备份监视器

备份监视器检查 Azure AD DS 托管域的自动定期备份成功运行。 下表详细介绍了可用的备份监视器状态：

| 详细信息值 | 说明 |
| --- | --- |
| 从不备份 | 对于新的 Azure AD DS 托管域，此状态是正常的。 第一个备份应在部署 Azure AD DS 托管域 24 小时后创建。 如果此状态仍然存在，[请打开 Azure 支持请求][azure-support]。 |
| 最后一个备份是在 1 到 14 天前创建的 | 此时间范围是备份监视器的预期状态。 在此期间应进行自动定期备份。 |
| 最后一个备份是在 14 天前创建的。 | 时间跨度超过两周表示自动定期备份出现问题。 活动严重警报可能会阻止备份 Azure AD DS 托管域。 解决 Azure AD DS 托管域的任何活动警报。 如果备份监视器未更新状态以报告最近的备份，[请打开 Azure 支持请求][azure-support]。 |

#### <a name="synchronization-with-azure-ad-monitor"></a>与 Azure AD 监视器同步

Azure AD DS 托管域定期与 Azure 活动目录同步。 用户和组对象数以及自上次同步以来 Azure AD 目录中所做的更改数会影响同步所需的时间。 如果 Azure AD DS 托管域上次同步超过三天前，请检查并解决任何活动警报。 如果同步监视器未更新状态以在您处理任何活动警报后显示最近的同步，[请打开 Azure 支持请求][azure-support]。

### <a name="alerts"></a>警报

针对 Azure AD DS 托管域中需要解决的问题生成警报，这些问题需要解决，以便服务正确运行。 每个警报都解释了问题，并给出了一个 URL，其中概述了解决问题的具体步骤。 有关可能的警报及其解决方法的详细信息，请参阅[故障排除警报](troubleshoot-alerts.md)。

运行状况警报分为以下严重级别：

 * **严重警报**是严重影响 Azure AD DS 托管域的问题。 应立即处理这些警报。 在问题解决之前，Azure 平台无法监视、管理、修补和同步托管域。
 * 如果问题仍然存在，**警告警报**会通知您可能影响 Azure AD DS 托管域操作的问题。 这些警报还提供保护托管域的建议。
 * **信息警报**是不会对 Azure AD DS 托管域产生负面影响的通知。 信息警报提供了对托管域中发生的情况的一些见解。

## <a name="next-steps"></a>后续步骤

有关运行状况页中显示的警报的详细信息，请参阅[解决托管域上的警报][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
