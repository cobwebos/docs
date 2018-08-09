---
title: 将 Windows Server VM 加入 Azure Active Directory 域服务 | Microsoft Docs
description: 使用 Azure 资源管理器模板将 Windows Server 虚拟机加入托管域。
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: 2b94496895c0ab06127b6c556b1240e3279e572f
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504407"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>使用资源管理器模板将 Windows Server 虚拟机加入托管域
本文介绍如何使用资源管理器模板将 Windows Server 虚拟机加入 Azure AD 域服务托管域。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>开始之前
若要执行本文中所列的任务，需要：
1. 一个有效的 **Azure 订阅**。
2. 一个 **Azure AD 目录** - 已与本地目录或仅限云的目录同步。
3. 必须为 Azure AD 目录启用 **Azure AD 域服务**。 如果未启用，请遵循[入门指南](active-directory-ds-getting-started.md)中所述的所有任务。
4. 请确保已将托管域的 IP 地址配置为虚拟网络的 DNS 服务器。 有关详细信息，请参阅[如何更新 Azure 虚拟网络的 DNS 设置](active-directory-ds-getting-started-dns.md)
5. 完成[将密码同步到 Azure AD 域服务托管域](active-directory-ds-getting-started-password-sync.md)所要执行的步骤。


## <a name="install-and-configure-required-tools"></a>安装并配置所需的工具
可以使用以下选项之一执行本文档中所述的步骤：
* **Azure PowerShell**：[安装和配置](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Azure 跨平台命令行接口**：[安装和配置](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>选项 1：预配新的 Windows Server VM 并将其加入托管域
**快速入门模板名称**：[201-vm-domain-join](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

若要部署 Windows Server 虚拟机并将其加入托管域，请执行以下步骤：
1. 导航到[快速入门模板](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)。
2. 单击 **“部署到 Azure”**。
3. 在“自定义部署”页中，提供预配虚拟机所需的信息。
4. 选择要在其中预配虚拟机的 **Azure 订阅**。 请选择在其中启用了 Azure AD 域服务的同一个 Azure 订阅。
5. 选择现有的**资源组**，或创建新的资源组。
6. 选择要在其中部署新虚拟机的**位置**。
7. 在“现有 VNET 名称”中，指定在其中部署了 Azure AD 域服务托管域的虚拟网络。
8. 在“现有子网名称”中，指定要在其中部署此虚拟机的虚拟网络中的子网。 请不要选择虚拟网络中的网关子网。 另外，请不要选择在其中部署了托管域的专用子网。
9. 在“DNS 标签前缀”中，指定所要预配的虚拟机的主机名。 例如“contoso-win”。
10. 为虚拟机选择适当的 **VM 大小**。
11. 在“要加入的域”中，指定托管域的 DNS 域名。
12. 在“域用户名”中，指定托管域上的、将 VM 加入该托管域时所要使用的用户帐户名称。
13. 在“域密码”中，指定“domainUsername”参数引用的域用户帐户的密码。
14. 可选：可以指定要将虚拟机添加到的自定义 OU 的 **OU 路径**。 如果未指定此参数的值，则会将虚拟机添加到托管域上的默认“AAD DC 计算机”OU。
15. 在“VM 管理员用户名”字段中，指定虚拟机的本地管理员帐户名称。
16. 在“VM 管理员密码”字段中，指定虚拟机的本地管理员密码。 为虚拟机提供强本地管理员密码，防范密码暴力破解攻击。
17. 单击“我同意上述条款和条件”。
18. 单击“购买”预配虚拟机。

> [!WARNING]
> **请小心处理密码。**
> 模板参数文件包含域帐户的密码，以及虚拟机的本地管理员密码。 请务必不要将此文件保留在文件共享或其他共享位置中。 我们建议完成部署虚拟机后，立即处置此文件。
>

部署成功完成后，新预配的 Windows 虚拟机随即会加入托管域。


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>选项 2：将现有的 Windows Server VM 加入托管域
**快速入门模板**：[201-vm-domain-join-existing](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

若要将现有的 Windows Server 虚拟机加入托管域，请执行以下步骤：
1. 导航到[快速入门模板](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)。
2. 单击 **“部署到 Azure”**。
3. 在“自定义部署”页中，提供预配虚拟机所需的信息。
4. 选择要在其中预配虚拟机的 **Azure 订阅**。 请选择在其中启用了 Azure AD 域服务的同一个 Azure 订阅。
5. 选择现有的**资源组**，或创建新的资源组。
6. 选择要在其中部署新虚拟机的**位置**。
7. 在“VM 列表”字段中，指定要加入托管域的现有虚拟机的名称。 使用逗号分隔每个 VM 名称。 例如，**contoso-web, contoso-api**。
8. 在“域加入用户名”中，指定托管域上的、将 VM 加入该托管域时所要使用的用户帐户名称。
9. 在“域加入用户密码”中，指定“domainUsername”参数引用的域用户帐户的密码。
10. 在“域 FQDN”中，指定托管域的 DNS 域名。
11. 可选：可以指定要将虚拟机添加到的自定义 OU 的 **OU 路径**。 如果未指定此参数的值，则会将虚拟机添加到托管域上的默认“AAD DC 计算机”OU。
12. 单击“我同意上述条款和条件”。
13. 单击“购买”预配虚拟机。

> [!WARNING]
> **请小心处理密码。**
> 模板参数文件包含域帐户的密码，以及虚拟机的本地管理员密码。 请务必不要将此文件保留在文件共享或其他共享位置中。 我们建议完成部署虚拟机后，立即处置此文件。
>

部署成功完成后，指定的 Windows 虚拟机随即会加入托管域。


## <a name="related-content"></a>相关内容
* [Azure PowerShell 概述](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.4.0)
* [Azure 快速入门模板 - 将新 VM 加入域](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Azure 快速入门模板 - 将现有 VM 加入域](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../azure-resource-manager/resource-group-template-deploy.md)
