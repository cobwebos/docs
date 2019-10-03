---
title: 教程 - 创建 Azure Active Directory B2C 租户
description: 了解如何通过使用 Azure 门户创建 Azure Active Directory B2C 租户来准备注册应用程序。
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: de29929c8fda476fe276f91d4a68ce4d632503fd
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345210"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>教程：创建 Azure Active Directory B2C 租户

必须先在你管理的租户中注册应用程序，然后才能与 Azure Active Directory B2C （Azure AD B2C）交互。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建 Azure AD B2C 租户
> * 将租户链接到订阅
> * 切换到包含 Azure AD B2C 租户的目录
> * 将 Azure AD B2C 资源添加为 Azure 门户中的*收藏*项

在下一个教程中，学习如何注册应用程序。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-azure-ad-b2c-tenant"></a>创建 Azure AD B2C 租户

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 请确保使用的是包含订阅的目录。

    在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含你的订阅的目录。 此目录不同于将包含 Azure AD B2C 租户的其他目录。

    ![选择了订阅租户的目录 + 订阅筛选器](media/tutorial-create-tenant/portal-01-select-directory.png)

1. 在 Azure 门户的左上角选择 "**创建资源**"。
1. 搜索并选择 " **Active Directory B2C**"，然后选择 "**创建**"。
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

## <a name="select-your-b2c-tenant-directory"></a>选择 B2C 租户目录

若要开始使用新的 Azure AD B2C 租户，需要切换到包含该租户的目录。

选择 Azure 门户顶部菜单中的 "**目录 + 订阅**" 筛选器，然后选择包含 Azure AD B2C 租户的目录。

如果第一次在列表中看不到新的 Azure B2C 租户，请刷新浏览器窗口，然后在顶部菜单中再次选择 "**目录 + 订阅**筛选器"。

![B2C 租户-包含在 Azure 门户中选择的目录](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>将 Azure AD B2C 添加为收藏项（可选）

此可选步骤使你可以更轻松地选择以下和所有后续教程中的 Azure AD B2C 租户。

不必在每次使用租户时在**所有服务**中搜索 "Azure AD B2C"，只需将资源收藏即可。 然后，可以从左侧的 **"收藏夹**" 菜单中选择它以快速浏览到 Azure AD B2C 租户。

只需执行此操作一次。 执行这些步骤之前，请确保已切换到包含 Azure AD B2C 租户的目录，如前一部分中所述，[选择 B2C 租户目录](#select-your-b2c-tenant-directory)。

1. 在[Azure 门户](https://portal.azure.com)的左侧菜单中选择 "**所有服务**"
1. 在 "搜索" 文本框中输入*Azure AD B2C*
1. 选择**星号**，将 Azure AD B2C 添加到收藏夹中
1. *Azure AD B2C*现在会出现在 "**收藏夹**" 左侧菜单中。 如果需要，你可以选择并在列表中将其拖到更高级别的位置，如下图所示：

![将 Azure AD B2C 添加到 Azure 门户中收藏夹的步骤](media/tutorial-create-tenant/portal-08-favorite-b2c.png)

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 创建 Azure AD B2C 租户
> * 将租户链接到订阅
> * 切换到包含 Azure AD B2C 租户的目录
> * 将 Azure AD B2C 资源添加为 Azure 门户中的*收藏*项

接下来，了解如何在新租户中注册 Web 应用程序。

> [!div class="nextstepaction"]
> [注册应用程序 >](tutorial-register-applications.md)
