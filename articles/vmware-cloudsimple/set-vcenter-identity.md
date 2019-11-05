---
title: Azure VMware 解决方案（按 CloudSimple）-在私有云上设置 vCenter 标识源
description: 描述如何设置私有云 vCenter 以使用 VMware 管理员访问 vCenter Active Directory 进行身份验证
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9d2986acc47087c267193eee43136e030abcc422
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990318"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>设置要使用的 vCenter 标识源 Active Directory

## <a name="about-vmware-vcenter-identity-sources"></a>关于 VMware vCenter 标识源

VMware vCenter 支持用于身份验证的不同标识源，以便对访问 vCenter 的用户进行身份验证。  你的 CloudSimple 私有云 vCenter 可以设置为使用 Active Directory 进行身份验证，以便 VMware 管理员可以访问 vCenter。 安装完成后， **cloudowner**用户可以将标识源中的用户添加到 vCenter。  

可以通过以下任一方式设置 Active Directory 域和域控制器：

* Active Directory 在本地运行的域和域控制器
* Active Directory azure 订阅中作为虚拟机在 Azure 上运行的域和域控制器
* 新 Active Directory 在私有云中运行的域和域控制器
* Azure Active Directory 服务

本指南介绍了设置 Active Directory 域和域控制器的任务，这些任务在本地或作为订阅中的虚拟机运行。  如果要使用 Azure AD 作为标识源，请参阅[在 CloudSimple 私有云上使用 Azure AD 作为 vCenter 的标识提供者](azure-ad.md)，以获取有关设置标识源的详细说明。

在[添加标识源](#add-an-identity-source-on-vcenter)之前，临时[升级 vCenter 权限](escalate-private-cloud-privileges.md)。

> [!CAUTION]
> 新用户必须仅添加到*云所有者组*、*云全局-群集管理组*、云全局*存储管理*组、云全局*网络管理*组或*云-全局-VM 管理*组的用户组。  添加到*管理员*组的用户将被自动删除。  只有服务帐户才能添加到*Administrators*组。  


## <a name="identity-source-options"></a>标识源选项

* [作为单一登录标识源添加本地 Active Directory](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [在私有云上设置新 Active Directory](#set-up-new-active-directory-on-a-private-cloud)
* [在 Azure 上设置 Active Directory](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>作为单一登录标识源添加本地 Active Directory

若要将本地 Active Directory 设置为单一登录标识源，需要：

* 从本地数据中心到私有云的[站点到站点 VPN 连接](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。
* 向 vCenter 和平台服务控制器（PSC）添加本地 DNS 服务器 IP。

设置 Active Directory 域时，请使用下表中的信息。

| **选项** | **说明** |
|------------|-----------------|
| **名称** | 标识源的名称。 |
| **用户的基本 DN** | 用户的基本可分辨名称。 |
| **域名** | 域的 FDQN，例如 example.com。 不要在此文本框中提供 IP 地址。 |
| **域别名** | 域 NetBIOS 名称。 如果使用的是 SSPI 身份验证，请将 Active Directory 域的 NetBIOS 名称添加为标识源的别名。 |
| **组的基本 DN** | 组的基本可分辨名称。 |
| **主服务器 URL** | 域的主域控制器 LDAP 服务器。<br><br>使用  或 `ldaps://hostname:port`格式 `ldap://hostname:port`。 端口通常为389，适用于 LDAP 连接，636用于 LDAPS 连接。 对于 Active Directory 多域控制器部署，端口通常为3268，适用于 LDAP，3269用于 LDAPS。<br><br>在主或辅助 LDAP URL 中使用 `ldaps://` 时，需要为 Active Directory 服务器的 LDAPS 终结点建立信任的证书。 |
| **辅助服务器 URL** | 用于故障转移的辅助域控制器 LDAP 服务器的地址。 |
| **选择证书** | 如果要将 LDAPS 与 Active Directory LDAP 服务器或 OpenLDAP 服务器标识源结合使用，则在 "URL" 文本框中键入 `ldaps://` 后，将显示 "选择证书" 按钮。 不需要辅助 URL。 |
| **用户名** | 域中用户的 ID，这些用户和组的基本 DN 至少具有只读访问权限。 |
| **密码** | Username 指定的用户的密码。 |

当你具有上表中的信息时，可以将本地 Active Directory 添加为 vCenter 上的单一登录标识源。

> [!TIP]
> 你将在[VMware 文档页](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html)上找到有关单一登录标识源的详细信息。

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>在私有云上设置新 Active Directory

你可以在私有云上设置新的 Active Directory 域，并将其用作单一登录的标识源。  Active Directory 域可以是现有 Active Directory 林的一部分，也可以设置为独立的林。

### <a name="new-active-directory-forest-and-domain"></a>新建 Active Directory 林和域

若要设置新的 Active Directory 林和域，需要：

* 运行 Microsoft Windows Server 的一个或多个虚拟机用作新 Active Directory 林和域的域控制器。
* 一个或多个运行 DNS 服务的虚拟机以进行名称解析。

请参阅[安装新的 Windows Server 2012 Active Directory 林](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-)获取详细步骤。

> [!TIP]
> 为实现服务的高可用性，我们建议设置多个域控制器和 DNS 服务器。

设置 Active Directory 林和域后，可以[在 vCenter 上为新 Active Directory 添加标识源](#add-an-identity-source-on-vcenter)。

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>现有 Active Directory 林中的新 Active Directory 域

若要在现有 Active Directory 林中设置新的 Active Directory 域，需要：

* 到 Active Directory 林位置的站点到站点 VPN 连接。
* 用于解析现有 Active Directory 林的名称的 DNS 服务器。

有关详细步骤，请参阅[安装新的 Windows Server 2012 Active Directory 子域或树域](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-)。

设置 Active Directory 域后，可以[在 vCenter 上为新 Active Directory 添加标识源](#add-an-identity-source-on-vcenter)。

## <a name="set-up-active-directory-on-azure"></a>在 Azure 上设置 Active Directory

在 Azure 上运行 Active Directory 类似于在本地运行 Active Directory。  若要将在 Azure 上运行的 Active Directory 设置为 vCenter 上的单一登录标识源，vCenter 服务器和 PSC 必须与运行 Active Directory 服务的 Azure 虚拟网络建立网络连接。  可以使用[Azure 虚拟网络连接](azure-expressroute-connection.md)，从运行 Active Directory 服务的 azure 虚拟网络连接到 CloudSimple 私有云，从而建立此连接。

建立网络连接后，请按照[将本地 Active Directory 添加为单一登录标识源](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)中的步骤将其添加为标识源。  

## <a name="add-an-identity-source-on-vcenter"></a>在 vCenter 上添加标识源

1. [提升](escalate-private-cloud-privileges.md)私有云上的权限。

2. 登录到你的私有云的 vCenter。

3. 选择 "**家庭 > 管理**"。

    ![管理](media/OnPremAD01.png)

4. 选择 "**单一登录 > 配置"** 。

    ![单一登录](media/OnPremAD02.png)

5. 打开 "**标识源**" 选项卡，然后单击 " **+** " 添加新的标识源。

    ![标识源](media/OnPremAD03.png)

6. 选择**Active Directory 作为 LDAP 服务器**，然后单击 "**下一步**"。

    ![Active Directory](media/OnPremAD04.png)

7. 为你的环境指定标识源参数，然后单击 "**下一步**"。

    ![Active Directory](media/OnPremAD05.png)

8. 查看设置，然后单击 "**完成**"。
