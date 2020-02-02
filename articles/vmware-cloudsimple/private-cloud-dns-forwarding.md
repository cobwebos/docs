---
title: Azure VMware 解决方案-从私有云到本地的 DNS 转发
description: 描述如何启用 CloudSimple 私有云 DNS 服务器来向前查找本地资源
author: sharaths-cs
ms.author: b-shsury
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: aa2af4302613aad23bfd78b4883bbb46c5e5ddbb
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961122"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>启用 CloudSimple 私有云 DNS 服务器以将本地资源的 DNS 查找转发到 DNS 服务器

私有云 DNS 服务器可以将任何本地资源的 DNS 查找转发到 DNS 服务器。  启用查找功能后，私有云 vSphere 组件便可查找在本地环境中运行的任何服务，并使用完全限定的域名（FQDN）与它们进行通信。

## <a name="scenarios"></a>方案 

转发本地 DNS 服务器的 DNS 查找允许你在以下情况下使用私有云：

* 使用私有云作为本地 VMware 解决方案的灾难恢复设置
* 使用本地 Active Directory 作为私有云的标识源 vSphere
* 使用 HCX 将虚拟机从本地迁移到私有云

## <a name="before-you-begin"></a>开始之前

必须将私有云网络中的网络连接提供给本地网络，才能进行 DNS 转发。  你可以使用以下内容设置网络连接：

* [使用 ExpressRoute 从本地连接到 CloudSimple](on-premises-connection.md)
* [设置站点到站点 VPN 网关](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway#set-up-a-site-to-site-vpn-gateway)

为了使 DNS 转发正常工作，必须在此连接上打开防火墙端口。  使用的端口为 TCP 端口53或 UDP 端口53。

> [!NOTE]
> 如果使用的是站点到站点 VPN，则必须将本地 DNS 服务器子网作为本地前缀的一部分添加。

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>请求从私有云到本地的 DNS 转发

若要允许从私有云到本地的 DNS 转发，请提交[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，并提供以下信息。

* 问题类型：**技术**
* 订阅：**部署 CloudSimple 服务的订阅**
* 服务： **VMware 解决方案（按 CloudSimple** ）
* 问题类型：**通知或如何实现 ...**
* 问题子类型：**需要有关 NW 的帮助**
* 在详细信息窗格中提供本地域的域名。
* 提供要在详细信息窗格中将查找从私有云转发到的本地 DNS 服务器的列表。

## <a name="next-steps"></a>后续步骤

* [详细了解本地防火墙配置](on-premises-firewall-configuration.md)
* [本地 DNS 服务器配置](on-premises-dns-setup.md)
