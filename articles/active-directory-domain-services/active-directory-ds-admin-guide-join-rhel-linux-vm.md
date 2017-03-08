---
title: "Azure Active Directory 域服务：将 RHEL VM 加入托管域 | Microsoft 文档"
description: "将 Red Hat Enterprise Linux 虚拟机加入 Azure AD 域服务"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 87291c47-1280-43f8-8fb2-da1bd61a4942
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: d36b4c1391dad88603ad823055e11c0a4a329c3c
ms.openlocfilehash: 89e97cb903e04efa77cfc6b02484d28a9ccfe6fb
ms.lasthandoff: 01/13/2017


---
# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>将 Red Hat Enterprise Linux 7 虚拟机加入托管域
本文说明如何将 Red Hat Enterprise Linux (RHEL) 7 虚拟机加入 Azure AD 域服务托管域。

## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>预配 Red Hat Enterprise Linux 虚拟机
请执行以下步骤，以使用 Azure 门户预配 RHEL 7 虚拟机。

1. 登录到 [Azure 门户](https://portal.azure.com)。

    ![Azure 门户仪表板](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)
2. 在左侧窗格中单击“新建”，然后按以下屏幕截图中所示在搜索栏中键入 **Red Hat**。 搜索结果中会出现 Red Hat Enterprise Linux 的条目。 单击“Red Hat Enterprise Linux 7.2”。

    ![在结果中选择 RHEL](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)
3. “全部”窗格中的搜索结果应列出 Red Hat Enterprise Linux 7.2 映像。 单击“Red Hat Enterprise Linux 7.2”查看有关虚拟机映像的详细信息。

    ![在结果中选择 RHEL](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)
4. 在“Red Hat Enterprise Linux 7.2”窗格中，应会看到有关虚拟机映像的详细信息。 在“选择部署模型”下拉列表中选择“经典”。 然后单击“创建”按钮。

    ![查看映像详细信息](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)
5. 在“创建虚拟机”向导的“基本信息”页中，输入新的虚拟机的“主机名”。 另外，请在“用户名”字段中指定本地管理员用户名，并输入**密码**。 也可以选择使用 SSH 密钥对本地管理员用户进行身份验证。 另外，请选择虚拟机的**定价层**。

    ![创建 VM - 基本信息页](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)
6. 在“创建虚拟机”向导的“大小”页中，选择虚拟机的大小。

    ![创建 VM - 选择大小](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-vm-size.png)

7. 在“创建虚拟机”向导的“设置”页中，选择虚拟机的存储帐户。 单击“虚拟网络”，选择要将 Linux VM 部署到的虚拟网络。 在“虚拟网络”边栏选项卡中，选择可在其中使用 Azure AD 域服务的虚拟网络。 本示例选择了“MyPreviewVNet”虚拟网络。

    ![创建 VM - 选择虚拟网络](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)
8. 在“创建虚拟机”向导的“摘要”页中检查，然后单击“确定”按钮。

    ![创建 VM - 已选择虚拟网络](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)
9. 此时应开始基于 RHEL 7.2 映像部署新虚拟机。

    ![创建 VM - 部署开始](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)
10. 几分钟后，虚拟机应部署成功并可供使用。

    ![创建 VM - 已部署](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)

## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>远程连接到新预配的 Linux 虚拟机
RHEL 7.2 虚拟机已在 Azure 中预配。 下一个任务是远程连接到该虚拟机。

**连接到 RHEL 7.2 虚拟机**。请遵循[如何登录到运行 Linux 的虚拟机](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)一文中的说明。

余下的步骤假设使用 PuTTY SSH 客户端连接到 RHEL 虚拟机。 有关详细信息，请参阅 [PuTTY 下载页](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。

1. 打开 PuTTY 程序。
2. 为新建的 RHEL 虚拟机输入**主机名**。 在本示例中，虚拟机的主机名为“contoso-rhel.cloudapp.net”。 如果不确定 VM 的主机名，请参考 Azure 门户上的 VM 仪表板。

    ![PuTTY 连接](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)
3. 使用创建虚拟机时指定的本地管理员凭据登录到虚拟机。 本示例使用本地管理员帐户“mahesh”。

    ![PuTTY 登录](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)

## <a name="install-required-packages-on-the-linux-virtual-machine"></a>在 Linux 虚拟机上安装所需的包
连接到虚拟机后，下一个任务是在虚拟机上安装加入域所需的包。 执行以下步骤：

1. **安装 realmd：**realmd 包用于加入域。 在 PuTTY 终端中键入以下命令：

    sudo yum install realmd

    ![安装 realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)

    几分钟后，realmd 包应已安装在虚拟机上。

    ![已安装 realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)
2. **安装 sssd：**realmd 包依赖于 sssd 来执行域加入操作。 在 PuTTY 终端中键入以下命令：

    sudo yum install sssd

    ![安装 sssd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)

    几分钟后，sssd 包应已安装在虚拟机上。

    ![已安装 realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)
3. **安装 kerberos：**在 PuTTY 终端中键入以下命令：

    sudo yum install krb5-workstation krb5-libs

    ![安装 kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)

    几分钟后，realmd 包应已安装在虚拟机上。

    ![已安装 Kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)

## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>将 Linux 虚拟机加入托管域
在 Linux 虚拟机上安装所需的包后，下一个任务是将虚拟机加入托管域。

1. 发现 AAD 域服务托管域。 在 PuTTY 终端中键入以下命令：

    sudo realm discover CONTOSO100.COM

    ![发现领域](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)

    如果**领域发现**无法找到托管域，请确保可从虚拟机连接到域（尝试执行 ping）。 另请确保虚拟机已确实部署到提供托管域的同一个虚拟网络。
2. 初始化 kerberos。 在 PuTTY 终端中键入以下命令。 请确保指定属于“AAD DC 管理员”组的用户。 只有这些用户可将计算机加入托管域。

    kinit bob@CONTOSO100.COM

    ![Kinit](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)

    确保以大写字母指定域名，否则 kinit 会失败。
3. 将计算机加入域。 在 PuTTY 终端中键入以下命令。 确保指定前一步骤中所指定的同一用户（“kinit”）。

    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'

    ![领域加入](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

计算机成功加入托管域后，应会显示一条消息（“已在领域中成功注册计算机”）。

## <a name="verify-domain-join"></a>验证域加入
可以快速验证计算机是否已成功加入托管域。 使用 SSH 和域用户帐户连接到刚刚加入域的 RHEL VM，然后检查用户帐户是否正确解析。

1. 在 PuTTY 终端中键入以下命令，使用 SSH 连接到刚刚加入域的 RHEL 虚拟机。 使用属于托管域的域帐户（在本例中为 'bob@CONTOSO100.COM'）。

    ssh -l bob@CONTOSO100.COM contoso-rhel.cloudapp.net
2. 在 PuTTY 终端中键入以下命令，查看是否已正确初始化主目录。

    pwd
3. 在 PuTTY 终端中键入以下命令，查看组成员身份是否正确解析。

    id

下面显示了这些命令的示例输出：

![验证域加入](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)

## <a name="troubleshooting-domain-join"></a>排查域加入问题
请参阅[排查域加入问题](active-directory-ds-admin-guide-join-windows-vm.md#troubleshooting-domain-join)一文。

## <a name="related-content"></a>相关内容
* [Azure AD 域服务 - 入门指南](active-directory-ds-getting-started.md)
* [将 Windows Server 虚拟机加入 Azure AD 域服务托管域](active-directory-ds-admin-guide-join-windows-vm.md)
* [如何登录到运行 Linux 的虚拟机](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* [安装 Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 - Windows 集成指南](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)

