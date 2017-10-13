---
title: "Azure Active Directory 门户中的登录活动报告错误代码 | Microsoft Docs"
description: "登录活动报告错误代码参考。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2a1b7b87df2cd8fa2e98f217480b46f5f6334297
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="sign-in-activity-report-error-codes-in-the-azure-active-directory-portal"></a>Azure Active Directory 门户中的登录活动报告错误代码

通过用户登录报告提供的信息，可以找到一些问题的答案，例如：

- 谁使用 Azure Active Directory 进行了登录？
- 登录到了哪些应用中？
- 哪些登录失败？失败原因是什么？

本主题列出了错误代码和相关说明。 

## <a name="how-can-i-display-failed-sign-ins"></a>如何才能显示失败的登录？ 

所有登录活动数据的第一个入口点为 Azure Active Directory 的“活动”部分中的**[登录](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)**。


![登录活动](./media/active-directory-reporting-activity-sign-ins-errors/61.png "登录活动")


在登录报告中，选择“失败”作为“登录状态”即可显示所有失败的登录。


![登录活动](./media/active-directory-reporting-activity-sign-ins-errors/06.png "登录活动")

单击已显示列表中的某个项，打开“活动详细信息: 登录”边栏选项卡。 此视图提供 Azure Active Directory 跟踪的有关登录的所有详细信息，包括“登录错误代码”和“失败原因”。

![登录活动](./media/active-directory-reporting-activity-sign-ins-errors/05.png "登录活动")


也可使用[报告 API](active-directory-reporting-api-getting-started-azure-portal.md)，作为使用 Azure 门户访问登录数据的替代。


以下部分完整概述了所有可能的错误和相关说明。 

## <a name="error-codes"></a>错误代码

| 错误| 说明 |
| --- | --- |
| 50001| 在名为 Y 的租户中找不到名为 X 的服务主体。如果应用程序尚未由租户管理员安装，则可能会发生这种情况。 或者，资源主体在目录中找不到或无效|
| 50008| SAML 断言在令牌中缺失或配置错误。|
| 50011| 回复地址缺失、配置错误或者与为应用程序配置的回复地址不匹配。|
| 50053| 帐户已锁定，因为用户尝试使用不正确的用户 ID 或密码登录的次数过多。|
| 50054| 使用了旧密码进行身份验证。|
| 50055| 密码无效，输入的密码已过期。|
| 50057| 用户帐户已禁用。|
| 50058| 在提供的凭据中找不到有关用户标识的信息，或者在租户中找不到用户，或者已发送无提示登录请求，但没有用户登录，或者服务无法对用户进行身份验证。|
| 50074| 必须进行强身份验证（双重身份验证）|
| 50079| 用户需注册进行双重身份验证|
| 50126| 用户名或密码无效，或者本地用户名或密码无效。|
| 50131| 用于各种条件性访问错误。 例如，Windows 设备状态不正确，请求因活动、访问策略和安全策略决策可疑而被阻止。|
| 50133| 会话因过期或最近更改了密码而无效。|
| 50144| 用户的 Active Directory 密码已过期。|
| 65001| 应用程序 X 无权访问应用程序 Y，或者权限已被吊销。 或者，用户或管理员尚未同意将应用程序与 ID X 配合使用。请发送针对该用户和资源的交互式授权请求。 或者，用户或管理员尚未同意将应用程序与 ID X 配合使用。请代表应用 Y 向租户管理员发送针对资源 Z 的授权请求。|
| 65005| 应用程序所需的资源访问列表不包含可以通过资源来发现的应用程序，或者客户端应用程序请求访问的资源未在其必需的资源访问列表中指定，或者 Graph 服务返回了错误的请求，或者资源找不到。|
| 70001| 在名为 Y 的租户中找不到名为 X 的应用程序。如果应用程序尚未由租户管理员安装，或者尚未获得租户中的任何用户同意，则可能会发生这种情况。 可能将身份验证请求发送给了错误的租户。|
| 80001| 没有可用的身份验证代理。|
| 80002| 身份验证代理的密码验证请求已超时。|
| 80003| 身份验证代理收到的响应无效。|
| 80004| 在登录请求中使用的用户主体名称 (UPN) 不正确。|
| 80005| 身份验证代理：出现错误。|
| 80007| 身份验证代理无法连接到 Active Directory。|
| 80010| 身份验证代理无法解密密码。|
| 81001| 用户的 Kerberos 票证太大。|
| 81002| 无法验证用户的 Kerberos 票证。|
| 81003| 无法验证用户的 Kerberos 票证。|
| 81004| Kerberos 身份验证尝试失败。|
| 81008| 无法验证用户的 Kerberos 票证。|
| 81009| 无法验证用户的 Kerberos 票证。|
| 81010| 无缝 SSO 失败，因为用户的 Kerberos 票证已过期或无效。|
| 81011| 根据用户的 Kerberos 票证中的信息找不到用户对象。|
| 81012| 尝试登录到 Azure AD 的用户不同于已登录到设备的用户。|
| 81013| 根据用户的 Kerberos 票证中的信息找不到用户对象。|
| 90014| 用于凭据中不存在预期字段的各种情况。|
| 90093| Graph 返回了针对请求的禁止访问错误代码。|



## <a name="next-steps"></a>后续步骤

如需更多详细信息，请参阅 [Azure Active Directory 门户中的登录活动报告](active-directory-reporting-activity-sign-ins.md)。

