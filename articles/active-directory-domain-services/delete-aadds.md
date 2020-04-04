---
title: 删除 Azure 活动目录域服务 |微软文档
description: 了解如何使用 Azure 门户禁用或删除 Azure 活动目录域服务托管域
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 595436daa2efbd8e706a539d0a89c3ea98be31ff
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655468"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>使用 Azure 门户删除 Azure 活动目录域服务托管域

如果不再需要托管域，可以删除 Azure 活动目录域服务 （Azure AD DS） 实例。 没有关闭或暂时禁用 Azure AD DS 托管域的选项。 删除 Azure AD DS 托管域不会删除 Azure AD 租户或以其他方式对 Azure AD 租户产生不利影响。 本文介绍如何使用 Azure 门户删除 Azure AD DS 托管域。

> [!WARNING]
> **删除是永久性的，无法撤消。**
> 删除 Azure AD DS 托管域时，将执行以下步骤：
>   * 将取消设置托管域的域控制器，并将从虚拟网络中删除。
>   * 托管域上的数据将被永久删除。 此数据包括您创建的自定义 O、GPO、自定义 DNS 记录、服务主体、GMSA 等。
>   * 已加入到托管域的计算机将丢失其与域的信任关系，需要从域中取消加入。
>       * 您无法使用公司 AD 凭据登录到这些计算机。 相反，您必须对计算机使用本地管理员凭据。

## <a name="delete-the-managed-domain"></a>删除托管域

要删除 Azure AD DS 托管域，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择**Azure AD 域服务**。
1. 选择 Azure AD DS 托管域的名称，如*aaddscontoso.com*。
1. 在“概览”**** 页上，选择“删除”****。 要确认删除，请再次键入托管域的域名，然后选择 **"删除**"。

删除 Azure AD DS 托管域可能需要 15-20 分钟或更长时间。

## <a name="next-steps"></a>后续步骤

请考虑[共享][feedback]有关您希望在 Azure AD DS 中查看的功能的反馈。

如果要再次开始使用 Azure AD DS，请参阅[创建和配置 Azure 活动目录域服务实例][create-instance]。

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md
