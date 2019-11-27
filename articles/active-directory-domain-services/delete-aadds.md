---
title: 删除 Azure Active Directory 域服务 |Microsoft Docs
description: 了解如何使用 Azure 门户禁用或删除 Azure Active Directory 域服务托管域
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 9f0b8a438a7c236c5e016673a59bfb691e9c2152
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546292"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>使用 Azure 门户删除 Azure Active Directory 域服务托管域

如果不再需要托管域，可以删除 Azure Active Directory 域服务（Azure AD DS）实例。 没有关闭或暂时禁用 Azure AD DS 托管域的选项。 删除 Azure AD DS 托管域不会删除或对 Azure AD 租户产生负面影响。 本文介绍如何使用 Azure 门户删除 Azure AD DS 托管域。

> [!WARNING]
> **删除是永久性的，不能撤消。**
> 删除 Azure AD DS 托管域时，会执行以下步骤：
>   * 将取消设置托管域的域控制器，并将从虚拟网络中删除。
>   * 托管域上的数据将被永久删除。 此数据包括你创建的自定义 Ou、Gpo、自定义 DNS 记录、服务主体、Gmsa 等。
>   * 已加入到托管域的计算机将丢失其与域的信任关系，需要从域中取消加入。
>       * 无法使用公司 AD 凭据登录到这些计算机。 相反，你必须使用该计算机的本地管理员凭据。

## <a name="delete-the-managed-domain"></a>删除托管域

若要删除 Azure AD DS 托管域，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择**Azure AD 域服务**"。
1. 选择 Azure AD DS 托管域的名称，例如*contoso.com*。
1. 在“概览”页上，选择“删除”。 若要确认删除，请再次键入托管域的域名，然后选择 "**删除**"。

删除 Azure AD DS 托管域可能需要15-20 分钟或更长时间。

## <a name="next-steps"></a>后续步骤

请考虑为你要在 Azure AD DS 中看到的功能[共享反馈][feedback]。

如果要再次开始使用 Azure AD DS，请参阅[创建和配置 Azure Active Directory 域服务实例][create-instance]。

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md
