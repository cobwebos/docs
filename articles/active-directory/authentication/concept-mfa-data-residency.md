---
title: Azure 多重身份验证数据驻留
description: 了解个人和组织数据 Azure 多重身份验证如何存储你和你的用户，以及在源国家/地区的数据。
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480133"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Azure 多重身份验证的数据驻留和客户数据

客户数据根据你的组织在订阅 Microsoft Online 服务（例如 Office 365 和 Azure）时所提供的地址 Azure AD 存储在地理位置。 有关存储客户数据的位置的信息，可以使用 Microsoft 信任中心的 "[数据的位置](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)" 部分。

基于云的 Azure 多重身份验证和 Azure 多重身份验证服务器处理并存储一些个人数据和组织数据。 本文概述数据的存储内容和位置。

以下多重身份验证活动目前源自美国数据中心，但以下情况除外：

* 使用电话呼叫或短信的双因素身份验证通常来自美国数据中心，由全局提供商路由。
    * 来自其他区域（如欧洲或澳大利亚）的常规用途用户身份验证请求当前由该区域的数据中心处理。 其他事件（如自助服务密码重置、Azure B2C 事件，或使用 NPS 扩展或 AD FS 适配器的混合方案）当前均由美国数据中心处理。
* 使用 Microsoft Authenticator 应用推送通知来自美国数据中心。 此外，设备供应商特定的服务也可能会从不同的区域开始播放。
* OATH 代码目前通常在美国
    * 同样，源自其他区域（如欧洲或澳大利亚）的常规用途用户身份验证事件由该区域的数据中心处理。 其他事件当前由我们的数据中心处理。

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Azure 多重身份验证存储的个人数据

个人数据是与特定人员关联的用户级信息。 以下数据存储包含个人信息：

* 被阻止的用户
* 绕过用户
* Microsoft Authenticator 设备令牌更改请求
* 多重身份验证活动报表
* Microsoft Authenticator 激活

此信息将保留90天。

Azure 多重身份验证不记录个人数据（例如用户名、电话号码或 IP 地址），但有一个*UserObjectId* ，用于标识用户的多重身份验证尝试。 日志数据存储30天。

### <a name="azure-multi-factor-authentication"></a>Azure 多重身份验证

对于 Azure 公有云，不包括 Azure B2C 身份验证、NPS 扩展和 Windows Server 2016 或 2019 AD FS 适配器，将存储以下个人数据：

| 事件类型                           | 数据存储类型 |
|--------------------------------------|-----------------|
| OATH 令牌                           | 在多重身份验证日志中     |
| 单向短信                          | 在多重身份验证日志中     |
| 语音呼叫                           | 在多重身份验证日志中<br />多重身份验证活动报表数据存储<br />如果已报告欺诈，则被阻止的用户 |
| Microsoft Authenticator 通知 | 在多重身份验证日志中<br />多重身份验证活动报表数据存储<br />如果已报告欺诈，则被阻止的用户<br />Microsoft Authenticator 设备令牌更改时更改请求 |

> [!NOTE]
> 不管处理身份验证请求的区域如何，多因素身份验证活动报表数据存储都存储在所有云的美国中。 Microsoft Azure 德国，由世纪互联运营的 Microsoft Azure 和 Microsoft 政府云具有独立于公有云区域数据存储的独立数据存储，但是，此数据始终存储在美国中。

对于 Microsoft Azure 政府、Microsoft Azure 德国、由世纪互联运营的 Microsoft Azure、Azure B2C 身份验证、NPS 扩展以及 Windows Server 2016 或 2019 AD FS 适配器，将存储以下个人数据：

| 事件类型                           | 数据存储类型 |
|--------------------------------------|-----------------|
| OATH 令牌                           | 在多重身份验证日志中<br />多重身份验证活动报表数据存储 |
| 单向短信                          | 在多重身份验证日志中<br />多重身份验证活动报表数据存储 |
| 语音呼叫                           | 在多重身份验证日志中<br />多重身份验证活动报表数据存储<br />如果已报告欺诈，则被阻止的用户 |
| Microsoft Authenticator 通知 | 在多重身份验证日志中<br />多重身份验证活动报表数据存储<br />如果已报告欺诈，则被阻止的用户<br />Microsoft Authenticator 设备令牌更改时更改请求 |

### <a name="multi-factor-authentication-server"></a>多重身份验证服务器

如果部署和运行 Azure 多重身份验证服务器，则会存储以下个人数据：

> [!IMPORTANT]
> 从2019年7月1日起，Microsoft 将不再为新部署提供多重身份验证服务器。 想要从其用户请求多重身份验证的新客户应使用基于云的 Azure 多重身份验证。 已激活7月1日前多重身份验证服务器的现有客户将能够下载最新版本、未来更新并像平常一样生成激活凭据。

| 事件类型                           | 数据存储类型 |
|--------------------------------------|-----------------|
| OATH 令牌                           | 在多重身份验证日志中<br />多重身份验证活动报表数据存储 |
| 单向短信                          | 在多重身份验证日志中<br />多重身份验证活动报表数据存储 |
| 语音呼叫                           | 在多重身份验证日志中<br />多重身份验证活动报表数据存储<br />如果已报告欺诈，则被阻止的用户 |
| Microsoft Authenticator 通知 | 在多重身份验证日志中<br />多重身份验证活动报表数据存储<br />如果已报告欺诈，则被阻止的用户<br />Microsoft Authenticator 设备令牌更改时更改请求 |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Azure 多重身份验证存储的组织数据

组织数据是可以公开配置或环境设置的租户级信息。 以下 Azure 门户多因素身份验证页面中的租户设置可能存储组织数据，如锁定阈值或传入电话身份验证请求的调用方 ID 信息：

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

日志信息的存储位置取决于处理它们的区域。 大多数地区具有本机 Azure 多重身份验证功能，因此日志数据存储在处理多重身份验证请求的同一区域中。 在没有本机 Azure 多重身份验证支持的地理位置中，它们由美国或欧洲地区提供服务，日志数据存储在处理多重身份验证请求的同一区域中。

某些核心身份验证日志数据仅存储在美国中。 由世纪互联运营的 Microsoft Azure 德国和 Microsoft Azure 始终存储在各自的云中。 Microsoft 政府云日志数据始终存储在美国中。

## <a name="next-steps"></a>后续步骤

有关基于云的 Azure 多重身份验证和 Azure 多重身份验证服务器收集的用户信息的详细信息，请参阅[Azure 多重身份验证用户数据收集](howto-mfa-reporting-datacollection.md)。
