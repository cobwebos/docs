---
title: 检查 Azure Active Directory 域服务的运行状况 | Microsoft 文档
description: 了解如何检查 Azure Active Directory 域服务 (Azure AD DS) 托管域的运行状况，以及如何使用 Azure 门户了解状态消息。
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: bf65657ea60e94f167bfbb6585026702b64e9658
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962728"
---
# <a name="check-the-health-of-an-azure-active-directory-domain-services-managed-domain"></a>检查 Azure Active Directory 域服务托管域的运行状况

Azure Active Directory 域服务 (Azure AD DS) 运行一些后台任务，以使托管域保持正常和最新。 这些任务包括备份、应用安全更新和从 Azure AD 同步数据。 如果 Azure AD DS 托管域存在问题，则可能无法成功完成这些任务。 若要查看并解决任何问题，可以使用 Azure 门户检查托管域的运行状态。

本文介绍如何查看 Azure AD DS 运行状态，以及如何理解显示的信息或警报。

## <a name="view-the-health-status"></a>查看运行状态

使用 Azure 门户查看托管域的运行状态。 可以查看有关上次备份时间和 Azure AD 同步的信息，以及指示托管域的运行状况问题的任何警报。 若要查看托管域的运行状态，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择“Azure AD 域服务”。
1. 选择你的托管域，例如“aaddscontoso.com”。
1. 在“Azure AD DS 资源”窗口的左侧，选择“运行状况”。 以下示例屏幕截图显示了一个正常的托管域以及上次备份和 Azure AD 同步的状态：

    ![Azure 门户中的运行状况页面概述，显示了 Azure Active Directory 域服务状态](./media/check-health/health-page.png)

运行状况页面的“上次评估”时间戳显示上次检查托管域的时间。 托管域的运行状况每隔一小时评估一次。 如果对托管域进行了任何更改，需等到下一评估周期才能看到更新的运行状态。

右上方的状态指示托管域的总体运行状况。 该状态考虑到了域中所有现有警报。 下表详细说明了可用的状态指示器：

| 状态 | 图标 | 说明 |
| --- | :----: | --- |
| 正在运行 | <img src= "./media/active-directory-domain-services-alerts/running-icon.png" width = "15" alt="Green check mark for running"> | 托管域正在正常运行，没有任何严重或警告警报。 此域可能有信息性警报。 |
| 需要注意（警告） | <img src= "./media/active-directory-domain-services-alerts/warning-icon.png" width = "15" alt="Yellow exclamation mark for warning"> | 托管域中未发生严重警报，但有一个或多个警告警报需要解决。 |
| 需要注意（严重） | <img src= "./media/active-directory-domain-services-alerts/critical-icon.png" width = "15" alt="Red exclamation mark for critical"> | 必须解决托管域上的一个或多个关键警报。 此外，还可能会收到警告和/或信息性警报。 |
| 正在部署 | <img src= "./media/active-directory-domain-services-alerts/deploying-icon.png" width = "15" alt="Blue circular arrows for deploying"> | 正在部署托管域。 |

## <a name="understand-monitors-and-alerts"></a>了解监视器和警报

托管域的运行状态显示两种类型的信息 - 监视器和警报 。 监视器显示核心后台任务完成的时间。 警报提供有助于提高托管域稳定性的信息或建议。

### <a name="monitors"></a>监视器

监视器是显示定期检查的托管域的情况的区域。 如果托管域有任何活动警报，则可能会导致其中一个监视器报告问题。 Azure AD DS 当前包含以下区域的监视器：

* Backup
* 与 Azure AD 同步

#### <a name="backup-monitor"></a>备份监视器

备份监视器检查托管域的自动定期备份是否成功运行。 下表详细介绍可用的备份监视器状态：

| 详细信息值 | 说明 |
| --- | --- |
| 从未备份 | 对于新建的托管域，此状态是正常的。 部署托管域后，应在 24 小时内创建第一个备份。 如果此状态仍然存在，请[打开 Azure 支持请求][azure-support]。 |
| 最后一个备份是在 1 到 14 天前创建的 | 此时间范围是备份监视器的预期状态。 自动定期备份应在此期间进行。 |
| 最后一个备份是在 14 天前创建的。 | 时间跨度超过两周表示自动定期备份出现问题。 活动严重警报可能会阻止对托管域进行备份。 解决托管域的任何活动警报。 如果备份监视器随后没有更新状态以报告最近的备份，请[打开 Azure 支持请求][azure-support]。 |

#### <a name="synchronization-with-azure-ad-monitor"></a>与 Azure AD 监视器同步

托管域与 Azure Active Directory 定期同步。 用户和组对象的数量，以及自上次同步后在 Azure Active Directory 中所做的更改数会影响同步所用的时间。 如果自上次同步托管域已超过三天，请检查并解决所有活动警报。 解决所有活动警报后，如果同步监视器未更新状态以显示最近的同步，请[打开 Azure 支持请求][azure-support]。

### <a name="alerts"></a>警报

警报针对托管域中的问题生成，需要解决这些问题才能使服务正常运行。 每个警报会对问题进行解释，并提供了一个 URL，它概述了解决此问题的特定步骤。 有关可能的警报及其解决方法的详细信息，请参阅[警报疑难解答](troubleshoot-alerts.md)。

运行状态警报分为以下严重性级别：

 * **严重警报**表示出现了对托管域造成严重影响的问题。 应该立即解决这些警报。 在解决问题之前，Azure 平台无法监视、管理、修补和同步托管域。
 * **警告警报**通知你持续下去可能影响托管域操作的问题。 这些警报还提供保护托管域的建议。
 * **信息性警报**是指不会对域造成负面影响的通知。 信息警报提供有关托管域中所发生状况的一些见解。

## <a name="next-steps"></a>后续步骤

有关运行状态页中显示的警报的详细信息，请参阅[解决针对托管域的警报][troubleshoot-alerts]

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[troubleshoot-alerts]: troubleshoot-alerts.md
