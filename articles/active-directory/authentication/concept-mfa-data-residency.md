---
title: Azure 多重身份验证数据驻留
description: 了解 Azure 多重身份验证存储哪些有关您和您的用户的个人和组织数据，以及源国家/地区内仍保留哪些数据。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c4556ece7faaacb8657a1012344f2263ee84214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480133"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Azure 多重身份验证的数据驻留和客户数据

客户数据由 Azure AD 存储在地理位置中，具体取决于组织在订阅 Microsoft 在线服务（如 Office 365 和 Azure）时提供的地址。 有关客户数据存储位置的信息，您可以使用 Microsoft 信任中心的"[数据位于何处"](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)部分。

基于云的 Azure 多重身份验证和 Azure 多重身份验证服务器进程并存储一定数量的个人数据和组织数据。 本文概述了数据的存储位置和位置。

以下多重身份验证活动当前源自美国数据中心，除非另有说明：

* 使用电话或 SMS 的双因素身份验证通常源自美国数据中心，并由全球提供商路由。
    * 来自欧洲或澳大利亚等其他地区的通用用户身份验证请求目前由该区域的数据中心处理。 其他事件（如自助服务密码重置、Azure B2C 事件或使用 NPS 扩展或 AD FS 适配器的混合方案）目前均由美国数据中心处理。
* 使用 Microsoft 身份验证器应用的推送通知源自美国数据中心。 此外，设备供应商特定的服务也可能从不同区域发挥作用。
* OATH 代码通常在美国进行验证。
    * 同样，源自其他区域（如欧洲或澳大利亚）的通用用户身份验证事件由该区域的数据中心处理。 其他事件当前由美国数据中心处理。

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Azure 多重身份验证存储的个人数据

个人数据是与特定人员关联的用户级信息。 以下数据存储包含个人信息：

* 被阻止的用户
* 绕过的用户
* 微软身份验证器设备令牌更改请求
* 多重身份验证活动报告
* 微软身份验证器激活

此信息将保留 90 天。

Azure 多重身份验证不会记录个人数据（如用户名、电话号码或 IP 地址），但有一个*UserObjectId*标识了对用户的多重身份验证尝试。 日志数据存储 30 天。

### <a name="azure-multi-factor-authentication"></a>Azure 多重身份验证

对于 Azure 公共云（不包括 Azure B2C 身份验证、NPS 扩展和 Windows 服务器 2016 或 2019 AD FS 适配器），存储以下个人数据：

| 事件类型                           | 数据存储类型 |
|--------------------------------------|-----------------|
| OATH 令牌                           | 在多重身份验证日志中     |
| 单向短信                          | 在多重身份验证日志中     |
| 语音呼叫                           | 在多重身份验证日志中<br />多重身份验证活动报告数据存储<br />如果举报了欺诈，则被阻止的用户 |
| 微软身份验证器通知 | 在多重身份验证日志中<br />多重身份验证活动报告数据存储<br />如果举报了欺诈，则被阻止的用户<br />更改 Microsoft 身份验证器设备令牌更改时的请求 |

> [!NOTE]
> 多因素身份验证活动报告数据存储存储在美国的所有云，而不考虑处理身份验证请求的区域。 微软 Azure 德国、由 21Vianet 运营的 Microsoft Azure 和 Microsoft 政府云都有独立于公共云区域数据存储的独立数据存储，但这些数据始终存储在美国。

对于 Microsoft Azure 政府、微软 Azure 德国版、由 21Vianet 操作的 Microsoft Azure、Azure B2C 身份验证、NPS 扩展和 Windows 服务器 2016 或 2019 AD FS 适配器，存储以下个人数据：

| 事件类型                           | 数据存储类型 |
|--------------------------------------|-----------------|
| OATH 令牌                           | 在多重身份验证日志中<br />多重身份验证活动报告数据存储 |
| 单向短信                          | 在多重身份验证日志中<br />多重身份验证活动报告数据存储 |
| 语音呼叫                           | 在多重身份验证日志中<br />多重身份验证活动报告数据存储<br />如果举报了欺诈，则被阻止的用户 |
| 微软身份验证器通知 | 在多重身份验证日志中<br />多重身份验证活动报告数据存储<br />如果举报了欺诈，则被阻止的用户<br />更改 Microsoft 身份验证器设备令牌更改时的请求 |

### <a name="multi-factor-authentication-server"></a>多重身份验证服务器

如果部署并运行 Azure 多重身份验证服务器，将存储以下个人数据：

> [!IMPORTANT]
> 自 2019 年 7 月 1 日起，Microsoft 将不再为新部署提供多重身份验证服务器。 希望用户进行多重身份验证的新客户应使用基于云的 Azure 多重身份验证。 在 7 月 1 日之前激活多重身份验证服务器的现有客户将能够像往常一样下载最新版本、将来的更新并生成激活凭据。

| 事件类型                           | 数据存储类型 |
|--------------------------------------|-----------------|
| OATH 令牌                           | 在多重身份验证日志中<br />多重身份验证活动报告数据存储 |
| 单向短信                          | 在多重身份验证日志中<br />多重身份验证活动报告数据存储 |
| 语音呼叫                           | 在多重身份验证日志中<br />多重身份验证活动报告数据存储<br />如果举报了欺诈，则被阻止的用户 |
| 微软身份验证器通知 | 在多重身份验证日志中<br />多重身份验证活动报告数据存储<br />如果举报了欺诈，则被阻止的用户<br />更改 Microsoft 身份验证器设备令牌更改时的请求 |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Azure 多重身份验证存储的组织数据

组织数据是租户级信息，可能会公开配置或环境设置。 以下 Azure 门户多重身份验证页的租户设置可能会存储组织数据，如锁定阈值或来电身份验证请求的来电方 ID 信息：

* 帐户锁定
* 欺诈警报
* 通知
* 电话呼叫设置

对于 Azure 多重身份验证服务器，以下 Azure 门户页面可能包含组织数据：

* 服务器设置
* 免验证一次
* 缓存规则
* 多重身份验证服务器状态

## <a name="log-data-location"></a>日志数据位置

日志信息的存储位置取决于它们在哪个区域的处理。 大多数地理位置具有本机 Azure 多重身份验证功能，因此日志数据存储在处理多重身份验证请求的同一区域中。 在没有本机 Azure 多重身份验证支持的地理位置中，它们由美国和欧洲地理位置提供服务，日志数据存储在处理多重身份验证请求的同一区域中。

某些核心身份验证日志数据仅存储在美国。 微软 Azure 德国和由 21Vianet 操作的 Microsoft Azure 始终存储在各自的云中。 Microsoft 政府云日志数据始终存储在美国。

## <a name="next-steps"></a>后续步骤

有关基于云的 Azure 多重身份验证和 Azure 多重身份验证服务器收集的用户信息的详细信息，请参阅[Azure 多重身份验证用户数据收集](howto-mfa-reporting-datacollection.md)。
