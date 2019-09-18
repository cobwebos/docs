---
title: 教程 - 创建 Azure Active Directory B2C 租户
description: 了解如何通过使用 Azure 门户创建 Azure Active Directory B2C 租户来准备注册应用程序。
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 57aed26fe2f2f03dca347836f21d1cad14a07642
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063353"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>教程：创建 Azure Active Directory B2C 租户

必须先在你管理的租户中注册应用程序，然后才能与 Azure Active Directory B2C （Azure AD B2C）交互。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建 Azure AD B2C 租户
> * 将租户链接到订阅

在下一个教程中，学习如何注册应用程序。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-azure-ad-b2c-tenant"></a>创建 Azure AD B2C 租户

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用的是包含订阅的目录。 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含你的订阅的目录。 此目录不同于将包含 Azure AD B2C 租户的其他目录。

    ![已选中订阅租户的目录和订阅筛选器](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. 在 Azure 门户的左上角，选择“创建资源”。
4. 搜索并选择“Active Directory B2C”，然后单击“创建”。
5. 选择“新建 Azure AD B2C 租户”，输入组织名称和初始域名。 选择国家/地区（以后不能更改），然后单击“创建”。

    初始域名用作租户名称的一部分。 在此示例中, 租户名称为*contoso0926Tenant.onmicrosoft.com*:

    ![Azure 门户中的 B2C 租户创建页](./media/tutorial-create-tenant/create-tenant.PNG)

6. 在“创建新的 B2C 租户或链接到现有租户”页上，选择“将现有 Azure AD B2C 租户链接到 Azure 订阅”。

    选择已创建的租户，然后选择订阅。

    对于资源组，请选择“新建”。 输入将包含该租户的资源组名，选择位置，然后单击“创建”。
1. 若要开始使用新租户，请在顶部菜单中选择 "**目录 + 订阅**" 筛选器并选择包含它的目录，确保使用的是包含 Azure AD B2C 租户的目录。

    如果第一次在列表中看不到新的 Azure B2C 租户，请刷新浏览器窗口，然后在顶部菜单中再次选择 "**目录 + 订阅**筛选器"。

    ![已选中 B2C 租户的目录和订阅筛选器](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 创建 Azure AD B2C 租户
> * 将租户链接到订阅

接下来，了解如何在新租户中注册 Web 应用程序。

> [!div class="nextstepaction"]
> [注册应用程序 >](tutorial-register-applications.md)
