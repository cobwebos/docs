---
title: "Azure Active Directory 域服务：管理托管域上的 DNS | Microsoft Docs"
description: "管理 Azure Active Directory 域服务托管域上的 DNS"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: maheshu
ms.openlocfilehash: 55b6368d55b5d0ad50d066a4963e74d8c44a2049
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>管理 Azure AD 域服务托管域上的 DNS
Azure Active Directory 域服务包括一个 DNS（域名解析）服务器，用于为托管域提供 DNS 解析。 用户偶尔需在托管域上配置 DNS。 用户可能需要为未加入域的计算机创建 DNS 记录、为负载均衡器配置虚拟 IP 地址，或者设置外部 DNS 转发器。 因此，需向属于“AAD DC 管理员”组的用户授予对托管域的“DNS 管理”权限。

## <a name="before-you-begin"></a>开始之前
若要执行本文中所列的任务，需要：

1. 一个有效的 **Azure 订阅**。
2. 一个 **Azure AD 目录** - 已与本地目录或仅限云的目录同步。
3. 必须为 Azure AD 目录启用 **Azure AD 域服务**。 如果未启用，请遵循[入门指南](active-directory-ds-getting-started.md)中所述的所有任务。
4. 一台**已加入域的虚拟机**，通过此虚拟机管理 Azure AD 域服务托管域。 如果没有此类虚拟机，请遵循[将 Windows 虚拟机加入托管域](active-directory-ds-admin-guide-join-windows-vm.md)一文中所述的所有任务。
5. 需要目录中**属于“AAD DC 管理员”组的用户帐户**的凭据来管理托管域的 DNS。

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>任务 1 - 预配已加入域的虚拟机，以便远程管理托管域的 DNS
可以使用熟悉的 Active Directory 管理工具（例如 Active Directory 管理中心 (ADAC) 或 AD PowerShell）对 Azure AD 域服务托管域进行远程管理。 同样，可以使用 DNS 服务器管理工具远程管理托管域的 DNS。

Azure AD 目录中的管理员无权通过远程桌面连接到托管域上的域控制器。 “AAD DC 管理员”组的成员可以使用已加入托管域的 Windows Server/客户端计算机中的 DNS 服务器工具对托管域的 DNS 进行远程管理。 可将 DNS 服务器工具作为远程服务器管理工具 (RSAT) 可选功能的一部分安装到已加入托管域的 Windows Server 和客户端计算机上。

第一个任务是预配已加入托管域的 Windows Server 虚拟机。 有关说明，请参阅[将 Windows Server 虚拟机加入 Azure AD 域服务托管域](active-directory-ds-admin-guide-join-windows-vm.md)一文。

## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>任务 2 - 在虚拟机上安装 DNS 服务器工具
执行以下步骤，在已加入域的虚拟机上安装 DNS 管理工具。 有关[安装和使用远程服务器管理工具](https://technet.microsoft.com/library/hh831501.aspx)的详细信息，请参阅 Technet。

1. 导航到 Azure 门户。 单击左侧面板上的“所有资源”。 找到并单击在任务 1 中创建的虚拟机。
2. 单击“概述”选项卡上的“连接”按钮。此时会创建并下载远程桌面协议 (.rdp) 文件。

    ![连接到 Windows 虚拟机](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. 若要连接到 VM，请打开下载的 RDP 文件。 出现提示时，请单击“连接”。 出现登录提示时，请使用属于“AAD DC 管理员”组的用户的凭据。 例如，我们在示例中使用“bob@domainservicespreview.onmicrosoft.com”。 你可能会在登录过程中收到证书警告。 单击“是”或“继续”以继续连接。

4. 在“开始”屏幕中打开“服务器管理器”。 在“服务器管理器”窗口的中心窗格中单击“添加角色和功能”。

    ![在虚拟机上启动服务器管理器](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. 在“添加角色和功能向导”的“准备工作”页上，单击“下一步”。

    ![“准备工作”页](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. 在“安装类型”页上选中“基于角色或基于功能的安装”选项，并单击“下一步”。

    ![“安装类型”页](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. 在“服务器选择”页上，从服务器池中选择当前的虚拟机，并单击“下一步”。

    ![“服务器选择”页](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. 在“服务器角色”页上，单击“下一步”。 我们跳过此页是因为我们不在服务器上安装任何角色。
9. 在“功能”页上，通过单击展开“远程服务器管理工具”节点，并通过单击展开“角色管理工具”节点。 从角色管理工具列表中选择“DNS 服务器工具”功能。

    ![“功能”页](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)
10. 在“确认”页上，单击“安装”在虚拟机上安装 DNS 服务器工具功能。 功能安装成功完成后，单击“关闭”退出“添加角色和功能”向导。

    ![“确认”页](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)

## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>任务 3 - 启动 DNS 管理控制台，对 DNS 进行管理
在已加入域的虚拟机上安装 DNS 服务器工具功能以后，即可使用 DNS 工具管理托管域上的 DNS。

> [!NOTE]
> 必须是“AAD DC 管理员”组的成员才能管理托管域上的 DNS。
>
>

1. 在“开始”屏幕中单击“管理工具”。 此时会看到在虚拟机上安装的 **DNS** 控制台。

    ![管理工具 - DNS 控制台](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)
2. 单击“DNS”启动 DNS 管理控制台。
3. 在“连接到 DNS 服务器”对话框中，单击名为“以下计算机”的选项，并输入托管域的 DNS 域名（例如“contoso100.com”）。

    ![DNS 控制台 - 连接到域](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)
4. DNS 控制台连接到托管域。

    ![DNS 控制台 - 管理域](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)
5. 用户现在可以使用 DNS 控制台，在已启用 AAD 域服务的虚拟网络中添加计算机的 DNS 条目。

> [!WARNING]
> 使用 DNS 管理工具管理托管域的 DNS 时要小心。 确保**不删除或修改域中的域服务使用的内置 DNS 记录**。 内置 DNS 记录包括域 DNS 记录、名称服务器记录，以及其他用于 DC 定位的记录。 如果修改这些记录，虚拟网络上的域服务会中断。
>
>

请参阅 [Technet 上的“DNS 工具”一文](https://technet.microsoft.com/library/cc753579.aspx)，详细了解如何管理 DNS。

## <a name="related-content"></a>相关内容
* [Azure AD 域服务 - 入门指南](active-directory-ds-getting-started.md)
* [将 Windows Server 虚拟机加入 Azure AD 域服务托管域](active-directory-ds-admin-guide-join-windows-vm.md)
* [管理受 Azure AD 域服务管理的域](active-directory-ds-admin-guide-administer-domain.md)
* [DNS administration tools](https://technet.microsoft.com/library/cc753579.aspx)（DNS 管理工具）
