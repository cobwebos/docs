---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 57368331e83111836e13b6c3796cacad9816e318
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521289"
---
若要从 Azure 门户查看和复制存储帐户访问密钥或连接字符串，请执行以下操作：

1. 导航到[Azure 门户](https://portal.azure.com)。
2. 找到自己的存储帐户。
3. 在 **"设置"** 下，选择 **"访问键**"。 此时会显示帐户访问密钥，以及每个密钥的完整连接字符串。
4. 找到“key1”下面的“密钥”值，单击“复制”按钮复制该帐户密钥。************
5. 或者，可复制整个连接字符串。 找到“密钥 1”下面的“连接字符串”值，单击“复制”按钮复制该连接字符串。************

    ![显示如何在 Azure 门户中查看访问密钥的屏幕截图](media/storage-view-keys-include/portal-connection-string.png)

可以使用任一密钥来访问 Azure 存储，但通常情况下，使用第一个密钥是很好的做法，并将第二个密钥保留到轮换密钥时使用。

要查看或读取帐户的访问密钥，用户必须是服务管理员，或者必须分配包含**Microsoft.存储/存储帐户/列表键/操作**的 RBAC 角色。 包含此操作的某些内置 RBAC 角色是**所有者**、**参与者**和**存储帐户密钥操作员服务角色**。 有关服务管理员角色的详细信息，请参阅[经典订阅管理员角色、Azure RBAC 角色和 Azure AD 角色](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)。 有关 Azure 存储的内置角色的详细信息，请参阅[Azure RBAC 的 Azure 内置角色中的](../articles/role-based-access-control/built-in-roles.md#storage)**存储**部分。
