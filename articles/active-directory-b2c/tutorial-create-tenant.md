---
title: 教程 - 创建 Azure Active Directory B2C 租户 | Microsoft Docs
description: 了解如何通过使用 Azure 门户创建 Azure Active Directory B2C 租户来准备注册应用程序。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 06/19/2018
ms.author: davidmu
ms.openlocfilehash: 04f3dbbe461bfe0f07b6930a92bdd8a721e55098
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296784"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>教程：创建 Azure Active Directory B2C 租户

必须在你管理的租户中注册应用程序，然后这些应用程序才能与 Azure Active Directory (Azure AD) B2C 交互。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建 Azure AD B2C 租户
> * 将租户链接到订阅

在下一个教程中，学习如何注册应用程序。

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-an-azure-ad-b2c-tenant"></a>创建 Azure AD B2C 租户

1. 在 Azure 门户的左上角，选择“创建资源”。
2. 在 Azure 市场资源列表上方的搜索框中，搜索并选择“Active Directory B2C”，然后单击“创建”。
3. 选择“新建 Azure AD B2C 租户”，输入租户名称中使用的组织名称和初始域名，选择国家/地区，然后单击“创建”。 请确保租户的国家/地区选择正确，因为以后不能更改。

    ![创建租户](./media/tutorial-create-tenant/create-tenant.png)

    在此示例中，租户名称是 contoso0522Tenant.onmicrosoft.com

若要开始管理新租户，请单击“此处”一词，即会显示“单击此处以管理新目录”。 你将看到“故障排除”消息，指出你需要将订阅链接到新租户。 

## <a name="link-your-tenant-to-your-subscription"></a>将租户链接到订阅

需要将 Azure AD B2C 租户链接到 Azure 订阅以启用所有功能，并支付使用费。 如果不将租户链接到订阅，应用程序将无法正常工作。

1. 通过切换 Azure 门户右上角的目录，确保使用的是包含要与新租户关联的订阅的目录。

    ![切换目录](./media/tutorial-create-tenant/switch-directories.png)

    然后选择包含订阅的目录。

    ![选择目录](./media/tutorial-create-tenant/select-directory.png)

2. 在 Azure 门户的左上角，选择“创建资源”。
3. 在 Azure 市场资源列表上方的搜索框中，搜索并选择“Active Directory B2C”，然后单击“创建”。
4. 选择“将现有 Azure AD B2C 租户链接到 Azure 订阅”，选择已创建的租户，选择订阅，输入 *myContosoTenantRG* 作为资源组名称，接受位置，然后单击“创建”。

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 创建 Azure AD B2C 租户
> * 将租户链接到订阅

> [!div class="nextstepaction"]
> [使 Web 应用程序能够使用帐户进行身份验证](active-directory-b2c-tutorials-web-app.md)