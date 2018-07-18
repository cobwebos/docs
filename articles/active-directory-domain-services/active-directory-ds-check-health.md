---
title: Azure AD 域服务 - 检查托管域的运行状况 | Microsoft Docs
description: 在 Azure 门户中使用运行状况页检查托管域的运行状况。
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 8999eec3-f9da-40b3-997a-7a2587911e96
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: ergreenl
ms.openlocfilehash: ffb00ce85a06812dfc42dfbbba74f712c1877615
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36212367"
---
# <a name="check-the-health-of-an-azure-ad-domain-services-managed-domain"></a>检查 Azure AD 域服务托管域的运行状况

## <a name="overview-of-the-health-page"></a>运行状况页概述
使用 Azure 门户中的运行状况页，可以随时了解托管域发生的最新情况。 本文逐步讲解运行状况页中的元素。

### <a name="how-to-view-the-health-of-your-managed-domain"></a>如何查看托管域的运行状况
1. 在 Azure 门户中导航到 [Azure AD 域服务页](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)。
2. 单击要查看其运行状况的域。
3. 在左侧导航窗格中，单击“运行状况”。

下图演示了一个示例运行状况页：![示例运行状况页](.\media\active-directory-domain-services-alerts\health-page.png)

>[!NOTE]
> 托管域的运行状况每隔一小时评估一次。 对托管域进行更改后，请等到进入下一评估周期，以查看托管域的更新运行状况。 右上角的“上次评估时间”时间戳显示上次评估托管域运行状况的时间。
>

### <a name="status-of-your-managed-domain"></a>托管域的状态
运行状况页右上角的状态指示托管域的总体运行状况。 该状态考虑到了域中发生的所有现有警报。 也可以在 Azure AD 域服务的概述页上查看域的状态。

| 状态 | 图标 | 说明 |
| --- | :----: | --- |
| 正在运行 | <img src= ".\media\active-directory-domain-services-alerts\running-icon.png" width = "15"> | 托管域平稳运行，未发生任何严重警报或警告警报。 此域可能发生了信息性的警报。 |
| 需要注意（警告） | <img src= ".\media\active-directory-domain-services-alerts\warning-icon.png" width = "15"> | 托管域中未发生严重警报，但有一个或多个警告警报需要解决。 |
| 需要注意（严重） | <img src= ".\media\active-directory-domain-services-alerts\critical-icon.png" width = "15"> | 托管域上发生了一个或多个严重警报。 此外，还可能会收到警告和/或信息性警报。 |
| 正在部署 | <img src= ".\media\active-directory-domain-services-alerts\deploying-icon.png" width = "15"> | 正在部署域。 |

## <a name="monitors"></a>监视器
监视器是 Azure AD 域服务定期监视的托管域功能。 使监视器保持正常运行状态的最佳方法是解决托管域的任何现存警报。

Azure AD 域服务目前会监视以下功能：
 - 备份
 - 与 Azure AD 同步

### <a name="the-backup-monitor"></a>“备份”监视器
此功能监视是否正在执行托管域的定期备份。 下表解释了备份监视器详细信息列中期望显示的内容：

| 详细信息值 | 说明 |
| --- | --- |
|“从未备份” | 对于新建的托管域，此状态是正常的。 一般情况下，会在预配托管域之后的 24 小时内创建第一个备份。 如果托管域不是新建的，或者此状态的持续时间有异常，请[联系支持人员](active-directory-ds-contact-us.md)。 |
| 最后一个备份是在 1 到 14 天前创建的 | 一般情况下，备份监视器预期会看到此值。 |
| 最后一个备份是在 14 天前创建的。 | 自上次备份以来超过两周的时间都是不正常的。 现存的严重警报可能会阻止定期备份托管域。 首先，请解决托管域的任何现存警报，如果问题持续出现，请[联系支持人员](active-directory-ds-contact-us.md)。 |


### <a name="the-synchronization-with-azure-ad-monitor"></a>“与 Azure AD 同步”监视器
Microsoft 会监视托管域与 Azure Active Directory 同步的频率。 对象（用户和组）的数目以及自上次同步以来在 Azure AD 目录中所做的更改数目都可能影响同步周期的持续时间。 如果托管域上次是在三天前同步的，请[联系支持人员](active-directory-ds-contact-us.md)。

## <a name="alerts"></a>警报
如果需要解决托管域中的问题才能让 Azure AD 域服务运行，则会生成警报。 每条警报解释了问题，并提供一个解决方法 URL，其中概述了解决问题的步骤。 若要查看所有警报及其解决方法，请访问[排查警报问题](active-directory-ds-troubleshoot-alerts.md)一文。

### <a name="alert-severity"></a>警报严重性
警报分为三个不同的严重级别：严重、警告和信息性。

 * **严重警报**表示出现了对托管域造成严重影响的问题。 应该立即解决这些警报，因为 Microsoft 无法监视、管理、修补和同步托管域。 
 * **警告警报**告知出现了将来可能影响托管域的问题。 这些警报提供保护托管域的建议。
 * **信息性警报**是指不会对域造成负面影响的通知。 信息性警报旨在让你了解域和 Azure AD 域服务中发生的情况。

## <a name="next-steps"></a>后续步骤
- [解决托管域中的警报](active-directory-ds-troubleshoot-alerts.md)
- [详细了解 Azure AD 域服务](active-directory-ds-overview.md)
- [联系产品团队](active-directory-ds-contact-us.md)
