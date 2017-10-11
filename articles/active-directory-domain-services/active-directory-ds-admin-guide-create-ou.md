---
title: "Azure Active Directory 域服务： 管理指南 |Microsoft 文档"
description: "在托管的 Azure AD 域服务域上创建组织单位 (OU)"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: 017a8cabe81743af4c0cbb694098df799a904468
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>在 Azure AD 域服务托管域中创建组织单位 (OU)
Azure AD 域服务托管的域包含两个分别名为 AADDC 计算机和 AADDC 用户的内置容器。 AADDC 计算机容器具有所有计算机的已加入到托管域的计算机的对象。 AADDC 用户容器在 Azure AD 租户中包含用户和组。 有时，可能需要在要部署工作负荷的托管域上创建服务帐户。 为此，可以在托管的域中创建自定义组织单位 (OU)，并创建该 OU 内的服务帐户。 这篇文章演示了如何在托管域中创建 OU。

## <a name="before-you-begin"></a>在开始之前
若要执行本文中列出的任务，你需要：

1. 一个有效**Azure 订阅**。
2. **Azure AD 目录**-可以与本地目录或仅限云的目录同步。
3. **Azure AD 域服务**必须启用 Azure AD 目录。 如果你尚未这样做，请按照中所述的所有任务[入门指南](active-directory-ds-getting-started.md)。
4. 已加入域的虚拟机从其管理的管理的 Azure AD 域服务域。 如果你没有此类虚拟机，请按照一文中概述的所有任务[将 Windows 虚拟机加入到托管域](active-directory-ds-admin-guide-join-windows-vm.md)。
5. 你需要的凭据**属于 AAD DC 管理员组的用户帐户**目录，以在托管域上创建自定义的 OU 中。

## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>以进行远程管理已加入域的虚拟机上安装 AD 管理工具
可以使用远程熟悉 Active Directory 管理工具，如 Active Directory 管理中心 (ADAC) 或 AD PowerShell 管理 azure AD 域服务托管的域。 租户管理员不具有特权连接到域控制器上通过远程桌面托管的域。 若要管理的托管的域，请加入到托管域的虚拟机上安装了 AD 管理工具功能。 请参阅标题为文章[管理 Azure AD 域服务托管的域](active-directory-ds-admin-guide-administer-domain.md)有关的说明。

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>在托管的域中创建组织单位
现在，AD 管理工具安装在已加入域的虚拟机，我们可以使用这些工具上托管的域中创建组织单位。 执行以下步骤：

> [!NOTE]
> 只有 AAD DC 管理员组的成员具有所需的权限来创建自定义的 OU。 请确保属于此组的用户执行以下步骤。
>
>

1. 从开始屏幕中，单击**管理工具**。 你应看到在虚拟机上安装了 AD 管理工具。

    ![在服务器上安装的管理工具](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. 单击**Active Directory Administrative Center**。

    ![Active Directory 管理中心](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. 若要查看域，请单击左窗格中 (例如，contoso100.com) 中的域名。

    ![ADAC-视图域](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)
4. 在右侧**任务**窗格中，单击**新建**域名称节点下。 在此示例中，我们单击**新建**右侧的 contoso100(local) 节点下**任务**窗格。

    ![ADAC-新 OU](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)
5. 你应看到此选项来创建组织单位。 单击**组织单位**以启动**创建组织单位**对话框。
6. 在**创建组织单位**对话框中，指定**名称**为新 OU。 为 OU 中提供的简短说明。 您还可以设置**管理者**OU 字段。 若要创建自定义的 OU，请单击**确定**。

    ![ADAC-创建 OU 对话框](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)
7. 新创建的 OU 现在应显示在 AD 管理中心 (ADAC)。

    ![ADAC-创建 OU](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="permissionssecurity-for-newly-created-ous"></a>新创建的 Ou 的权限/安全性
默认情况下，创建自定义的 OU 的用户 （AAD DC 管理员组的成员） 是通过 OU 授予管理权限 （完全控制）。 然后，用户可以继续，并将权限授予其他用户或作为所需的 AAD DC 管理员组。 如以下屏幕截图中，用户所示bob@domainservicespreview.onmicrosoft.com创建新的 MyCustomOU 组织单位的人员授予完全控制。

 ![ADAC-新的 OU 安全](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)

## <a name="notes-on-administering-custom-ous"></a>有关管理自定义的 Ou 的说明
现在，你已创建自定义的 OU，你可以继续，并在此 OU 中创建用户、 组、 计算机和服务帐户。 不能移动用户或组从 ' AADDC Users' OU 到自定义的 Ou。

> [!WARNING]
> 用户帐户、 组、 服务帐户和自定义 Ou 下创建的计算机对象不在你的 Azure AD 租户中可用。 换而言之，这些对象不显示使用 Azure AD Graph API 或 Azure AD UI 中。 这些对象在 Azure AD 域服务托管域中才可用。
>
>

## <a name="related-content"></a>相关的内容
* [管理 Azure AD 域服务托管的域](active-directory-ds-admin-guide-administer-domain.md)
* [在托管域上配置组策略](active-directory-ds-admin-guide-administer-group-policy.md)
* [Active Directory 管理中心： 入门](https://technet.microsoft.com/library/dd560651.aspx)
* [服务帐户循序渐进指南](https://technet.microsoft.com/library/dd548356.aspx)
