---
title: 更改名称或徽标的 Azure Active Directory 中企业应用程序 |Microsoft Docs
description: 如何更改名称或徽标在 Azure Active Directory 中的自定义企业应用程序
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
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780927"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>更改名称或徽标的 Azure Active Directory 中企业应用程序

在 Azure Active Directory (Azure AD) 中更改自定义企业应用程序的名称或徽标很容易。 必须具有适当的权限来进行这些更改，并且您必须是自定义应用程序的创建者。

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>如何更改企业应用程序的名称或徽标？

1. 登录到[Azure Active Directory 门户](https://aad.portal.azure.com/)目录的全局管理员的帐户。 **Azure Active Directory 管理中心**页将出现。
2. 在左窗格中，选择“企业应用程序”。 显示企业应用程序的列表。
3. 选择一个应用程序。 将显示应用程序概述页。
4. 在应用程序概述窗格中下,**管理**标题下方，选择**属性**。 **属性**页将出现。
5. 如果你想要更改名称，选择**名称**框中，键入新名称，然后按**Enter**。
6. 如果你想要更改的徽标，找到**徽标**字段，然后选择文件夹图标旁**选择一个文件**框中，这低于应用程序的当前徽标图像。

   ![选择属性命令](./media/change-name-or-logo-portal/change-logo.png)

   否则，如果不更改徽标，请转到步骤 8。
7. 在文件选取器中，选择作为新徽标的所需的文件。 当前的徽标图像下方的框中显示的文件的名称。

   > [!NOTE]
   > Azure 需要徽标图像是 PNG 文件，并且它应用上的宽度、 高度和文件大小限制。
8. 选择“保存”。 如果选择了新徽标**徽标**字段的图像发生更改以反映新的徽标文件。

## <a name="next-steps"></a>后续步骤

* [快速入门：在 Azure Active Directory 中查看你的组织组和成员](../fundamentals/active-directory-groups-view-azure-portal.md)
* [向企业应用分配用户或组](assign-user-or-group-access-portal.md)
* [删除企业应用的用户或组分配](remove-user-or-group-access-portal.md)
* [Disable user sign-ins for an enterprise app](disable-user-sign-in-portal.md)
