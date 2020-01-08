---
title: 在 Azure FarmBeats 中管理用户
description: 本文介绍如何在 Azure FarmBeats 中管理用户。
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 208a302a0702a5c4de4d194c9e72f562aaf758c4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482291"
---
# <a name="manage-users"></a>管理用户

Azure FarmBeats 为属于 Azure Active Directory （Azure AD）实例的人员提供用户管理。 你可以将用户添加到你的 Azure FarmBeats 实例，以访问 Api、查看生成的映射以及从场访问传感器遥测。

## <a name="prerequisites"></a>必备组件

- 需要安装 Azure FarmBeats。 有关详细信息，请参阅[Install Azure FarmBeats](install-azure-farmbeats.md)。
- 要在 Azure FarmBeats 实例中添加或删除的用户的电子邮件 Id。

## <a name="manage-azure-farmbeats-users"></a>管理 Azure FarmBeats 用户

Azure FarmBeats 使用 Azure AD 进行身份验证、访问控制和角色。 可以将 Azure AD 租户中的用户添加为 Azure FarmBeats 中的用户。

> [!NOTE]
> 如果尝试添加为 Azure FarmBeats 用户的用户在 Azure AD 租户中不存在，请按照 "添加 Azure AD 用户" 部分中的说明完成安装。

Azure FarmBeats 支持两种类型的用户角色：

 - **管理员**：对 Azure FarmBeats Datahub api 的完全访问权限。 此角色中的用户可以查询所有 Azure FarmBeats Datahub 对象，并从 FarmBeats 加速器执行所有操作。
 - **只读**：对 FarmBeats Datahub api 的只读访问。 用户可以查看 Datahub Api、加速器仪表板和映射。 具有只读访问权限的用户无法执行诸如生成地图、关联设备或创建场等操作。

## <a name="add-users-to-azure-farmbeats"></a>将用户添加到 Azure FarmBeats

若要将用户添加到 Azure FarmBeats：

1. 登录到 "加速器"，然后选择 "**设置**" 图标。
2. 选择 "**访问控制**"。

    !["场设置" 窗格](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. 输入要向其授予访问权限的用户的电子邮件 ID。
4. 选择所需的角色 "**管理员**" 或 "**只读**"。
5. 选择 "**添加角色**"。

添加的用户现在可以访问 Azure FarmBeats （Datahub 和加速器）。

## <a name="delete-users-from-azure-farmbeats"></a>从 Azure FarmBeats 删除用户

若要从 Azure FarmBeats 系统中删除用户：

1. 登录到 "加速器"，然后选择 "**设置**" 图标。
2. 选择 "**访问控制**"。
3. 选择“删除”。

   将从系统中删除该用户。 你将收到以下确认消息：

   ![Azure FarmBeats 确认消息](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>添加 Azure AD 用户

> [!NOTE]
> Azure FarmBeats 用户需要存在于 Azure AD 租户中，然后才能将其分配给应用程序和角色。 如果 Azure AD 租户中不存在要添加到 Azure FarmBeats 的用户，请按照本部分中的说明进行操作。 如果 Azure AD 租户中存在该用户，则可以跳过这些说明。

若要将用户添加到 Azure AD，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 在右上角选择帐户，然后切换到与 FarmBeats 关联的 Azure AD 租户。
3. 选择 " **Azure Active Directory** > **用户**"。

    将显示 Azure AD 用户的列表。

4. 若要将用户添加到目录，请选择 "**新建用户**"。 若要添加外部用户，请选择 "**新建来宾用户**"。

    !["所有用户" 窗格](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. 选择新用户的名称，然后完成该用户的必填字段。
6. 选择“创建”。

有关管理 Azure AD 用户的信息，请参阅[在 Azure AD 中添加或删除用户](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/)。

## <a name="next-steps"></a>后续步骤

已成功将用户添加到 Azure FarmBeats 实例。 现在，了解如何[创建和管理场](manage-farms-in-azure-farmbeats.md#create-farms)。
