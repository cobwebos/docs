---
title: 配置 DNS 以使用 LDAPS 通过 Internet 访问托管域 | Microsoft Docs
description: 配置 DNS 以使用 LDAPS 通过 Internet 访问 Azure AD 域服务托管域
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: a47f0f3e-2578-422a-a421-034f66de38f5
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: maheshu
ms.openlocfilehash: 669e0392cb77434c372c9af3c4d467d19cff8abd
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39501728"
---
# <a name="configure-dns-to-access-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>配置 DNS 以使用安全 LDAP (LDAPS) 访问 Azure AD 域服务托管域

## <a name="before-you-begin"></a>开始之前
完成[任务 3：使用 Azure 门户为托管域启用安全 LDAP](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)

## <a name="task-4-configure-dns-to-access-the-managed-domain-from-the-internet"></a>任务 4：配置 DNS 以便从 Internet 访问托管域
> [!TIP]
> **可选任务** - 如果不打算使用 LDAPS 来通过 Internet 访问托管域，请跳过此配置任务。
>
>

开始此任务之前，请完成[任务 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md) 中所述的步骤。

启用通过 Internet 的安全 LDAP 访问后，需要更新 DNS，使客户端计算机能够找到此托管域。 “LDAPS 访问的外部 IP 地址”中的“属性”选项卡中会显示外部 IP 地址。

请配置外部 DNS 提供程序，使托管域的 DNS 名称（例如“ldaps.contoso100.com”）指向此外部 IP 地址。 例如，创建以下 DNS 条目：

    ldaps.contoso100.com  -> 52.165.38.113

就这么简单！ 现在，可以使用安全 LDAP 通过 Internet 连接到托管域。

> [!WARNING]
> 请记住，客户端计算机必须信任 LDAPS 证书的颁发者，才能成功使用 LDAPS 连接到托管域。 如果使用公开的受信任证书颁发机构，则不需要采取任何操作，因为客户端计算机信任这些证书颁发者。 如果使用自签名证书，请在客户端计算机的受信任证书存储中安装自签名证书的公开部分。
>
>

## <a name="next-step"></a>后续步骤
[任务 5：绑定到托管域和锁定安全 LDAP 访问](active-directory-ds-ldaps-bind-lockdown.md)
