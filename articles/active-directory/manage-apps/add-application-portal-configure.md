---
title: 快速入门：在 Azure Active Directory (Azure AD) 租户中配置应用程序的属性
description: 本快速入门使用 Azure 门户配置已注册到 Azure Active Directory (Azure AD) 租户的应用程序。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82a3310f6fc2169a515b4b13d81c88d187bd0f9c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956146"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>快速入门：在 Azure Active Directory (Azure AD) 租户中配置应用程序的属性

在前面的快速入门中，你已将一个应用程序添加到 Azure AD 租户。 添加应用程序时，需让你的 Azure AD 租户知道它是该应用程序的标识提供者。 现在，你将配置应用程序的一些属性。
 
## <a name="prerequisites"></a>先决条件

要在 Azure AD 租户中配置应用程序的属性，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。
- （可选：完成[查看应用](view-applications-portal.md)）。
- （可选：完成[添加应用](add-application-portal.md)）。

>[!IMPORTANT]
>建议使用非生产环境来测试此快速入门中的步骤。

## <a name="configure-app-properties"></a>配置应用属性

完成向 Azure AD 租户添加应用程序后，便会立即显示它的概述页面。 如果你正在配置已添加的应用程序，请查看第一个快速入门，它将引导你查看添加到租户中的应用程序。 

编辑应用程序属性：

1. 在 Azure AD 门户中，选择“企业应用程序”，然后找到并选择要配置的应用程序。
2. 在“管理”部分中，选择“属性”，以打开属性页进行编辑。
    ![显示“属性”屏幕和可编辑的应用属性](media/add-application-portal/edit-properties.png)
3. 请花点时间了解可用于配置的选项。
    - “是否启用以供用户登录?”决定分配到应用程序的用户能否登录。
    - “是否需要进行用户分配?”决定了未分配到应用程序的用户能否登录。
    - “是否对用户可见?”确定分配给应用的用户是否可以在访问面板 (https://myapps.microsoft.com) ) 和 O365 应用启动器（Office 365 或 Microsoft 365 网站左上角的华夫饼菜单）中查看该应用。
4. 参考下表选择最符合需求的选项。

   - **已分配**用户的行为：

       | 应用程序属性 | 应用程序属性 | 应用程序属性 | 已分配用户的体验 | 已分配用户的体验 |
       |---|---|---|---|---|
       | 启用以供用户登录? | 需要进行用户分配? | 对用户可见? | 已分配用户能否登录? | 已分配用户能否看到应用程序?* |
       | 是 | 是 | 是 | 是 | 是  |
       | 是 | 是 | 否  | 是 | 否   |
       | 是 | 否  | 是 | 是 | 是  |
       | 是 | 否  | 否  | 是 | 否   |
       | 否  | 是 | 是 | 否  | 否   |
       | 否  | 是 | 否  | 否  | 否   |
       | 否  | 否  | 是 | 否  | 否   |
       | 否  | 否  | 否  | 否  | 否   |

   - **未分配**用户的行为：

       | 应用程序属性 | 应用程序属性 | 应用程序属性 | 未分配用户的体验 | 未分配用户的体验 |
       |---|---|---|---|---|
       | 启用以供用户登录? | 需要进行用户分配? | 对用户可见? | 未分配用户能否登录? | 未分配用户能否看到应用程序?* |
       | 是 | 是 | 是 | 否  | 否   |
       | 是 | 是 | 否  | 否  | 否   |
       | 是 | 否  | 是 | 是 | 否   |
       | 是 | 否  | 否  | 是 | 否   |
       | 否  | 是 | 是 | 否  | 否   |
       | 否  | 是 | 否  | 否  | 否   |
       | 否  | 否  | 是 | 否  | 否   |
       | 否  | 否  | 否  | 否  | 否   |

     *用户能否在访问面板和 Office 365 应用启动器中看到应用程序?

## <a name="use-a-custom-logo"></a>使用自定义徽标

若要使用自定义徽标，请执行以下操作：

1. 创建一个 215 x 215 像素的徽标，将其保存为 PNG 格式。
2. 在 Azure AD 门户中，选择“企业应用程序”，然后找到并选择要配置的应用程序。
3. 在“管理”部分中，选择“属性”，以打开属性页进行编辑。 
4. 选择图标以上传徽标。
5. 完成后，选择“保存”。 
    ![演示如何从应用的“属性”页更改徽标](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > 此“属性”窗格中显示的缩略图不会立即更新。 可以关闭并重新打开属性以查看更新的图标。

## <a name="next-steps"></a>后续步骤

配置好应用程序的属性之后，即可继续设置单一登录。

- [设置单一登录](add-application-portal-setup-sso.md)
- [删除应用](delete-application-portal.md)