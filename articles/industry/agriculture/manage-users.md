---
title: 管理用户
description: 本文介绍如何管理用户。
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: f58db00e598fd08972adb60cbd8aae0d3bf3391d
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74942287"
---
# <a name="manage-users"></a>管理用户

Azure FarmBeats 包括 Azure Active Directory （Azure AD）中的人员的用户管理。 你可以将用户添加到你的 Azure FarmBeats 实例，以从场中访问 Api、查看生成的映射以及传感器遥测。

## <a name="prerequisites"></a>必备组件

- 需要 Azure FarmBeats 部署。 若要了解有关如何设置 Azure FarmBeats 的详细信息，请参阅[部署 FarmBeats](prepare-for-deployment.md) 。
- 要在 Azure FarmBeats 实例中添加或删除的用户的电子邮件 ID。

## <a name="manage-azure-farmbeats-users"></a>管理 Azure FarmBeats 用户

Azure FarmBeats 使用 Azure AD 进行身份验证、访问控制和角色。 可以将 Azure AD 租户中的用户添加为 Azure FarmBeats 中的用户。

> [!NOTE]
> 如果尝试添加的用户在 Azure AD 租户中不存在，请按照**添加 Azure AD 用户**部分中的说明完成设置，然后再继续将其设置为 Azure FarmBeats 用户。

**角色**

目前，Azure FarmBeats 支持两种用户角色：

 - **管理员**-对 Azure FarmBeats 数据中心 api 的所有访问权限。 此角色中的用户可以查询所有 Azure FarmBeats 数据中心对象，并从 FarmBeats 加速器执行所有操作。
 - **只读**-对 FarmBeats 数据中心 api 的只读访问。 用户可以查看数据中心 Api、加速器仪表板和映射。 具有 "只读" 角色的用户将不能执行任何操作，例如生成映射、关联设备或创建场。


## <a name="add-user-to-azure-farmbeats"></a>将用户添加到 Azure FarmBeats

按照以下步骤将用户添加到 Azure FarmBeats：

1.  登录到加速器，然后选择 "**设置**" 图标。
2.  选择 "**访问控制**"。

    ![项目场节拍](./media/create-farms/settings-users-1.png)

3.  输入要为其提供访问权限的用户的电子邮件 ID。
4.  选择所需的角色-管理员或只读。
5.  选择 "**添加角色**"。

添加的用户现在将能够访问 Azure FarmBeats （数据中心和加速器）。

## <a name="delete-user-from-azure-farmbeats"></a>从 Azure FarmBeats 删除用户

按照以下步骤从 Azure FarmBeats 系统中删除用户：

1.  登录到加速器，然后选择 "**设置**" 图标。
2.  选择 "**访问控制**"。
3.  选择 "**删除**" 以删除用户。

将从系统中删除该用户。 你将收到以下消息，用于确认操作是否成功。

![项目场节拍](./media/create-farms/manage-users-2.png)

## <a name="add-azure-ad-users"></a>添加 Azure AD 用户

> [!NOTE]
> 如果 Azure AD 租户中不存在用户，请按照以下步骤向 Azure FarmBeats 提供用户访问权限。 如果用户存在于 Azure AD 租户中，则可以跳过以下步骤。
>

FarmBeats 用户需要存在于 Azure AD 租户中，然后才能将其分配给应用程序和角色。 若要向 Azure AD 添加用户，请执行以下步骤：

1.  登录到 [Azure 门户](https://portal.azure.com/)。
2.  在右上角选择你的帐户，并切换到与 FarmBeats 关联的 Azure AD 租户。
3.  选择“Azure Active Directory”>“用户”。
    此时会看到目录中用户的列表。
4.  若要向目录添加用户，请选择“新用户”。 对于外部用户，请选择“新来宾用户”。

    ![项目场节拍](./media/create-farms/manage-users-3.png)

5.  完成新用户的必填字段。 选择**创建**。

若要更详细地了解如何管理 Azure AD 中的用户，请访问 [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/) 文档。

## <a name="next-steps"></a>后续步骤

已成功将用户添加到 Azure FarmBeats 实例。 现在，了解如何[创建场](manage-farms.md#create-farms)。
