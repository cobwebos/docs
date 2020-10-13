---
title: 如何在 Azure AD 中使用自助服务应用程序访问
description: 启用自助服务，以便用户可以在 Azure AD 中查找应用
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere,asteen
ms.openlocfilehash: 0cd20d7a11bcffe9937537e3681199757a52bee5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89181708"
---
# <a name="how-to-use-self-service-application-access"></a>如何使用自助服务应用程序访问

你需要启用 **自助应用程序访问** 你希望允许用户自行发现并请求访问的任何应用程序，然后你的用户才可以从其 "我的应用" 页面自行发现应用程序。

对于 IT 组，此功能是节省时间和金钱的绝佳方式，我们强烈建议将其作为 Azure Active Directory 的新式应用程序部署的一部分。

若要了解如何从最终用户的角度使用我的应用，请参阅 [我的应用门户的帮助](../user-help/my-apps-portal-end-user-access.md)。

使用此功能，可以：

-   让用户从 [应用](https://myapps.microsoft.com/) 程序自行发现应用程序，而无需麻烦 IT 组。
-   将这些用户添加到预配置组，以便查看请求访问的用户、删除访问权限以及管理分配给用户的角色。
-   （可选）允许某人批准应用程序访问请求，使 IT 组不必这样做。
-   （可选）最多配置 10 个可以批准访问此应用程序的人员。
-   （可选）允许某人设置用户登录应用程序时可以使用的密码。
-   （可选）自动将自助服务分配的用户直接分配到应用程序角色。

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>启用自助服务应用程序访问以允许用户查找自己的应用程序

自助应用程序访问是帮助用户自己发现应用程序的绝佳方式，可选择性地允许业务组批准对这些应用程序的访问。 可以从 "我的应用" 页中，允许业务组管理分配给这些用户的密码 Single-Sign 的凭据。

若要启用应用程序的自助应用程序访问，请执行以下步骤：
1. 打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。
2. 通过选择主左侧导航菜单顶部的 "**所有服务**" 打开**Azure Active Directory 扩展**。
3. 在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。
4. 从 Azure Active Directory 左侧导航菜单中选择 " **企业应用程序** "。
5. 选择“所有应用程序”，查看所有应用程序的列表。
   * 如果未看到要在此处显示的应用程序，请使用“所有应用程序列表”顶部的“筛选器”控件，并将“显示”选项设置为“所有应用程序”。
6. 从列表中选择要对其启用自助访问的应用程序。
7. 加载应用程序后，在应用程序的左侧导航菜单中选择 " **自助服务** "。
8. 要对此应用程序启用自助应用程序访问，请将“允许用户请求对此应用程序的访问权限?”**** 切换到“是”****。
9. 接下来，若要选择要向其添加对此应用程序的访问权限的用户组，请选择 "应将 **用户添加到哪个组？** " 旁边的选择器，然后选择一个组。
10. **可选：** 如果希望获得业务批准之后才允许用户访问，请将“需要获得批准才能授权访问此应用程序?”**** 切换到“是”****。
11. **可选：对于仅使用密码单一登录的应用程序，** 如果要允许业务审批人为已批准的用户指定发送到此应用程序的密码，请将“允许审批人设置此应用程序的用户密码?”**** 切换到“是”****。
12. **可选：** 指定允许审批对此应用的访问权限的业务审批者。 选择 **允许谁批准对此应用程序的访问权限？** 然后，最多可选择10个单独的业务审批者。
    * 不支持组。
13. **可选：** **对于公开角色的应用程序**，如果想要将自助服务批准的用户分配给角色，请选择 " **此应用程序中应向哪个角色分配用户？** " 旁边的选择器，以选择应将这些用户分配到的角色。
14. 选择顶部的 " **保存** " 按钮以完成操作。

完成自助服务应用程序配置后，用户可以导航到 ["我的应用](https://myapps.microsoft.com/) "，然后选择 " **+ 添加** " 按钮以查找已启用自助访问的应用。 业务审批者还会在其 ["我的应用"](https://myapps.microsoft.com/) 页中看到通知。 可以启用电子邮件，在用户请求需要审批人批准的应用程序的访问权限时，向审批人发送电子邮件通知。 

这些批准仅支持单个审批工作流，意味着如果指定多个审批人，任何一个审批人都可以批准对该应用程序的访问。

## <a name="things-to-check-if-self-service-isnt-working"></a>若要检查自助服务是否工作不起作用
-   确保已让用户或组能够请求自助应用程序访问。
-   确保用户在进行自助应用程序访问时所访问的位置正确。 用户可以导航到 ["我的应用](https://myapps.microsoft.com/) " 页，然后选择 " **+ 添加** " 按钮以查找已启用自助访问的应用。
-   如果最近配置了自助应用程序访问，请在几分钟后尝试再次登录到用户的 "我的应用"，以查看是否显示了自助服务访问更改。

## <a name="next-steps"></a>后续步骤
[为自助组管理设置 Azure Active Directory](../users-groups-roles/groups-self-service-management.md)
