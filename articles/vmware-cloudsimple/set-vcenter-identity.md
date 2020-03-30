---
title: Azure VMware 解决方案（按云简单 ） - 在私有云上设置 vCenter 标识源
description: 描述如何设置私有云 vCenter 以使用活动目录进行身份验证，以便 VMware 管理员访问 vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5355e43ca6ac075e76a76ceb51be135cf4b62b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564017"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>设置 vCenter 标识源以使用活动目录

## <a name="about-vmware-vcenter-identity-sources"></a>关于 VMware vCenter 标识源

VMware vCenter 支持不同的标识源，用于对访问 vCenter 的用户进行身份验证。  云简单私有云 vCenter 可以设置为使用活动目录进行身份验证，以便 VMware 管理员访问 vCenter。 设置完成后，**云所有者**用户可以将用户从标识源添加到 vCenter。  

您可以通过以下任何方式设置活动目录域和域控制器：

* 在本地运行的活动目录域和域控制器
* 在 Azure 订阅中作为虚拟机在 Azure 上运行的活动目录域和域控制器
* 在私有云中运行的新活动目录域和域控制器
* Azure Active Directory 服务

本指南介绍了在订阅中设置活动目录域和在本地运行或作为虚拟机运行的任务。  如果要使用 Azure AD 作为标识源，请参阅[使用 Azure AD 作为云简单私有云上 vCenter 的标识提供程序](azure-ad.md)，以获取设置标识源的详细说明。

[在添加标识源](#add-an-identity-source-on-vcenter)之前，请暂时[升级 vCenter 权限](escalate-private-cloud-privileges.md)。

> [!CAUTION]
> 新用户只能添加到*云所有者组*、*云-全球群集-管理员组*、*云-全球-存储-管理员组*、*云-全球-网络-管理员组*或*云-全球-VM-管理员组*。  添加到*管理员*组的用户将自动删除。  只能将服务帐户添加到*管理员*组，并且不得使用服务帐户登录到 vSphere Web UI。   


## <a name="identity-source-options"></a>标识源选项

* [将本地活动目录添加为单个登录标识源](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [在私有云上设置新的活动目录](#set-up-new-active-directory-on-a-private-cloud)
* [在 Azure 上设置活动目录](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>将本地活动目录添加为单一登录标识源

要将本地活动目录设置为单一登录标识源，您需要：

* 从本地数据中心到私有云的[站点到站点 VPN 连接](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。
* 本地 DNS 服务器 IP 添加到 vCenter 和平台服务控制器 （PSC）。

设置活动目录域时，请使用下表中的信息。

| **选项** | **说明** |
|------------|-----------------|
| **名称** | 标识源的名称。 |
| **用户基本 DN** | 用户的基本可分辨名称。 |
| **域名** | 例如，域的 FQDN example.com。 请勿在此文本框中提供 IP 地址。 |
| **域别名** | 域 NetBIOS 名称。 如果使用 SSPI 身份验证，则将 Active Directory 域的 NetBIOS 名称添加为标识源的别名。 |
| **组的基本 DN** | 组的基本可分辨名称。 |
| **主服务器 URL** | 域的主域控制器 LDAP 服务器。<br><br>使用格式 `ldap://hostname:port` 或 `ldaps://hostname:port`。 对于 LDAP 连接，端口通常为 389，LDAPS 连接为 636。 对于 Active Directory 多域控制器部署，端口通常为 3268（用于 LDAP）和 3269（用于 LDAPS）。<br><br>当您 `ldaps://` 在主数据库或辅助 LDAP URL 中使用时，需要为活动目录服务器的 LDAPS 终结点建立信任的证书。 |
| **辅助服务器 URL** | 用于故障转移的辅助域控制器 LDAP 服务器的地址。 |
| **选择证书** | 如果要将 LDAPS 与活动目录 LDAP 服务器或 OpenLDAP 服务器标识源一起使用，则在 `ldaps://` URL 文本框中键入后将显示"选择证书"按钮。 不需要辅助 URL。 |
| **用户** | 域中用户 ID，该用户和组对基本 DN 具有最少的只读访问权限。 |
| **密码** | 由用户名指定的用户的密码。 |

在上表中具有信息时，可以将本地活动目录添加为 vCenter 上的单一登录标识源。

> [!TIP]
> 您可以在[VMware 文档页面上](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html)找到有关单一登录标识源的详细信息。

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>在私有云上设置新的活动目录

您可以在私有云上设置新的活动目录域，并将其用作单一登录的标识源。  活动目录域可以是现有活动目录林的一部分，也可以设置为独立林。

### <a name="new-active-directory-forest-and-domain"></a>新的活动目录林和域

要设置新的活动目录林和域，您需要：

* 运行 Microsoft Windows 服务器的一个或多个虚拟机用作新活动目录林和域的域控制器。
* 运行 DNS 服务的一个或多个虚拟机以进行名称解析。

有关详细步骤[，请参阅安装新的 Windows 服务器 2012 活动目录林](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-)。

> [!TIP]
> 对于高可用性服务，我们建议设置多个域控制器和 DNS 服务器。

设置活动目录林和域后，可以在 vCenter 上为新的活动目录[添加标识源](#add-an-identity-source-on-vcenter)。

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>现有活动目录林中的新活动目录域

要在现有活动目录林中设置新的活动目录域，您需要：

* 站点到站点 VPN 连接到您的活动目录林位置。
* DNS 服务器解析现有活动目录林的名称。

有关详细步骤[，请参阅安装新的 Windows 服务器 2012 活动目录子目录或树域](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-)。

设置活动目录域后，可以在 vCenter 上为新的活动目录[添加标识源](#add-an-identity-source-on-vcenter)。

## <a name="set-up-active-directory-on-azure"></a>在 Azure 上设置活动目录

在 Azure 上运行的活动目录类似于在本地运行的活动目录。  要将在 Azure 上运行的活动目录设置为 vCenter 上的单一登录标识源，vCenter 服务器和 PSC 必须具有与运行活动目录服务的 Azure 虚拟网络的网络连接。  您可以使用[Azure 虚拟网络连接建立此连接，使用](azure-expressroute-connection.md)来自 Azure 虚拟网络的 ExpressRoute，其中 Active 目录服务运行到云简单私有云。

建立网络连接后，按照[将本地活动目录中的步骤添加为单一登录标识源](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)，将其添加为标识源。  

## <a name="add-an-identity-source-on-vcenter"></a>在 vCenter 上添加标识源

1. [升级](escalate-private-cloud-privileges.md)私有云上的权限。

2. 登录到私有云的 vCenter。

3. 选择**家庭>管理**。

    ![管理](media/OnPremAD01.png)

4. 选择 **"配置>上的单一符号**。

    ![单一登录](media/OnPremAD02.png)

5. 打开 **"标识源**"选项卡，**+** 然后单击以添加新标识源。

    ![标识源](media/OnPremAD03.png)

6. 选择**活动目录作为 LDAP 服务器**，然后单击 **"下一步**"。

    ![Active Directory](media/OnPremAD04.png)

7. 指定环境的标识源参数，然后单击 **"下一步**"。

    ![Active Directory](media/OnPremAD05.png)

8. 查看设置并单击 **"完成**"。
