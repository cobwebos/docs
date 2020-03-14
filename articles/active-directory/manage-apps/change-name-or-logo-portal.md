---
title: 在 Azure AD 中更改企业应用程序的名称或徽标
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
ms.openlocfilehash: d23849df75d1ab239eb269b462abb21df196e7e5
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79138495"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>在 Azure Active Directory 中更改企业应用程序的名称或徽标

在 Azure Active Directory (Azure AD) 中更改自定义企业应用程序的名称或徽标很容易。 您必须具有相应的权限才能进行这些更改，您必须是自定义应用程序的创建者。

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>如何实现更改企业应用程序的名称或徽标？

1. 使用目录的全局管理员帐户登录到[Azure Active Directory 门户](https://aad.portal.azure.com/)。 此时将显示 " **Azure Active Directory 管理中心**" 页。
2. 在左窗格中，选择“企业应用程序”。 此时将显示企业应用程序的列表。
3. 选择一个应用程序。 此时将显示 "应用程序概述" 页。
4. 在 "应用程序概述" 窗格中的 "**管理**" 标题下，选择 "**属性**"。 此时将显示 "**属性**" 页。
5. 如果要更改名称，请选择 "**名称**" 框，键入新名称，然后按**enter**。
6. 如果要更改徽标，请找到**徽标**字段，然后选择 "**选择文件**" 框旁的文件夹图标，该图标位于应用程序的当前徽标图像下方。

   ![选择属性命令](./media/change-name-or-logo-portal/change-logo.png)

   否则，如果不更改徽标，请跳到步骤8。
7. 在文件选取器中，选择要用作新徽标的文件。 文件的名称将显示在当前徽标图像下方的框中。

   > [!NOTE]
   > Azure 要求徽标图像为 PNG 文件，并应用宽度、高度和文件大小的限制。 自定义徽标的大小必须为 215 &times; 215 像素且大小为 PNG 格式。 建议你在应用程序徽标中使用纯色背景，无透明度，以最大程度显示给用户。
8. 选择“保存”。 如果选择了新徽标，**徽标**字段的图像将更改以反映新的徽标文件。

## <a name="next-steps"></a>后续步骤

* [快速入门：在 Azure Active Directory 中查看组织的组和成员](../fundamentals/active-directory-groups-view-azure-portal.md)
* [向企业应用分配用户或组](assign-user-or-group-access-portal.md)
* [删除企业应用的用户或组分配](remove-user-or-group-access-portal.md)
* [禁用企业应用的用户登录](disable-user-sign-in-portal.md)
