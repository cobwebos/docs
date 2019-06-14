---
title: 教程-创建 Azure Active Directory B2C 租户
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
ms.openlocfilehash: 711b9152f9f3fa1b3573e39d1950f18b628c268a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056320"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>教程：创建 Azure Active Directory B2C 租户

必须在你管理的租户中注册应用程序，然后这些应用程序才能与 Azure Active Directory (Azure AD) B2C 交互。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建 Azure AD B2C 租户
> * 将租户链接到订阅

在下一个教程中，学习如何注册应用程序。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-azure-ad-b2c-tenant"></a>创建 Azure AD B2C 租户

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用包含你的订阅的目录。 单击**目录和订阅筛选器**在顶部菜单中，然后选择包含你的订阅的目录。 此目录是不同于将包含在 Azure AD B2C 租户。

    ![切换到订阅目录](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. 在 Azure 门户的左上角，选择“创建资源”  。
4. 搜索并选择“Active Directory B2C”  ，然后单击“创建”  。
5. 选择**创建新的 Azure AD B2C 租户**输入组织名称和初始域名。 选择国家/地区 （它不能更改更高版本），然后单击**创建**。

    初始域名用作你的租户名称的一部分。 在此示例中，租户名称是*contoso0926Tenant.onmicrosoft.com*:

    ![创建租户](./media/tutorial-create-tenant/create-tenant.PNG)

6. 上**创建新的 B2C 租户或链接至现有租户**页上，选择**将现有 Azure AD B2C 租户链接到我的 Azure 订阅**。

    选择你创建的租户，并选择你的订阅。

    对于资源组中，选择**新建**。 输入将包含该租户的资源组名，选择位置，然后单击“创建”  。
1. 若要开始使用新的租户，请确保你正在使用通过单击包含在 Azure AD B2C 租户的目录**目录和订阅筛选器**顶部的菜单，然后选择包含它的目录中。

    如果在第一个看不到新的 Azure B2C 租户列表中，刷新浏览器窗口，然后选择**目录和订阅筛选器**再次在顶部菜单中。

    ![切换到租户目录](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 创建 Azure AD B2C 租户
> * 将租户链接到订阅

接下来，了解如何在新的租户中注册 web 应用程序。

> [!div class="nextstepaction"]
> [注册你的应用程序 >](tutorial-register-applications.md)
