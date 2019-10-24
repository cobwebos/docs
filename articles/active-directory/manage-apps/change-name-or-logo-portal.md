---
title: 在 Azure Active Directory 中更改企业应用程序的名称或徽标 | Microsoft Docs
description: 如何在 Azure Active Directory 中更改自定义企业应用程序的名称或徽标
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30da8d6843c27c42d4d99adef50b9ad98a131c95
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65780927"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>在 Azure Active Directory 中更改企业应用程序的名称或徽标

在 Azure Active Directory (Azure AD) 中更改自定义企业应用程序的名称或徽标很容易。 必须具有适当的权限才能进行这些更改，并且必须是自定义应用程序的创建者。

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>如何更改企业应用程序的名称或徽标？

1. 使用属于目录全局管理员的帐户登录到 [Azure Active Directory 门户](https://aad.portal.azure.com/)。 此时会显示“Azure Active Directory 管理中心”页。
2. 在左窗格中，选择“企业应用程序”。 此时会显示企业应用程序的列表。
3. 选择一个应用程序。 此时会显示应用程序概述页。
4. 在应用程序概述窗格中的“管理”标题下，选择“属性”。 此时会显示“属性”页。
5. 若要更改名称，请选择“名称”框，键入新名称，然后按 **Enter**。
6. 若要更改徽标，请找到“徽标”字段，然后选择应用程序当前徽标图像下方的“选择文件”框旁边的文件夹图标。

   ![选择属性命令](./media/change-name-or-logo-portal/change-logo.png)

   如果不想要更改徽标，请转到步骤 8。
7. 在文件选取器中，选择要用作新徽标的文件。 该文件的名称将显示在当前徽标图像下方的框中。

   > [!NOTE]
   > Azure 要求徽标图像采用 PNG 文件，并且对宽度、高度和文件大小施加了限制。
8. 选择“保存”。 如果选择新徽标，“徽标”字段的图像将会更改，以反映新徽标文件。

## <a name="next-steps"></a>后续步骤

* [快速入门：在 Azure Active Directory 中查看组织的组和成员](../fundamentals/active-directory-groups-view-azure-portal.md)
* [向企业应用分配用户或组](assign-user-or-group-access-portal.md)
* [删除企业应用的用户或组分配](remove-user-or-group-access-portal.md)
* [Disable user sign-ins for an enterprise app](disable-user-sign-in-portal.md)
