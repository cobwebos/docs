---
title: 检查 Azure Active Directory 域服务的运行状况 |Microsoft Docs
description: 了解如何检查 Azure Active Directory 域服务（Azure AD DS）托管域的运行状况，以及如何使用 Azure 门户了解状态消息。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: iainfou
ms.openlocfilehash: 501214f87a65c71436e262608f7e9b3471cc9775
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705412"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>检查 Azure Active Directory 域服务托管域的运行状况

Azure Active Directory 域服务（Azure AD DS）运行一些后台任务，以使托管域保持健康和最新。 这些任务包括备份、应用安全更新和从 Azure AD 同步数据。 如果 Azure AD DS 托管域存在问题，则可能无法成功运行这些任务。 若要查看并解决任何问题，可以使用 Azure 门户检查 Azure AD DS 托管域的运行状况状态。

本文介绍如何查看 Azure AD DS 运行状况状态，并了解显示的信息或警报。

## <a name="view-the-health-status"></a>查看运行状况状态

使用 Azure 门户查看 Azure AD DS 托管域的运行状况状态。 可以查看有关上次备份时间和与 Azure AD 同步的信息，以及指示托管域的运行状况问题的任何警报。 若要查看 Azure AD DS 托管域的运行状况状态，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择**Azure AD 域服务**"。
1. 选择 Azure AD DS 托管域，如*aadds.contoso.com*。
1. 在 Azure AD DS 资源 "窗口的左侧，选择"**运行状况**"。 以下示例屏幕截图显示了一个运行状况良好 Azure AD DS 托管域，以及上次备份和 Azure AD 同步的状态：

    ![Azure 门户显示 Azure Active Directory 域服务状态的运行状况页面概述](./media/check-health/health-page.png)

上次检查 Azure AD DS 托管域的时间，运行状况页面*最后计算*的时间戳将会显示。 每小时计算一次 Azure AD DS 托管域的运行状况。 如果对 Azure AD DS 托管域进行任何更改，请等待下一个评估周期，以查看更新的运行状况状态。

右上方的状态指示 Azure AD DS 托管域的总体运行状况。 状态决定了域中所有现有警报。 下表详细说明了可用状态指示器：

| 状态 | 图标 | 说明 |
| --- | :----: | --- |
| 正在运行 | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | Azure AD DS 托管域正在正常运行，且没有任何严重或警告警报。 域可能包含信息性警报。 |
| 需要注意（警告） | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | Azure AD DS 托管域上没有严重警报，但应解决一个或多个警告警报。 |
| 需要注意（严重） | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | 必须解决 Azure AD DS 托管域上的一个或多个关键警报。 还可能会出现警告和/或信息性警报。 |
| 正在部署 | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | 正在部署 Azure AD DS 域。 |

## <a name="understand-monitors-and-alerts"></a>了解监视器和警报

Azure AD DS 托管域的运行状况状态显示两种类型的信息监视器和警报。 监视器显示核心后台任务完成的时间。 警报提供了信息或建议来提高托管域的稳定性。

### <a name="monitors"></a>监视器

监视器是定期检查 Azure AD DS 托管域的区域。 如果 Azure AD DS 托管域有任何活动警报，则可能会导致其中一个监视器报告问题。 目前 Azure AD 域服务监视以下方面：

* 备份
* 与 Azure AD 同步

#### <a name="backup-monitor"></a>备份监视器

备份监视器检查 Azure AD DS 托管域的自动定期备份是否成功运行。 下表详细介绍了可用的备份监视器状态：

| 详细信息值 | 说明 |
| --- | --- |
| 从未备份 | 对于新 Azure AD DS 托管域，此状态正常。 应在部署 Azure AD DS 托管域后的24小时内创建第一次备份。 如果此状态仍然存在，请[打开 Azure 支持请求][azure-support]。 |
| 最后一个备份是在 1 到 14 天前创建的 | 此时间范围是备份监视器的预期状态。 自动定期备份应在此期间进行。 |
| 最后一个备份是在 14 天前创建的。 | 时间跨度超过两周表示自动定期备份出现问题。 活动严重警报可能会阻止备份 Azure AD DS 托管域。 解决 Azure AD DS 托管域的任何活动警报。 如果备份监视器未随后更新状态以报告最近的备份，请[打开 Azure 支持请求][azure-support]。 |

#### <a name="synchronization-with-azure-ad-monitor"></a>与 Azure AD 监视器同步

Azure AD DS 托管域会定期与 Azure Active Directory 同步。 用户和组对象的数量，以及自上次同步后在 Azure AD 目录中所做的更改数会影响同步所用的时间。 如果 Azure AD DS 托管域在三天前同步，请检查并解决所有活动警报。 如果同步监视器未随后更新状态以显示最近的同步，请[打开 Azure 支持请求][azure-support]。

### <a name="alerts"></a>警报

对于需要解决以使服务正确运行的 Azure AD DS 托管域中的问题，会生成警报。 每个警报都说明了该问题，并提供了一个 URL，其中概述了解决此问题的特定步骤。 有关可能的警报及其解决方法的详细信息，请参阅[解决警报问题](troubleshoot-alerts.md)。

运行状况状态警报分为以下严重性级别：

 * **严重警报**是严重影响 Azure AD DS 托管域的问题。 应该立即解决这些警报。 在解决问题之前，Azure 平台无法监视、管理、修补和同步托管域。
 * 如果问题仍然存在，**警告警报**将通知你可能会影响 Azure AD DS 托管域操作的问题。 这些警报还提供保护托管域的建议。
 * **信息警报**是不会对 Azure AD DS 托管域产生负面影响的通知。 信息警报可提供有关托管域中发生的情况的一些见解。

## <a name="next-steps"></a>后续步骤

有关 "运行状况状态" 页中显示的警报的详细信息，请参阅在[托管域上解决警报][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
