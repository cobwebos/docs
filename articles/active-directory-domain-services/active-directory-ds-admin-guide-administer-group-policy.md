---
title: "Azure Active Directory 域服务：管理托管域上的组策略 | Microsoft 文档"
description: "管理 Azure Active Directory 域服务托管域上的组策略"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 76987a6e91ae688b3856567073a7d27472e5ba09
ms.openlocfilehash: 9245eb870f592ee0a1f1d6956ce3d573f4902485


---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>管理 Azure AD 域服务托管域上的组策略
Azure Active Directory 域服务包括“AADDC 用户”和“AADDC 计算机”容器的内置组策略对象 (GPO)。 可以自定义这些内置 GPO，以便在托管域上配置组策略。 另外，“AAD DC 管理员”组的成员可以在托管域中创建自己的自定义 OU。 他们还可以创建自定义 GPO，并将其链接到这些自定义 OU。 需向属于“AAD DC 管理员”组的用户授予对托管域的“组策略管理”权限。

## <a name="before-you-begin"></a>开始之前
若要执行本文中所列的任务，需要：

1. 一个有效的 **Azure 订阅**。
2. 一个 **Azure AD 目录** - 已与本地目录或仅限云的目录同步。
3. 必须为 Azure AD 目录启用 **Azure AD 域服务**。 如果未启用，请遵循[入门指南](active-directory-ds-getting-started.md)中所述的所有任务。
4. 一台**已加入域的虚拟机**，通过此虚拟机管理 Azure AD 域服务托管域。 如果没有此类虚拟机，请遵循[将 Windows 虚拟机加入托管域](active-directory-ds-admin-guide-join-windows-vm.md)一文中所述的所有任务。
5. 需要目录中**属于“AAD DC 管理员”组的用户帐户**的凭据来管理托管域的组策略。

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>任务 1 - 预配已加入域的虚拟机，以便远程管理托管域的组策略
可以使用熟悉的 Active Directory 管理工具（例如 Active Directory 管理中心 (ADAC) 或 AD PowerShell）对 Azure AD 域服务托管域进行远程管理。 同样，可以使用组策略管理工具远程管理托管域的组策略。

Azure AD 目录中的管理员无权通过远程桌面连接到托管域上的域控制器。 “AAD DC 管理员”组的成员可以远程管理托管域的组策略。 他们可以在已加入托管域的 Windows Server/客户端计算机上使用组策略工具。 可将组策略工具作为组策略管理可选功能的一部分安装到已加入托管域的 Windows Server 和客户端计算机上。

第一个任务是预配已加入托管域的 Windows Server 虚拟机。 有关说明，请参阅[将 Windows Server 虚拟机加入 Azure AD 域服务托管域](active-directory-ds-admin-guide-join-windows-vm.md)一文。

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>任务 2 - 在虚拟机上安装组策略工具
执行以下步骤，在已加入域的虚拟机上安装组策略管理工具。

1. 在 Azure 经典门户中导航到“虚拟机”节点。 选择在任务 1 中创建的虚拟机，然后单击窗口底部命令栏上的“连接”。

    ![连接到 Windows 虚拟机](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
2. 经典门户将提示打开或保存用于连接虚拟机的、扩展名为“.rdp”的文件。 文件下载完成后，请单击该文件。
3. 出现登录提示时，请使用属于“AAD DC 管理员”组的用户的凭据。 例如，我们在示例中使用 'bob@domainservicespreview.onmicrosoft.com'。
4. 在“开始”屏幕中打开“服务器管理器”。 在“服务器管理器”窗口的中心窗格中单击“添加角色和功能”。

    ![在虚拟机上启动服务器管理器](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. 在“添加角色和功能向导”的“准备工作”页上，单击“下一步”。

    ![“准备工作”页](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. 在“安装类型”页上选中“基于角色或基于功能的安装”选项，然后单击“下一步”。

    ![“安装类型”页](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. 在“服务器选择”页上，从服务器池中选择当前的虚拟机，然后单击“下一步”。

    ![“服务器选择”页](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. 在“服务器角色”页上，单击“下一步”。 我们跳过此页是因为我们不在服务器上安装任何角色。
9. 在“功能”页上，选择“组策略管理”功能。

    ![“功能”页](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. 在“确认”页上，单击“安装”在虚拟机上安装组策略管理功能。 功能安装成功完成后，单击“关闭”退出“添加角色和功能”向导。

    ![“确认”页](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>任务 3 - 启动组策略管理控制台来管理组策略
可以在已加入域的虚拟机上使用组策略管理控制台来管理托管域上的组策略。

> [!NOTE]
> 必须是“AAD DC 管理员”组的成员才能管理托管域上的组策略。
>
>

1. 在“开始”屏幕中单击“管理工具”。 此时会看到在虚拟机上安装的**组策略管理**控制台。

    ![启动组策略管理](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. 单击“组策略管理”启动组策略管理控制台。

    ![组策略控制台](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>任务 4 - 自定义内置的组策略对象
有两个内置的组策略对象 (GPO) - 托管域中的“AADDC 计算机”和“AADDC 用户”容器各一个。 可以自定义这些 GPO，以便在托管域上配置组策略。

1. 在“组策略管理”控制台中，单击展开“林: contoso100.com”和“域”节点，以便查看托管域的组策略。

    ![内置 GPO](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. 可以自定义这些内置 GPO，以便在托管域上配置组策略。 右键单击该 GPO，然后单击“编辑...”自定义内置 GPO。 可以使用组策略配置编辑器工具自定义该 GPO。

    ![编辑内置 GPO](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. 现在可以使用**组策略管理编辑器**控制台编辑内置 GPO。 例如，下面的屏幕截图显示了如何自定义内置的“AADDC 计算机”GPO。

    ![自定义 GPO](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>任务 5 - 创建自定义组策略对象 (GPO)
可以创建或导入自己的自定义组策略对象。 还可以将自定义 GPO 链接到在托管域中创建的自定义 OU。 有关创建自定义组织单位的详细信息，请参阅[在托管域中创建自定义 OU](active-directory-ds-admin-guide-create-ou.md)。

> [!NOTE]
> 必须是“AAD DC 管理员”组的成员才能管理托管域上的组策略。
>
>

1. 在“组策略管理”控制台中，单击选择自定义组织单位 (OU)。 右键单击 OU，然后单击“在此域中创建 GPO 并在此处链接...”。

    ![创建自定义 GPO](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. 指定新 GPO 的名称，然后单击“确定”。

    ![指定 GPO 的名称](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. 此时会创建新的 GPO 并将其链接到自定义 OU。 右键单击该 GPO，然后在菜单中单击“编辑...”。

    ![新创建的 GPO](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. 可以使用“组策略管理编辑器”自定义新创建的 GPO。

    ![自定义新的 GPO](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


可在 Technet 上查看有关如何使用[组策略管理控制台](https://technet.microsoft.com/library/cc753298.aspx)的详细信息。

## <a name="related-content"></a>相关内容
* [Azure AD 域服务 - 入门指南](active-directory-ds-getting-started.md)
* [将 Windows Server 虚拟机加入 Azure AD 域服务托管域](active-directory-ds-admin-guide-join-windows-vm.md)
* [管理受 Azure AD 域服务管理的域](active-directory-ds-admin-guide-administer-domain.md)
* [组策略管理控制台](https://technet.microsoft.com/library/cc753298.aspx)



<!--HONumber=Jan17_HO4-->


