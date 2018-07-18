---
title: Azure Active Directory 域服务：管理指南 | Microsoft 文档
description: 在 Azure AD 域服务托管域中创建组织单位 (OU)
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: 15bd837149b9856897eb83f86052a26b24a21fb0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36334303"
---
# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>在 Azure AD 域服务托管域中创建组织单位 (OU)
Azure AD 域服务托管域包含两个内置容器，分别称为“AADDC 计算机”和“AADDC 用户”。 “AADDC 计算机”容器包含已加入托管域的所有计算机的计算机对象。 “AADDC 用户”容器包含 Azure AD 租户中的用户和组。 有时，可能需要在托管域上创建服务帐户才能部署工作负荷。 为此，可以在托管域上创建自定义的组织单位 (OU)，并在此 OU 中创建服务帐户。 本文说明如何在托管域中创建 OU。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>开始之前
若要执行本文中所列的任务，需要：

1. 一个有效的 **Azure 订阅**。
2. 一个 **Azure AD 目录** - 已与本地目录或仅限云的目录同步。
3. 必须为 Azure AD 目录启用 **Azure AD 域服务**。 如果未启用，请遵循[入门指南](active-directory-ds-getting-started.md)中所述的所有任务。
4. 一台已加入域的虚拟机，通过此虚拟机管理 Azure AD 域服务托管域。 如果没有此类虚拟机，请遵循[将 Windows 虚拟机加入托管域](active-directory-ds-admin-guide-join-windows-vm.md)一文中所述的所有任务。
5. 需要目录中**属于“AAD DC 管理员”组的用户帐户**的凭据来在托管域上创建自定义 OU。

## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>在已加入域的虚拟机上安装 AD 管理工具进行远程管理
可以使用熟悉的 Active Directory 管理工具（例如 Active Directory 管理中心 (ADAC) 或 AD PowerShell）对 Azure AD 域服务托管域进行远程管理。 租户管理员无权通过远程桌面连接到托管域上的域控制器。 若要管理托管域，请在加入托管域的虚拟机上安装 AD 管理工具功能。 有关说明，请参阅[管理 Azure AD 域服务托管域](active-directory-ds-admin-guide-administer-domain.md)一文。

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>在托管域上创建组织单位
在已加入域的虚拟机上安装 AD 管理工具后，可以使用这些工具在托管域上创建组织单位。 执行以下步骤：

> [!NOTE]
> 只有“AAD DC 管理员”组的成员具有创建自定义 OU 的必要权限。 请确保以此组成员的用户身份执行以下步骤。
>
>

1. 在“开始”屏幕中单击“管理工具”。 此时会看到在虚拟机上安装的 AD 管理工具。

    ![在服务器上安装的管理工具](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. 单击“Active Directory 管理中心”。

    ![Active Directory 管理中心](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. 若要查看域，请单击左窗格中的域名（例如“contoso100.com”）。

    ![ADAC - 查看域](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)
4. 在右侧的“任务”窗格中，单击域名节点下的“新建”。 在本示例中，请在右侧的“任务”窗格中单击“contoso100(local)”节点下的“新建”。

    ![ADAC - 新建 OU](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)
5. 应会看到用于创建组织单位的选项。 单击“组织单位”启动“创建组织单位”对话框。
6. 在“创建组织单位”对话框中，指定新 OU 的**名称**。 提供 OU 的简短说明。 还可以设置 OU 的“管理方”字段。 若要创建自定义 OU，请单击“确定”。

    ![ADAC - 创建 OU 对话框](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)
7. 新建的 OU 应会出现在 AD 管理中心 (ADAC)。

    ![ADAC - 已创建 OU](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="permissionssecurity-for-newly-created-ous"></a>新建 OU 的权限/安全性
创建自定义 OU 的用户（“AAD DC 管理员”组的成员）默认已被授予 OU 的管理权限（完全控制权）。 此用户以后可以继续将权限授予其他用户，或根据需要向“AAD DC 管理员”组授予权限。 如以下屏幕截图所示，创建新“MyCustomOU”组织单位的用户“bob@domainservicespreview.onmicrosoft.com”已被授予该组织单位的完全控制权。

 ![ADAC - 新 OU 的安全性](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)

## <a name="notes-on-administering-custom-ous"></a>有关管理自定义 OU 的说明
创建自定义 OU 后，可以继续在此 OU 中创建用户、组、计算机和服务帐户。 无法将用户或组从“AADDC 用户”OU 移到自定义 OU。

> [!WARNING]
> 在自定义 OU 下创建的用户帐户、组、服务帐户和计算机对象无法在 Azure AD 租户中使用。 换而言之，使用 Azure AD 图形 API 或 Azure AD UI 无法显示这些对象。 这些对象仅在 Azure AD 域服务托管域中可用。
>
>

## <a name="related-content"></a>相关内容
* [管理 Azure AD 域服务托管域](active-directory-ds-admin-guide-administer-domain.md)
* [在托管域上配置组策略](active-directory-ds-admin-guide-administer-group-policy.md)
* [Active Directory 管理中心：入门](https://technet.microsoft.com/library/dd560651.aspx)
* [服务帐户分步指南](https://technet.microsoft.com/library/dd548356.aspx)
