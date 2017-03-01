---
title: "使用 Azure 基于角色的访问控制 (RBAC) 控制用于创建和管理支持请求的访问权限 | Microsoft 文档"
description: "使用 Azure 基于角色的访问控制 (RBAC) 控制用于创建和管理支持请求的访问权限"
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
translationtype: Human Translation
ms.sourcegitcommit: d408dbd55fa667e150bb42ff0d1e4b3236c438e9
ms.openlocfilehash: 20ebd324cbf379980b43d255d468673de2b6d950


---

# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>使用 Azure 基于角色的访问控制 (RBAC) 控制用于创建和管理支持请求的访问权限

[基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) 可用于对 Azure 进行细致的访问管理。
Azure 门户（[portal.azure.com](https://portal.azure.com)）中的支持请求创建使用 Azure 的 RBAC 模型定义谁可以创建和管理支持请求。
访问权限是通过将相应的 RBAC 角色分配给特定范围（可以是订阅、资源组或资源）内的用户、组和应用程序来授予的。

让我们举个例子：作为在订阅范围内具有读取权限的资源组所有者，你可以管理资源组中的所有资源，如网站、虚拟机和子网。
但是，当你尝试针对虚拟机资源创建支持请求时，你遇到以下错误

![订阅错误](./media/create-manage-support-requests-using-access-control/subscription-error.png)

在支持请求管理中，你需要写入权限或可在订阅范围内执行支持操作 Microsoft.Support/* 的角色，才能创建和管理支持请求。

以下文章介绍如何在 Azure 门户中使用 Azure 的自定义基于角色的访问控制 (RBAC) 来创建和管理支持请求。

## <a name="getting-started"></a>入门

使用上面的示例，如果订阅所有者为你分配了订阅的自定义 RBAC 角色，你将能够针对资源创建支持请求。
可以使用 Azure PowerShell、Azure 命令行接口 (CLI) 和 REST API 创建[自定义 RBAC 角色](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/)。

自定义角色的 actions 属性指定该角色向其授予访问权限的 Azure 操作。
若要创建支持请求管理的自定义角色，角色必须可执行操作 Microsoft.Support/*

下面是可用于创建和管理支持请求的自定义角色的示例。
我们已将此角色命名为“支持请求参与者”，我们在本文中以此方式引用该自定义角色。

``` Json
{
    "Name":  "Support Request Contributor",
    "Id":  "1f2aad59-39b0-41da-b052-2fb070bd7942",
    "IsCustom":  true,
    "Description":  "Lets you create and manage support tickets.",
    "Actions":  [
                    "Microsoft.Support/*"
                ],
    "NotActions":  [
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

请按照[此视频](https://www.youtube.com/watch?v=-PaBaDmfwKI)中所述的步骤了解如何为订阅创建自定义角色。

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>在 Azure 门户中创建和管理支持请求

让我们举个例子 - 你是订阅“Visual Studio MSDN 订阅”的所有者。
Joe 是你的同事，他是此订阅中某些资源组的资源所有者，并对此订阅具有读取权限。
你想要为同事 Joe 提供访问权限，使其能够为此订阅下的资源创建和管理支持票证。

1. 第一步是转到此订阅，在“设置”下你将看到用户列表。 单击对此订阅有读者访问权限的用户 Joe，让我们为其分配新的自定义角色。

    ![添加角色](./media/create-manage-support-requests-using-access-control/add-role.png)

2. 单击“用户”边栏选项卡下的“添加”。 从角色列表中选择“支持请求参与者”自定义角色

    ![添加支持参与者角色](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. 选择角色名称后，单击“添加用户”并输入 Joe 的电子邮件凭据。 单击“选择”

    ![添加用户](./media/create-manage-support-requests-using-access-control/add-users.png)

4. 单击“确定”继续

    ![添加访问权限](./media/create-manage-support-requests-using-access-control/add-access.png)

5. 现在，你将在你是所有者的订阅下看到具有新添加的自定义角色“支持请求参与者”的用户

    ![已添加的用户](./media/create-manage-support-requests-using-access-control/user-added.png)

    当 Joe 登录门户时，他将看到他已添加到的订阅。

7. Joe 在“帮助和支持”边栏选项卡上单击“新建支持请求”，可以为“Visual Studio Ultimate with MSDN”创建支持请求

    ![新建支持请求](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Joe 单击“所有支持请求”可以看到已为此订阅创建的支持请求列表  ![案例详细信息视图](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>在 Azure 门户中删除支持请求访问权限

就像在可以向用户授予访问权限使其可以创建和管理支持请求一样，也可以删除用户的访问权限。
若要删除创建和管理支持请求的能力，请转到此订阅，单击“设置”，然后单击用户（在本例中为 Joe）。
右键单击角色名称“支持请求参与者”，然后单击“删除”

![删除支持请求访问权限](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

当 Joe 登录到门户并尝试创建支持请求时，他遇到以下错误

![订阅错误-2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

当 Joe 单击“所有支持请求”时，他看不到任何支持请求

![案例详细信息视图-2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)



<!--HONumber=Feb17_HO1-->


