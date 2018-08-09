---
title: 禁用 Azure Active Directory 域服务 | Microsoft Docs
description: 使用 Azure 门户禁用 Azure Active Directory 域服务
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/27/2017
ms.author: maheshu
ms.openlocfilehash: af24c7f9b721aab4f1ab810cf42f737fd14bdf88
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505569"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>使用 Azure 门户禁用 Azure Active Directory 域服务
本文演示如何使用 Azure 门户为 Azure AD 目录禁用 Azure Active Directory (AD) 域服务。

> [!WARNING]
> **删除是永久性的，无法撤消。**
> 请谨慎继续！ 删除托管域时：
  * 将取消设置托管域的域控制器，并将从虚拟网络中删除。
  * 托管域上的数据将被永久删除。 这包括已在托管域上创建的自定义 OU、GPO、自定义 DNS 记录、服务主体、GMSA 等。
  * 已加入到托管域的计算机将丢失其与域的信任关系，需要从域中取消加入。
  * 将无法使用公司 AD 凭据登录到这些计算机。 对这些计算机改用本地管理员凭据。
删除托管域不会删除 Azure AD 目录，否则会对目录产生不利影响。
>

执行以下步骤删除 Azure AD 域服务托管域：
1. 在 Azure 门户中导航到 [Azure AD 域服务扩展](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices)。
2. 单击托管域的名称。

    ![选择要删除的域](./media/getting-started/domain-services-delete-select-domain.png)

3. 在“概述”页上，单击“删除”按钮。

    ![删除域](./media/getting-started/domain-services-delete-domain.png)

4. 若要确认删除，请键入托管域的 DNS 域名。 完成后单击“删除”按钮。

    ![删除域确认](./media/getting-started/domain-services-delete-domain-confirm.png)

大约 15 到 20 分钟后托管域将被删除。

欢迎各位[分享反馈](active-directory-ds-contact-us.md)，让我们了解哪些功能可在你们今后选择 Azure AD 域服务时提供帮助。 此外，这些反馈可帮助我们改进服务，使其更好地满足部署需求，适合具体的用例。
