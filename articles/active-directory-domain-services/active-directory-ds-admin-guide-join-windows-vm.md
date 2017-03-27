---
title: "Azure Active Directory 域服务：将 Windows Server VM 加入托管域 | Microsoft 文档"
description: "将 Windows Server 虚拟机加入 Azure AD 域服务"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 74dbdb33-05db-4d47-badc-0d7bb6d0c8cb
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: aad6bcd3eb704f090156d2ace80d2540a9543bd7
ms.lasthandoff: 12/29/2016


---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>将 Windows Server 虚拟机加入托管域
> [!div class="op_single_selector"]
> * [Azure 经典门户 - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
> * [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)
>
>

<br>

本文说明如何使用 Azure 经典门户将运行 Windows Server 2012 R2 的虚拟机加入 Azure AD 域服务托管域。

## <a name="step-1-create-the-windows-server-virtual-machine"></a>步骤 1：创建 Windows Server 虚拟机
遵循[在 Azure 经典门户中创建运行 Windows 的虚拟机](../virtual-machines/virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)教程中所述的说明操作。 请务必将这个新建的虚拟机加入已在其中启用了 Azure AD 域服务的同一个虚拟网络。 使用“快速创建”选项无法将虚拟机加入虚拟网络。 因此，需要使用“从库中”选项创建虚拟机。

执行以下步骤创建 Windows 虚拟机，然后将其加入已在其中启用了 Azure AD 域服务的虚拟网络。

1. 在 Azure 经典门户中，单击窗口底部命令栏上的“新建”。
2. 在“计算”下面，单击“虚拟机”，然后单击“从库中”。
3. 在出现的第一个屏幕中，可以从可用映像列表中为虚拟机**选择映像**。 请选择适当的映像。

    ![选择映像](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)
4. 第二个屏幕可让你选择计算机名称、大小和管理用户名与密码。 使用所需的层和大小运行你的应用或工作负载。 在此处选择的用户名是计算机上的本地管理员用户。 请不要输入域用户帐户的凭据。

    ![配置虚拟机](./media/active-directory-domain-services-admin-guide/create-windows-vm-config.png)
5. 第三个屏幕可让你配置网络资源、存储和可用性。 请务必从“区域/地缘组/虚拟网络”下拉列表中选择已在其中启用了 Azure AD 域服务的虚拟网络。 为虚拟机指定合适的**云服务 DNS 名称**。

    ![为虚拟机选择虚拟网络](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

   > [!WARNING]
   > 请务必将虚拟机加入已在其中启用了 Azure AD 域服务的同一个虚拟网络。 这样，虚拟机就可以“看到”该域并执行域加入等任务。 如果选择在不同的虚拟网络中创建虚拟机，请将该虚拟网络连接到已在其中启用了 Azure AD 域服务的虚拟网络。
   >
   >
6. 在第四个屏幕中，可以安装 VM 代理以及配置某些可用的扩展。

    ![完成](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
7. 创建虚拟机后，经典门户将在“虚拟机”节点下列出新虚拟机。 虚拟机和云服务都会自动启动，其状态将显示为“正在运行”。

    ![虚拟机已启动并运行](./media/active-directory-domain-services-admin-guide/create-windows-vm-running.png)

## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>步骤 2：使用本地管理员帐户连接到 Windows Server 虚拟机
现在，连接到新建的 Windows Server 虚拟机，以便将其加入域。 使用创建虚拟机时指定的本地管理员凭据来与它建立连接。

执行以下步骤连接到虚拟机。

1. 在经典门户中导航到“虚拟机”节点。 选择在步骤 1 中创建的虚拟机，然后单击窗口底部命令栏上的“连接”。

    ![连接到 Windows 虚拟机](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
2. 经典门户将提示打开或保存用于连接虚拟机的、扩展名为“.rdp”的文件。 文件下载完成后，请单击打开该文件。
3. 在登录提示中，输入创建虚拟机时指定的**本地管理员凭据**。 例如，本示例使用的是“localhost\mahesh”。

此时，应已使用本地管理员凭据登录到新建的 Windows 虚拟机。 下一步是将该虚拟机加入域。

## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>步骤 3：将 Windows Server 虚拟机加入 AAD-DS 托管域
执行以下步骤，将 Windows Server 虚拟机加入 AAD-DS 托管域。

1. 如步骤 2 中所述连接到 Windows Server。 在“开始”屏幕中打开“服务器管理器”。
2. 在“服务器管理器”窗口的左窗格中单击“本地服务器”。

    ![在虚拟机上启动服务器管理器](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)
3. 单击“属性”部分下面的“工作组”。 在“系统属性”属性页中，单击“更改”加入域。

    ![“系统属性”页](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)
4. 在“域”文本框中指定 Azure AD 域服务托管域的域名，然后单击“确定”。

    ![指定要加入的域](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)
5. 系统会提示输入用于加入域的凭据。 请务必**指定属于 AAD DC 管理员组的用户的凭据**。 只有此组的成员才有权将计算机加入托管域。

    ![指定用于加入域的凭据](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)
6. 可以通过以下方式之一指定凭据：

   * UPN 格式：指定在 Azure AD 中配置的用户帐户的 UPN 后缀。 在本示例中，用户“bob”的 UPN 后缀为 'bob@domainservicespreview.onmicrosoft.com'。
   * SAMAccountName 格式：可以使用 SAMAccountName 格式指定帐户名。 在本示例中，用户“bob”需要输入“CONTOSO100\bob”。

     > [!NOTE]
     > **建议使用 UPN 格式指定凭据。** 如果用户的 UPN 前缀过长（例如“joereallylongnameuser”），系统可能会自动生成 SAMAccountName。 如果 Azure AD 租户中有多个用户具有相同的 UPN 前缀（例如“bob”），服务可能会自动生成其 SAMAccountName 格式。 在这种情况下，使用 UPN 格式能够可靠地登录域。
     >
     >
7. 成功加入域后，将看到以下欢迎加入域的消息。 请重新启动虚拟机完成域加入操作。

    ![欢迎加入域](./media/active-directory-domain-services-admin-guide/join-domain-done.png)

## <a name="troubleshooting-domain-join"></a>排查域加入问题
### <a name="connectivity-issues"></a>连接问题
如果虚拟机找不到域，请参阅以下故障排除步骤：

* 确保虚拟机已连接到已在其中启用域服务的同一个虚拟网络。 否则，虚拟机无法连接到域，因此也就无法加入该域。
* 如果虚拟机已连接到其他虚拟网络，请确保此虚拟网络已连接到已在其中启用域服务的虚拟网络。
* 尝试使用托管域的域名来 ping 域（例如“ping contoso100.com”）。 如果无法执行此操作，请尝试 ping 页面中显示的、已在其中启用 Azure AD 域服务的域的 IP 地址（例如“ping 10.0.0.4”）。 如果能够 ping 通该 IP 地址，但无法 ping 通域，则表示 DNS 的配置可能不正确。 可能尚未将域的 IP 地址配置为虚拟网络的 DNS 服务器。
* 请尝试刷新虚拟机上的 DNS 解析程序缓存（“ipconfig /flushdns”）。

如果出现请求提供凭据来加入域的对话框，则表示没有连接问题。

### <a name="credentials-related-issues"></a>与凭据相关的问题
如果凭据有问题，因而无法加入域，请参考以下步骤。

* 尝试使用 UPN 格式指定凭据。 如果租户中有多个用户具有相同的 UPN 前缀，或者 UPN 前缀过长，系统可能会自动生成帐户的 SAMAccountName。 因此，帐户的 SAMAccountName 格式可能不同于所需的格式或者在本地域中使用的格式。
* 请尝试使用属于“AAD DC 管理员”组的用户帐户的凭据将计算机加入托管域。
* 确保根据《入门指南》中所述的步骤[启用密码同步](active-directory-ds-getting-started-password-sync.md)。
* 确保使用 Azure AD 中配置的用户的 UPN（例如 'bob@domainservicespreview.onmicrosoft.com') 登录。
* 确保根据《入门指南》中所述，等待足够长的时间来完成密码同步。

## <a name="related-content"></a>相关内容
* [Azure AD 域服务 - 入门指南](active-directory-ds-getting-started.md)
* [管理受 Azure AD 域服务管理的域](active-directory-ds-admin-guide-administer-domain.md)

