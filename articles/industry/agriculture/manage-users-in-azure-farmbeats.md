---
title: 管理 Azure 服务器场中的用户
description: 本文介绍如何在 Azure FarmBeats 中管理用户。
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 6ccc29422c6abf1120d13c05d10125efd0871ca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502062"
---
# <a name="manage-users"></a>管理用户

Azure FarmBeats 包括 Azure 活动目录 （Azure AD） 实例一部分的用户的用户管理。 您可以将用户添加到 Azure FarmBeats 实例以访问 API、查看生成的地图以及从服务器场访问传感器遥测。

## <a name="prerequisites"></a>先决条件

- 需要安装 Azure 服务器场节拍。 有关详细信息，请参阅安装[Azure 服务器场节拍](install-azure-farmbeats.md)。
- 要从 Azure FarmBeats 实例中添加或删除的用户的电子邮件标识。

## <a name="manage-azure-farmbeats-users"></a>管理 Azure 服务器场用户

Azure FarmBeats 使用 Azure AD 进行身份验证、访问控制和角色。 您可以将 Azure AD 租户中的用户添加为 Azure FarmBeats 中的用户。

> [!NOTE]
> 如果用户不是 Azure AD 租户用户，请按照 **"添加 Azure AD 用户"** 部分中的说明完成设置。

Azure 服务器场节拍支持两种类型的用户角色：

 - **管理员**：完全访问 Azure 服务器场数据数据集 API。 此角色中的用户可以查询所有 Azure FarmBeats Datahub 对象，并从服务器场节拍加速器执行所有操作。
 - **只读**：对服务器场节拍数据集 API 的只读访问。 用户可以查看 Datahub API、加速器仪表板和地图。 具有只读访问权限的用户无法执行生成地图、关联设备或创建服务器场等操作。

## <a name="add-users-to-azure-farmbeats"></a>将用户添加到 Azure 服务器场节拍

要将用户添加到 Azure 服务器场节拍：

1. 登录到加速器，然后选择 **"设置"** 图标。
2. 选择**访问控制**。

    !["服务器场设置"窗格](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. 输入要授予访问权限的用户的电子邮件 ID。
4. 选择所需的角色、**管理员**或**只读**。
5. 选择 **"添加角色**"。

添加的用户现在可以访问 Azure FarmBeats（Datahub 和加速器）。

## <a name="delete-users-from-azure-farmbeats"></a>从 Azure 服务器场删除用户

要从 Azure 服务器场节拍系统中删除用户，请执行以下准备：

1. 登录到加速器，然后选择 **"设置"** 图标。
2. 选择**访问控制**。
3. 选择 **"删除**"。

   用户将从系统中删除。 您将收到以下确认消息：

   ![Azure 农场节拍确认消息](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>添加 Azure AD 用户

> [!NOTE]
> Azure FarmBeats 用户需要存在于 Azure AD 租户中，然后才能将其分配给应用程序和角色。 如果用户在 Azure AD 租户中不存在，请按照本节中的说明操作。 如果 Azure AD 租户中已存在用户，请跳过说明。

按照步骤将用户添加到 Azure AD：

1. 登录到 Azure[门户](https://portal.azure.com/)。
2. 在右上角，选择您的帐户，然后切换到与 FarmBeats 关联的 Azure AD 租户。
3. 选择**Azure 活动目录** > **用户**。

    将显示 Azure AD 用户的列表。

4. 要将用户添加到目录，请选择 **"新建用户**"。 要添加外部用户，请选择 **"新建来宾用户**"。

    !["所有用户"窗格](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. 选择新用户的名称，然后完成该用户所需的字段。
6. 选择 **“创建”**。

有关管理 Azure AD 用户的信息，请参阅[在 Azure AD 中添加或删除用户](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/)。

## <a name="next-steps"></a>后续步骤

您已成功将用户添加到 Azure FarmBeats 实例。 现在，学习如何[创建和管理农场](manage-farms-in-azure-farmbeats.md#create-farms)。
