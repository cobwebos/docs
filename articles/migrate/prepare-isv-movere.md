---
title: 准备 Azure Migrate 来使用 ISV 工具/Movere
description: 本文介绍了如何准备 Azure Migrate 以使用 ISV 工具或 Movere，然后介绍如何开始使用该工具。
ms.topic: how-to
ms.date: 06/10/2020
ms.openlocfilehash: ed7652cb34705bac56a79b5c30e6bda3dac69af0
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86103919"
---
# <a name="prepare-to-work-with-an-isv-tool-or-movere"></a>准备使用 ISV 工具或 Movere

如果已将 [ISV 工具](migrate-services-overview.md#isv-integration) 或 Movere 添加到 Azure Migrate 项目中，则请在链接该工具并将数据发送到 Azure Migrate 之前执行几个步骤。 

## <a name="check-azure-ad-permissions"></a>检查 Azure AD 权限

你的 Azure 用户帐户需要以下权限：

- 可使用 Azure 租户注册 Azure Active Directory (Azure AD) 应用的权限
- 可在订阅级别将角色分配给 Azure AD 应用的权限


### <a name="set-permissions-to-register-an-azure-ad-app"></a>设置权限来注册 Azure AD 应用

1. 在 Azure AD 中，查看你的帐户的角色。
2. 如果你有用户角色，请在左侧选择“用户设置”，并验证用户是否可注册应用程序。 如果设置为“是”，则 Active AD 租户中的任何用户都可注册应用。 如果设置为“否”，则只有管理员用户可注册应用。   
3. 如果你没有权限，则管理员用户可为你的用户帐户提供[应用程序管理员](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-administrator)角色，以便你可注册应用。
4. 将该工具链接到 Azure Migrate 后，管理员可从你的帐户中删除该角色。

### <a name="set-permissions-to-assign-a-role-to-an-azure-ad-app"></a>设置权限来将角色分配给 Azure AD 应用
 
要将角色分配给 Azure AD 应用，你的帐户需要在 Azure 订阅中拥有 Microsoft.Authorization/*/Write 访问权限。 

1. 在 Azure 门户中，打开 **订阅**。
2. 选择相关订阅。 如果看不到它，请选择“全局订阅筛选器”。 
3. 选择“我的权限”。 然后，选择“单击此处查看此订阅的完整访问详细信息”。
4. 在“角色分配” > “视图中查看权限” 。 如果你的帐户没有权限，请让订阅管理员将你添加到[用户访问管理员](../role-based-access-control/built-in-roles.md#user-access-administrator)角色和[所有者](../role-based-access-control/built-in-roles.md#owner)角色。

## <a name="allow-access-to-urls"></a>允许访问 URL

对于 ISV 工具和 Azure 数据库迁移助手，允许访问表中汇总的公有云 Url。 如果使用基于 URL 的代理连接到 Internet，请确保代理在查找 URL 时会解析接收到的任何 CNAME 记录。 

**URL** | **详细信息**
--- | ---
*.portal.azure.com  | 导航到 Azure 门户。 
*.windows.net<br/> *.msftauth.net<br/> *.msauth.net <br/> *.microsoft.com<br/> *.live.com   | 登录到 Azure 订阅。 
*.microsoftonline.com<br/> *.microsoftonline-p.com | 为设备创建 Azure Active Directory (AD) 应用，以便与 Azure Migrate 通信。 
management.azure.com | 对 Azure Migrate 项目进行 Azure 资源管理器调用。
*.servicebus.windows.net | 设备与 EventHub 之间用于发送消息的通信。


## <a name="start-using-the-tool"></a>开始使用该工具

1. 如果你还没有该工具的许可证或免费试用版，请在 Azure Migrate 的工具项中，选择“注册”下的“了解详细信息” 。
2. 在工具中，按照说明从工具链接到 Azure Migrate 项目，或者将数据发送到 Azure Migrate。

## <a name="next-steps"></a>后续步骤

按照 ISV 或 Movere 中的说明将数据发送到 Azure Migrate。

   
