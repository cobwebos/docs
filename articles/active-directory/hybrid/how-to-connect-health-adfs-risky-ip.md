---
title: Azure AD Connect Health AD FS 风险 IP 报表 |Microsoft Docs
description: 介绍 Azure AD Connect Health AD FS 风险 IP 报表。
services: active-directory
documentationcenter: ''
ms.reviewer: zhiweiwangmsft
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49b93cb7852692e4dad65fcbd72cd749db1b16fb
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540901"
---
# <a name="risky-ip-report-public-preview"></a>风险 IP 报表 （公共预览版）
AD FS 客户可以将密码身份验证终结点公开给 Internet，以便为最终用户提供身份验证服务，方便他们访问 Office 365 之类的 SaaS 应用程序。 在这种情况下，恶意参与者可能会尝试登录 AD FS 系统，用猜测最终用户密码的方式获得应用程序资源的访问权限。 AD FS 提供 Extranet 帐户锁定功能，可以防止这些类型的攻击，自 AD FS 出现在 Windows Server 2012 R2 中以后就是这样。 如果所用版本较低，强烈建议将 AD FS 系统升级到 Windows Server 2016。 <br />

另外，单个 IP 地址可能会尝试针对多个用户进行多次登录。 在这些情况下，每个用户的尝试次数必须在 AD FS 中的帐户锁定保护阈值以下。 Azure AD Connect Health 现在提供“风险 IP 报表”来检测这种情况，并在发生这种情况时通知管理员。 下面是此报表的主要优点： 
- 检测超出基于密码的登录失败次数阈值的 IP 地址
- 支持在密码错误或出现 Extranet 状态为锁定的情况下，强制登录失败
- 使用可自定义的电子邮件设置，在发生这种情况时通过电子邮件通知来提醒管理员
- 阈值设置可以自定义，因此可以与组织的安全策略相匹配
- 报表可以下载，适合进行脱机分析，并可通过自动化操作与其他系统集成

> [!NOTE]
> 若要使用此报表，请务必启用 AD FS 审核。 有关详细信息，请参阅 [为 AD FS 启用审核](how-to-connect-health-agent-install.md#enable-auditing-for-ad-fs)。 <br />
> 若要访问预览版，需要提供全局管理员或[安全读取者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)权限。  
> 

## <a name="what-is-in-the-report"></a>什么是报表？
通过 Web 应用程序代理服务器聚合中活动客户端 IP 地址失败的登录。 “风险 IP”报表中的每个项目都会显示有关失败的 AD FS 登录活动（失败次数超出指定阈值）的聚合信息。 它提供以下信息：![Azure AD Connect Health 门户](./media/how-to-connect-health-adfs/report4a.png)

| 报告项 | 描述 |
| ------- | ----------- |
| 时间戳 | 当检测时间窗口启动时，显示基于 Azure 门户本地时间的时间戳。<br /> 所有每日事件都在 UTC 时间的午夜生成。 <br />每小时事件的时间戳舍入为整点。 可以在已导出文件的“firstAuditTimestamp”中找到第一个活动开始时间。 |
| 触发器类型 | 显示检测时间窗口的类型。 聚合触发器类型为每小时或每日。 这适用于检测高频暴力破解攻击，与之相反的是慢速攻击，后者在一天中的尝试攻击行动是分散的。 |
| IP 地址 | 密码错误或者 Extranet 登录活动处于锁定状态的单一风险 IP 地址。 这可能是 IPv4 或 IPv6 地址。 |
| 不正确密码错误计数 | 在检测时间窗口期间的 IP 地址中发生“密码不正确”错误的计数。 某些用户的“密码不正确”错误可能发生多次。 请注意，这不包括因密码过期而尝试失败的情况。 |
| Extranet 锁定错误计数 | 在检测时间窗口期间的 IP 地址中发生“Extranet 锁定”错误的计数。 某些用户的“Extranet 锁定”错误可能发生多次。 仅当在 AD FS（2012R2 或更高版本）中配置 Extranet 锁定的情况下，才会出现这种错误。 <b>注意</b> 强烈建议在允许使用密码进行 Extranet 登录的情况下启用此功能。 |
| 已尝试的唯一用户 | 在检测时间窗口期间的 IP 地址中进行了尝试的唯一用户的计数。 这样就提供了一种机制，用于区分单用户攻击模式和多用户攻击模式。  |

例如，下面的报表项表明，在 2018/02/28 下午 6 点到 7 点这段时间，IP 地址 <i>104.2XX.2XX.9</i> 没有出现“密码不正确”错误，但出现 284 次“Extranet 锁定”错误。 符合条件的 14 个唯一用户受影响。 活动事件超出指定的报表小时阈值。 

![Azure AD Connect Health 门户](./media/how-to-connect-health-adfs/report4b.png)

> [!NOTE]
> - 只有那些超出指定阈值的活动才会显示在报表列表中。 
> - 此报表最多可回溯 30 天。
> - 此警报报表不显示 Exchange IP 地址或专用 IP 地址。 它们仍包括在导出列表中。 
>

![Azure AD Connect Health 门户](./media/how-to-connect-health-adfs/report4c.png)

## <a name="load-balancer-ip-addresses-in-the-list"></a>负载均衡器 IP 地址列表中
负载均衡器聚合失败的登录活动并命中警报阈值。 如果出现负载均衡器 IP 地址，很可能是因为外部负载均衡器在将请求传递给 Web 应用程序代理服务器时未发送客户端 IP 地址。 请正确配置负载均衡器，使之传递转发客户端 IP 地址。 

## <a name="download-risky-ip-report"></a>下载有风险 IP 报表 
使用**下载**功能，可以将过去 30 天的整个风险 IP 地址列表从 Connect Health 门户导出 导出结果将包括每个检测时段所有失败的 AD FS 登录活动，因此可以在导出后自定义筛选功能。 除了门户中突出显示的聚合，导出结果还显示有关已失败登录活动（按 IP 地址划分）的更多详细信息：

|  报告项  |  描述  | 
| ------- | ----------- | 
| firstAuditTimestamp | 显示在检测时段启动失败的活动时的第一个时间戳。  | 
| lastAuditTimestamp | 显示在检测时段结束失败的活动时的最后一个时间戳。  | 
| attemptCountThresholdIsExceeded | 表示当前活动是否超出警报阈值的标志。  | 
| isWhitelistedIpAddress | 表示 IP 地址是否从警报和报表进行筛选的标志。 专用 IP 地址（<i>10.x.x.x、172.x.x.x 和 192.168.x.x</i>）和 Exchange IP 地址会在筛选后标记为 True。 如果看到专用 IP 地址范围，则很可能是因为外部负载均衡器在将请求传递给 Web 应用程序代理服务器时未发送客户端 IP 地址。  | 

## <a name="configure-notification-settings"></a>配置通知设置
可以通过“通知设置”更新报表的管理联系人。 默认情况下，风险 IP 警报电子邮件通知处于关闭状态。 若要启用通知，可以切换“获取 IP 地址超过失败活动阈值报表的电子邮件通知”下的按钮 与 Connect Health 中的泛型警报通知设置一样，它可以让你在此处自定义有关风险 IP 报表的指定通知接收者列表。 也可在进行更改时通知所有全局管理员。 

## <a name="configure-threshold-settings"></a>配置阈值设置
可以通过“阈值设置”更新警报阈值。 首先，系统已默认设置阈值。 在风险 IP 报表阈值设置中，共有四个类别：

![Azure AD Connect Health 门户](./media/how-to-connect-health-adfs/report4d.png)

| 阈值项 | 描述 |
| --- | --- |
| (错误 U/P + Extranet 锁定) / 天  | 阈值设置，用于在特定条件下报告活动并触发警报通知。该特定条件是：每**天**的“密码不正确”错误的计数加上“Extranet 锁定”错误的计数超出该阈值。 |
| (错误 U/P + Extranet 锁定) / 小时 | 阈值设置，用于在特定条件下报告活动并触发警报通知。该特定条件是：每**小时**的“密码不正确”错误的计数加上“Extranet 锁定”错误的计数超出该阈值。 |
| Extranet 锁定 / 天 | 阈值设置，用于在特定条件下报告活动并触发警报通知。该特定条件是：每**天**的“Extranet 锁定”错误的计数超出该阈值。 |
| Extranet 锁定 / 小时| 阈值设置，用于在特定条件下报告活动并触发警报通知。该特定条件是：每**小时**的“Extranet 锁定”错误的计数超出该阈值。 |

> [!NOTE]
> - 在一小时的设置更改以后，将会应用对报表阈值的更改。 
> - 现有的报表项不会受到阈值更改的影响。 
> - 建议分析环境中发现的事件数，并相应地调整阈值。 
>
>

## <a name="faq"></a>常见问题
**为何会出现在报表中的专用 IP 地址范围？**  <br />
专用 IP 地址（<i>10.x.x.x、172.x.x.x 和 192.168.x.x</i>）和 Exchange IP 地址会在筛选后在 IP 允许列表中标记为 True。 如果看到专用 IP 地址范围，则很可能是因为外部负载均衡器在将请求传递给 Web 应用程序代理服务器时未发送客户端 IP 地址。

**为何会出现负载均衡器在报表中的 IP 地址？**  <br />
如果出现负载均衡器 IP 地址，很可能是因为外部负载均衡器在将请求传递给 Web 应用程序代理服务器时未发送客户端 IP 地址。 请正确配置负载均衡器，使之传递转发客户端 IP 地址。 

**我应该做什么才能阻止 IP 地址？**  <br />
应该将标识的恶意 IP 地址添加到防火墙，或者在 Exchange 中进行阻止。   <br />

**为何不会出现此报表中的任何项？** <br />
- 失败的登录活动数未超出阈值设置。
- 确保在 AD FS 服务器列表中没有活动的“运行状况服务不是最新的”警报。  详细了解[如何排查此警报问题](how-to-connect-health-data-freshness.md)。
- AD FS 场中未启用审核。

**为何会出现任何报表访问权限？**  <br />
需要全局管理员或[安全读取者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#security-reader)权限。 请联系全局管理员以获得访问权限。


## <a name="next-steps"></a>后续步骤
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Azure AD Connect Health 代理安装](how-to-connect-health-agent-install.md)
