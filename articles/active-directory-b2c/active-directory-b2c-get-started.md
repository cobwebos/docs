---
title: "Azure Active Directory B2C：创建 Azure Active Directory B2C 租户 | Microsoft Docs"
description: "有关如何创建 Azure Active Directory B2C 租户的主题"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: patricka
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 06/07/2017
ms.author: swkrish
ms.openlocfilehash: 213d532f484056f3833c743d25c5e6faa5b732e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-active-directory-b2c-tenant-in-the-azure-portal"></a>在 Azure 门户中创建 Azure Active Directory B2C 租户

此快速入门可帮助在数分钟内创建一个 Microsoft Azure Active Directory (Azure AD) B2C 租户。 创建完成后，便拥有了一个可用于注册 B2C 应用程序的 B2C 租户（也称为目录）。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

##  <a name="log-in-to-azure"></a>登录 Azure

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-an-azure-ad-b2c-tenant"></a>创建 Azure AD B2C 租户

无法在现有的租户中启用 B2C 功能。 需要创建一个 Azure AD B2C 租户。

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

恭喜，已成功创建 Azure Active Directory B2C 租户。 你是该租户的全局管理员。 可以根据需要添加其他全局管理员。 若要切换到新租户，请单击“管理新租户”链接。

![“管理新租户”链接](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> 如果计划将 B2C 租户用于生产应用，请阅读[生产规模与预览 B2C 租户](active-directory-b2c-reference-tenant-type.md)一文。 当删除现有 B2C 租户并重新创建具有相同域名的 B2C 租户时，存在已知问题。 需创建具有不同域名的 B2C 租户。
>
>

## <a name="link-your-tenant-to-your-subscription"></a>将租户链接到订阅

需要将 Azure AD B2C 租户链接至 Azure 订阅，才能启用所有 B2C 功能并支付使用费。 若要了解详细信息，请参阅[此文章](active-directory-b2c-how-to-enable-billing.md)。 如果未将 Azure AD B2C 租户链接到 Azure 订阅，则某些功能会被阻止，且 B2C 设置中将显示一条警告消息（“未将任何订阅链接到此 B2C 租户，或者订阅需要关注。”）。 在将应用转移到生产环境之前，必须执行此步骤。

## <a name="easy-access-to-settings"></a>轻松访问设置

[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

也可在门户顶部的“搜索资源”中输入 `Azure AD B2C` 来访问边栏选项卡。 在结果列表中，选择“Azure AD B2C”即可访问 B2C 设置边栏选项卡。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在 B2C 租户中注册 B2C 应用程序](active-directory-b2c-app-registration.md)