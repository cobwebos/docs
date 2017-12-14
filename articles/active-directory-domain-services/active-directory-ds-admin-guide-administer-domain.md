---
title: "Azure Active Directory 域服务：管理托管域 | Microsoft Docs"
description: "管理 Azure Active Directory 域服务托管域"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4fdbc75-3e6b-4e20-8494-5dcc3bf2220a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: maheshu
ms.openlocfilehash: 7ffe3e54b891fd74e14666c136be49132d78590b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>管理 Azure Active Directory 域服务托管域
本文介绍如何管理 Azure Active Directory (AD) 域服务托管域。

## <a name="before-you-begin"></a>开始之前
若要执行本文中所列的任务，需要：

1. 一个有效的 **Azure 订阅**。
2. 一个 **Azure AD 目录** - 已与本地目录或仅限云的目录同步。
3. 必须为 Azure AD 目录启用 **Azure AD 域服务**。 如果未启用，请遵循[入门指南](active-directory-ds-getting-started.md)中所述的所有任务。
4. 一台**已加入域的虚拟机**，通过此虚拟机管理 Azure AD 域服务托管域。 如果没有此类虚拟机，请遵循[将 Windows 虚拟机加入托管域](active-directory-ds-admin-guide-join-windows-vm.md)一文中所述的所有任务。
5. 需要目录中**属于“AAD DC 管理员”组的用户帐户**的凭据来管理托管域。

<br>

## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>可以在托管域上执行的管理任务
“AAD DC 管理员”组的成员被授予托管域上的相应权限，可以执行下述任务：

* 将计算机加入托管域。
* 配置托管域中”AADDC 计算机”和”AADDC 用户”容器的内置 GPO。
* 管理托管域上的 DNS。
* 创建和管理托管域上的自定义组织单位 (OU)。
* 获取对已加入托管域的计算机的管理访问权限。

## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>用户没有的托管域的管理权限
域以及各种活动（例如修补、监视和执行备份）由 Microsoft 托管。 因此，域处于锁定状态，用户没有权限对域执行某些管理任务。 下面是用户无法执行的任务的一些示例。

* 没有托管域的域管理员或企业管理员权限。
* 无法扩展托管域的架构。
* 无法使用远程桌面连接到托管域的域控制器。
* 无法将域控制器添加到托管域。

## <a name="task-1---provision-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>任务 1 - 预配已加入域的 Windows Server 虚拟机，以便远程管理托管域
可以使用熟悉的 Active Directory 管理工具（例如 Active Directory 管理中心 (ADAC) 或 AD PowerShell）对 Azure AD 域服务托管域进行管理。 租户管理员无权通过远程桌面连接到托管域上的域控制器。 因此，“AAD DC 管理员”组的成员可以使用已加入托管域的 Windows Server/客户端计算机中的 AD 管理工具对托管域进行远程管理。 可将 AD 管理工具作为远程服务器管理工具 (RSAT) 可选功能的一部分安装到已加入托管域的 Windows Server 和客户端计算机上。

第一个步骤是设置已加入托管域的 Windows Server 虚拟机。 有关说明，请参阅[将 Windows Server 虚拟机加入 Azure AD 域服务托管域](active-directory-ds-admin-guide-join-windows-vm.md)一文。

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>通过客户端计算机（例如 Windows 10）远程管理托管域
本文中的说明使用 Windows Server 虚拟机管理 AAD-DS 托管域。 不过，用户也可选择使用 Windows 客户端（例如 Windows 10）虚拟机来这样做。

可以按照 TechNet 上的说明在 Windows 客户端虚拟机上[安装远程服务器管理工具 (RSAT)](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx)。

## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>任务 2 - 在虚拟机上安装 Active Directory 管理工具
执行以下步骤，在已加入域的虚拟机上安装 Active Directory 管理工具。 有关[安装和使用远程服务器管理工具](https://technet.microsoft.com/library/hh831501.aspx)的详细信息，请参阅 Technet。

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
9. 在“功能”页上，通过单击展开“远程服务器管理工具”节点，并通过单击展开“角色管理工具”节点。 从角色管理工具列表中选择“AD DS 和 AD LDS 工具”功能。

    ![“功能”页](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)
10. 在“确认”页上，单击“安装”在虚拟机上安装 AD 和 AD LDS 工具功能。 功能安装成功完成后，单击“关闭”退出“添加角色和功能”向导。

    ![“确认”页](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)

## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>任务 3 - 连接到托管域并对其进行浏览
在已加入域的虚拟机上安装 AD 管理工具以后，即可使用这些工具浏览和管理托管域。

> [!NOTE]
> 必须是“AAD DC 管理员”组的成员才能管理托管域。
>
>

1. 在“开始”屏幕中单击“管理工具”。 此时会看到在虚拟机上安装的 AD 管理工具。

    ![在服务器上安装的管理工具](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. 单击“Active Directory 管理中心”。

    ![Active Directory 管理中心](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. 若要浏览域，请单击左窗格中的域名（例如“contoso100.com”）。 请注意两个容器，其名称分别为“AADDC 计算机”和“AADDC 用户”。

    ![ADAC - 查看域](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)
4. 单击名为“AADDC 用户”的容器，查看属于托管域的所有用户和组。 会看到来自 Azure AD 租户的用户帐户和组显示在此容器中。 请注意在此示例中，此容器中会出现名为“bob”的用户的用户帐户，以及名为“AAD DC 管理员”的组。

    ![ADAC - 域用户](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)
5. 单击名为“AADDC 计算机”的容器，查看已加入此托管域的计算机。 此时会显示已加入域的当前虚拟机的项。 已加入 Azure AD 域服务托管域的所有计算机的计算机帐户存储在“AADDC 计算机”这个容器中。

    ![ADAC - 已加入域的计算机](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>相关内容
* [Azure AD 域服务 - 入门指南](active-directory-ds-getting-started.md)
* [将 Windows Server 虚拟机加入 Azure AD 域服务托管域](active-directory-ds-admin-guide-join-windows-vm.md)
* [部署远程服务器管理工具](https://technet.microsoft.com/library/hh831501.aspx)
