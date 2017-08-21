---
title: "Azure Active Directory 域服务：创建 Azure AD DC 管理员组 | Microsoft Docs"
description: "通过 Azure 经典门户启用 Azure Active Directory 域服务"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: 19be73fd0aec3a8f03a7cd83c12cfcc060f6e5e7
ms.openlocfilehash: 5ed0125e05928cf0f6d9941e099b433ecb46e6e2
ms.contentlocale: zh-cn
ms.lasthandoff: 07/13/2017

---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-classic-portal"></a>通过 Azure 经典门户启用 Azure Active Directory 域服务
本文介绍并指导你完成为 Azure Active Directory (Azure AD) 租户启用 Azure Active Directory 域服务 (Azure AD DS) 所需的配置任务。

> [!NOTE]
> [**改为尝试新的（预览版）Azure 门户体验**](active-directory-ds-getting-started.md)。 
>

## <a name="task-1-create-the-azure-ad-dc-administrators-group"></a>任务 1：创建 Azure AD DC 管理员组
第一个任务是在 Azure AD 租户中创建管理组。 此特殊管理组称为 *AAD DC 管理员*。 此组的成员在已加入 Azure Active Directory 域服务托管域的计算机上拥有管理权限。 在已加入域的计算机上，此组将添加到管理员组。 此外，此组的成员可以使用远程桌面远程连接到已加入域的计算机。  

> [!NOTE]
> 你在使用 Azure Active Directory 域服务创建的托管域中没有“域管理员”或“企业管理员”权限。 在托管域上，服务将保留这些权限，不会将其提供给租户中的用户。 但是，可以使用在此配置任务中创建的特殊管理组执行某些特权操作。 这些操作包括将计算机加入域、将用户添加到已加入域的计算机上的管理组和配置组策略。
>

在此配置任务中，需要创建一个管理组，并将目录中的一个或多个用户添加到该组。 若要创建 Azure Active Directory 域服务的管理组，请执行以下操作：

1. 转到 [Azure 经典门户](https://manage.windowsazure.com)。
2. 在左窗格中，选择“Active Directory”按钮。
3. 选择要为其启用 Azure Active Directory 域服务的 Azure AD 租户（目录）。 对于每个 Azure AD 目录，只能创建一个域。

    ![选择 Azure AD 目录](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. 在“预览目录”页上，单击“组”选项卡。
5. 若要将组添加到 Azure AD 租户，请在窗口底部的任务窗格中单击“添加组”。

    ![“添加组”按钮](./media/active-directory-domain-services-getting-started/add-group-button.png)
6. 在“添加组”对话框中，创建一个名为“AAD DC 管理员”的组，然后将“组类型”设置为“安全”。

   > [!WARNING]
   > 若要在 Azure Active Directory 域服务托管域中启用访问权限，请使用此确切名称创建组。
   >
   >

    ![“添加组”对话框](./media/active-directory-domain-services-getting-started/create-admin-group.png)
7. 在“说明”框中，输入一个说明，使其他人可以了解此组在 Azure Active Directory 域服务中授予管理权限。
8. 已创建组后，单击组名称以查看其属性。
9. 若要将用户添加为此组的成员，请在窗口底部单击“添加成员”按钮。

    ![添加组成员按钮](./media/active-directory-domain-services-getting-started/add-group-members-button.png)
10. 在“添加成员”对话框中，选择应成为此组的成员的用户，然后单击右下角的复选标记图标。

    ![将用户添加到管理员组](./media/active-directory-domain-services-getting-started/add-group-members.png)


## <a name="next-step"></a>后续步骤
[任务 2：创建或选择 Azure 虚拟网络](active-directory-ds-getting-started-vnet.md)

