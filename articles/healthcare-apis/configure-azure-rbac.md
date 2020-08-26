---
title: 为用于 FHIR 的 Azure API 配置 azure RBAC) 的 Azure 基于角色的访问控制 (
description: 本文介绍如何为适用于 FHIR 数据平面的 Azure API 配置 Azure RBAC
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: matjazl
ms.reviewer: dseven
ms.openlocfilehash: c4a78168f2759f9d7bb9e50165b8cf03c71ecab5
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87845931"
---
# <a name="configure-azure-rbac-for-fhir"></a>配置 FHIR 的 Azure RBAC 

在本文中，你将了解如何使用 azure [RBAC)  (azure 基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/)来分配对 Azure API for FHIR 数据平面的访问权限。 当在与 Azure 订阅关联的 Azure Active Directory 租户中管理数据平面用户时，Azure RBAC 是用于分配数据平面访问权限的首选方法。 如果你使用的是外部 Azure Active Directory 租户，请参阅[本地 RBAC 赋值引用](configure-local-rbac.md)。

## <a name="confirm-azure-rbac-mode"></a>确认 Azure RBAC 模式

若要使用 Azure RBAC，你的 Azure API for FHIR 必须配置为使用 Azure 订阅租户进行数据平面，并且不应分配任何标识对象 Id。 可以通过检查 Azure API for FHIR 的**身份验证**边栏选项卡来验证你的设置：

:::image type="content" source="media/rbac/confirm-azure-rbac-mode.png" alt-text="确认 Azure RBAC 模式":::

应将该**颁发机构**设置为与您的订阅关联的 Azure Active directory 租户，并在标记为 "允许的**对象 id**" 的框中不应有任何 guid。 你还会注意到，此框处于禁用状态，并且标签指示应该使用 Azure RBAC 分配数据平面角色。

## <a name="assign-roles"></a>分配角色

若要授予用户、服务主体或组对 FHIR 数据平面的访问权限，请单击 " ** (IAM) 的" 访问控制**"，然后单击"**角色分配**"，然后单击" **+ 添加**"：

:::image type="content" source="media/rbac/add-azure-rbac-role-assignment.png" alt-text="添加 Azure 角色分配":::

在 "**角色**选择" 中，搜索 FHIR 数据平面的某个内置角色：

:::image type="content" source="media/rbac/built-in-fhir-data-roles.png" alt-text="内置 FHIR 数据角色":::

你可以选择：

* FHIR 数据读取器：可以读取 (和搜索) FHIR 数据。
* FHIR 数据编写器：可读取、写入和软删除 FHIR 数据。
* FHIR 数据导出程序：可以读取和导出 (`$export` 运算符) 数据。
* FHIR 数据参与者：可执行所有数据平面操作。

如果这些角色不足以满足你的需要，你还可以[创建自定义角色](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell)。

在 "**选择**" 框中，搜索要将角色分配到的用户、服务主体或组。

## <a name="caching-behavior"></a>缓存行为

适用于 FHIR 的 Azure API 会缓存最多5分钟的决策。 如果通过将 FHIR 服务器添加到允许的对象 Id 列表中来向用户授予对该服务器的访问权限，或者从列表中删除这些对象，则应预计该用户需要5分钟才能传播权限更改。

## <a name="next-steps"></a>后续步骤

本文介绍了如何为 FHIR 数据平面分配 Azure 角色。 接下来了解适用于 FHIR 的 Azure API 的其他设置：
 
>[!div class="nextstepaction"]
>[其他设置 Azure API for FHIR](azure-api-for-fhir-additional-settings.md)
