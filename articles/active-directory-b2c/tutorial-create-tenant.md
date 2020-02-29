---
title: 教程-创建 Azure Active Directory B2C 租户
description: 了解如何通过使用 Azure 门户创建 Azure Active Directory B2C 租户来准备注册应用程序。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c31f3c4c6688af7d2142180e8d9b7100965bad96
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186397"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>教程：创建 Azure Active Directory B2C 租户

必须先在你管理的租户中注册应用程序，然后才能与 Azure Active Directory B2C （Azure AD B2C）交互。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建 Azure AD B2C 租户
> * 将租户链接到订阅
> * 切换到包含 Azure AD B2C 租户的目录
> * 将 Azure AD B2C 资源添加为 Azure 门户中的**收藏**项

在下一个教程中，学习如何注册应用程序。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-azure-ad-b2c-tenant"></a>创建 Azure AD B2C 租户

1. 登录 [Azure 门户](https://portal.azure.com/)。 使用 Azure 帐户登录，该帐户至少分配有订阅中的[参与者](../role-based-access-control/built-in-roles.md)角色或订阅中的资源组。

1. 选择包含你的订阅的目录。

    在 "Azure 门户" 工具栏中，选择 "**目录 + 订阅**" 图标，然后选择包含你的订阅的目录。 此目录与将包含 Azure AD B2C 租户的目录不同。

    ![订阅租户，已选择订阅租户的目录 + 订阅筛选器](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. 在 Azure 门户菜单或“主页”页上，选择“创建资源”。
1. 搜索 " **Azure Active Directory B2C**"，然后选择 "**创建**"。
1. 选择“创建新的 Azure AD B2C 租户”。

    ![创建在 Azure 门户中选择的新 Azure AD B2C 租户](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. 输入**组织名称**和**初始域名**。 选择**国家或地区**（以后不能更改），然后选择 "**创建**"。

    域名用作完整租户域名的一部分。 在此示例中，租户名称为*contosob2c.onmicrosoft.com*：

    ![在中创建租户窗体，其中包含示例值 Azure 门户](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. 租户创建完成后，选择 "租户创建" 页顶部的 "**创建新的 B2C 租户" 或 "链接到现有租户**" 链接。

    ![Azure 门户中突出显示的链接租户痕迹链接](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. 选择 **"将现有 Azure AD B2C 租户链接到我的 Azure 订阅"** 。

   ![在 Azure 门户中链接现有订阅选择](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. 选择您创建的**Azure AD B2C 租户**，然后选择您的**订阅**。

    对于“资源组”，选择“新建”。 输入将包含租户的资源组的**名称**，选择**资源组位置**，然后选择 "**创建**"。

    ![Azure 门户中的链接订阅设置窗体](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

    出于计费目的，可以将多个 Azure AD B2C 租户链接到单个 Azure 订阅。

## <a name="select-your-b2c-tenant-directory"></a>选择 B2C 租户目录

若要开始使用新的 Azure AD B2C 租户，需要切换到包含该租户的目录。

选择 Azure 门户顶部菜单中的 "**目录 + 订阅**" 筛选器，然后选择包含 Azure AD B2C 租户的目录。

如果第一次在列表中看不到新的 Azure B2C 租户，请刷新浏览器窗口，然后在顶部菜单中再次选择 "**目录 + 订阅**筛选器"。

![B2C 租户-包含在 Azure 门户中选择的目录](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>将 Azure AD B2C 添加为收藏项（可选）

此可选步骤使你可以更轻松地选择以下和所有后续教程中的 Azure AD B2C 租户。

无需在每次使用租户时搜索**所有服务**中的*Azure AD B2C* ，只需将资源收藏即可。 然后，可以从门户菜单的 **"收藏夹**" 部分选择它，以快速浏览到 Azure AD B2C 租户。

只需执行此操作一次。 执行这些步骤之前，请确保已切换到包含 Azure AD B2C 租户的目录，如前一部分中所述，[选择 B2C 租户目录](#select-your-b2c-tenant-directory)。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在 "Azure 门户" 菜单中，选择 "**所有服务**"。
1. 在 "**所有服务**" 搜索框中，搜索 " **Azure AD B2C**"，将鼠标悬停在搜索结果上，然后在工具提示中选择星形图标。 **Azure AD B2C**现在出现在 "**收藏夹**" 下的 Azure 门户中。
1. 如果要更改新收藏的位置，请转到 "Azure 门户" 菜单，选择 " **Azure AD B2C**"，然后将其上移或下移到所需的位置。

    ![Azure AD B2C、收藏夹菜单 Microsoft Azure 门户](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 创建 Azure AD B2C 租户
> * 将租户链接到订阅
> * 切换到包含 Azure AD B2C 租户的目录
> * 将 Azure AD B2C 资源添加为 Azure 门户中的**收藏**项

接下来，了解如何在新租户中注册 web 应用程序。

> [!div class="nextstepaction"]
> [注册应用程序 >](tutorial-register-applications.md)
