---
title: Azure 多重身份验证数据驻留
description: 了解 Azure 多重身份验证存储了关于你和你的用户的哪些个人和组织数据，以及哪些数据保留在来源国/地区内。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e5d8dc60ee0a1f4742382b1cec8ef3ed60e8fb3
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90970661"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Azure 多重身份验证的数据驻留和客户数据

客户数据根据你的组织在订阅 Microsoft Online 服务（例如 Microsoft 365 和 Azure）时所提供的地址 Azure AD 存储在地理位置。 要了解客户数据的存储位置，请参阅 Microsoft 信任中心的[数据存储在何处？](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)部分。

基于云的 Azure 多重身份验证和 Azure 多重身份验证服务器会处理和存储一些个人数据和组织数据。 本文简要介绍了存储哪些数据及存储在何处。

Azure 多重身份验证服务在美国、欧洲和亚太有数据中心。 以下活动源自地区数据中心，但以下情况除外：

* 使用电话呼叫的多重身份验证源自美国数据中心，由全局提供商路由。
* 根据用户的位置，当前正在处理来自其他区域（如欧洲或澳大利亚）的常规用途用户身份验证请求。
* 使用 Microsoft Authenticator 应用推送通知将根据用户的位置发起区域数据中心。
    * 特定于设备供应商的服务（如 Apple 推送通知）可能位于用户位置之外。

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>通过 Azure 多重身份验证存储的个人数据

个人数据是与特定人员关联的用户级信息。 以下数据存储包含个人信息：

* 被阻止的用户
* 免验证的用户
* Microsoft Authenticator 设备令牌更改请求
* 多重身份验证活动报告
* Microsoft Authenticator 激活

此信息将保留 90 天。

Azure 多重身份验证不记录用户名、电话号码或 IP 地址等个人数据，但有一个 UserObjectId 用于标识对用户的多重身份验证尝试。 日志数据将存储 30 天。

### <a name="azure-multi-factor-authentication"></a>Azure 多重身份验证

对于 Azure 公有云（不包括 Azure B2C 身份验证、NPS 扩展和 Windows Server 2016 或 2019 AD FS 适配器），将存储以下个人数据：

| 事件类型                           | 数据存储类型 |
|--------------------------------------|-----------------|
| OATH 令牌                           | 在多重身份验证日志中     |
| 单向短信                          | 在多重身份验证日志中     |
| 语音呼叫                           | 在多重身份验证日志中<br />多重身份验证活动报告数据存储<br />被阻止的用户（如果报告欺诈） |
| Microsoft Authenticator 通知 | 在多重身份验证日志中<br />多重身份验证活动报告数据存储<br />被阻止的用户（如果报告欺诈）<br />Microsoft Authenticator 设备令牌更改时的更改请求 |

> [!NOTE]
> 无论是哪个区域负责处理身份验证请求，所有云中的多重身份验证活动报告数据都存储在美国。 Microsoft Azure 德国、由世纪互联运营的 Microsoft Azure 以及 Microsoft 政府都有自己的数据存储，它们独立于公有云区域数据存储，但是这些数据始终存储在美国。

对于 Microsoft Azure 政府、Microsoft Azure 德国、由世纪互联运营的 Microsoft Azure、Azure B2C 身份验证、NPS 扩展，以及 Windows Server 2016 或 2019 AD FS 适配器，将存储以下个人数据：

| 事件类型                           | 数据存储类型 |
|--------------------------------------|-----------------|
| OATH 令牌                           | 在多重身份验证日志中<br />多重身份验证活动报告数据存储 |
| 单向短信                          | 在多重身份验证日志中<br />多重身份验证活动报告数据存储 |
| 语音呼叫                           | 在多重身份验证日志中<br />多重身份验证活动报告数据存储<br />被阻止的用户（如果报告欺诈） |
| Microsoft Authenticator 通知 | 在多重身份验证日志中<br />多重身份验证活动报告数据存储<br />被阻止的用户（如果报告欺诈）<br />Microsoft Authenticator 设备令牌更改时的更改请求 |

### <a name="multi-factor-authentication-server"></a>多重身份验证服务器

如果部署并运行 Azure 多重身份验证服务器，将存储以下个人数据：

> [!IMPORTANT]
> 从 2019 年 7 月 1 日开始，Microsoft 不再为新部署提供多重身份验证服务器。 希望用户执行多重身份验证的新客户应使用基于云的 Azure 多重身份验证。 在 7 月 1 日之前激活了多重身份验证服务器的现有客户可像平时一样下载最新版本和未来的更新，也可生成激活凭据。

| 事件类型                           | 数据存储类型 |
|--------------------------------------|-----------------|
| OATH 令牌                           | 在多重身份验证日志中<br />多重身份验证活动报告数据存储 |
| 单向短信                          | 在多重身份验证日志中<br />多重身份验证活动报告数据存储 |
| 语音呼叫                           | 在多重身份验证日志中<br />多重身份验证活动报告数据存储<br />被阻止的用户（如果报告欺诈） |
| Microsoft Authenticator 通知 | 在多重身份验证日志中<br />多重身份验证活动报告数据存储<br />被阻止的用户（如果报告欺诈）<br />Microsoft Authenticator 设备令牌更改时的更改请求 |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>通过 Azure 多重身份验证存储的组织数据

组织数据是可公开配置或环境设置的租户级别的信息。 来自 Azure 门户中下列多重身份验证页面的租户设置可存储组织数据，例如锁定阈值或传入电话身份验证请求的调用方 ID 信息：

* 帐户锁定
* 欺诈警报
* 通知
* 电话呼叫设置

对于 Azure 多重身份验证服务器，Azure 门户的以下页面可能包含组织数据：

* 服务器设置
* 免验证一次
* 缓存规则
* 多重身份验证服务器状态

## <a name="log-data-location"></a>日志数据位置

日志信息的存储位置取决于处理它们的区域。 大多数地理区域都具有本机 Azure 多重身份验证功能，因此日志数据存储在处理多重身份验证请求的同一区域中。 在没有本机 Azure 多重身份验证支持的地理区域中，它们由美国或欧洲地理区域提供服务，日志数据存储在处理多重身份验证请求的同一区域中。

某些核心身份验证日志数据仅存储在美国。 Microsoft Azure 德国以及世纪互联运营的 Microsoft Azure 始终存储在各自的云中。 Microsoft 政府云日志数据始终存储在美国。

## <a name="next-steps"></a>后续步骤

要详细了解基于云的 Azure 多重身份验证和 Azure 多重身份验证服务器收集的用户信息，请参阅 [Azure 多重身份验证用户数据收集](howto-mfa-reporting-datacollection.md)。
