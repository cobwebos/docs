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
ms.openlocfilehash: 8418a45c9eba596339999c2bddf937a516d3e2d9
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223340"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>快速入门：在 Azure Active Directory (Azure AD) 租户中配置应用程序的属性

在前面的快速入门中，你已将一个应用程序添加到 Azure Active Directory (Azure AD) 租户。 添加应用程序时，需让你的 Azure AD 租户知道它是该应用的标识提供者。 现在，你将配置应用程序的一些属性。
 
## <a name="prerequisites"></a>先决条件

要在 Azure AD 租户中配置应用程序的属性，你需要：

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 以下角色之一：全局管理员、云应用程序管理员、应用程序管理员或服务主体的所有者。
- 可选：完成[查看应用](view-applications-portal.md)。
- 可选：完成[添加应用](add-application-portal.md)。

>[!IMPORTANT]
>使用非生产性环境测试本快速入门中的步骤。

## <a name="configure-app-properties"></a>配置应用属性

将应用程序添加到 Azure AD 租户后，“概述”页便会显示。 如果要配置已添加的应用程序，请查看第一个快速入门。 它将引导你查看已添加到租户的应用程序。 

编辑应用程序属性：

1. 在 Azure AD 门户中，选择“企业应用程序”。 然后找到并选择要配置的应用程序。
1. 在“管理”部分中，选择“属性”以打开“属性”窗格进行编辑  。

    ![“属性”屏幕的屏幕截图，其中显示可编辑的应用属性。](media/add-application-portal/edit-properties.png)

1. 请花点时间了解可用于配置的选项：
    - “是否启用以供用户登录?”决定分配到应用程序的用户能否登录。
    - “是否需要进行用户分配?”决定了未分配到应用程序的用户能否登录。
    - “是否对用户可见?”决定分配到应用的用户能否在[访问面板](https://myapps.microsoft.com)和 Office 365 应用启动器中看到该应用。 （请参阅 Office 365 或 Microsoft 365 网站左上角的华夫饼菜单。）
1. 参考下表选择最符合需求的选项。

   - *已分配*用户的行为：

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

   - *未分配*用户的行为：

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

1. 创建一个 215 x 215 像素的徽标，将其保存为 .png 格式。
1. 在 Azure AD 门户中，选择“企业应用程序”。 然后找到并选择要配置的应用程序。
1. 在“管理”部分中，选择“属性”以打开“属性”窗格进行编辑  。 
1. 选择图标以上传徽标。
1. 完成后，选择“保存”。

    ![显示如何更改徽标的“属性”屏幕的屏幕截图。](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > 此“属性”窗格中显示的缩略图不会立即更新。 可以关闭并重新打开“属性”窗格以查看更新的图标。

## <a name="next-steps"></a>后续步骤

配置好应用程序的属性之后，即可继续设置单一登录。

- [设置单一登录](add-application-portal-setup-sso.md)
- [删除应用](delete-application-portal.md)