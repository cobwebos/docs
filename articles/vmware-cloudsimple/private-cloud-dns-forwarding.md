---
title: Azure VMware 解决方案 - 从私有云转发到本地的 DNS
description: 描述如何使云简单私有云 DNS 服务器转发本地资源
author: sharaths-cs
ms.author: b-shsury
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: aa2af4302613aad23bfd78b4883bbb46c5e5ddbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961122"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>启用云简单私有云 DNS 服务器，将本地资源的 DNS 查找转发到 DNS 服务器

私有云 DNS 服务器可以将任何本地资源的 DNS 查找转发到您的 DNS 服务器。  启用查找允许私有云 vSphere 组件查找在本地环境中运行的任何服务，并使用完全限定的域名 （FQDN） 与其通信。

## <a name="scenarios"></a>方案 

通过转发本地 DNS 服务器的 DNS 查询，您可以针对以下方案使用私有云：

* 将私有云用作本地 VMware 解决方案的灾难恢复设置
* 将本地活动目录用作私有云 vSphere 的身份源
* 使用 HCX 将虚拟机从本地迁移到私有云

## <a name="before-you-begin"></a>开始之前

从私有云网络到本地网络，必须存在网络连接，以便 DNS 转发工作。  您可以使用：

* [使用 ExpressRoute 从本地连接到云简单](on-premises-connection.md)
* [设置站点到站点 VPN 网关](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway#set-up-a-site-to-site-vpn-gateway)

必须在此连接上打开防火墙端口，DNS 转发才能正常工作。  使用的端口是 TCP 端口 53 或 UDP 端口 53。

> [!NOTE]
> 如果使用站点到站点 VPN，则必须将本地 DNS 服务器子网添加为本地前缀的一部分。

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>请求 DNS 从私有云转发到本地

要启用 DNS 从私有云转发到本地，请提交[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)，并提供以下信息。

* 问题类型：**技术**
* 订阅：**部署云简单服务的订阅**
* 服务 **：VMware 解决方案（按云简单）**
* 问题类型：**咨询或如何...**
* 问题子类型：**需要使用 NW 的帮助**
* 在详细信息窗格中提供本地域的域名。
* 在详细信息窗格中提供本地 DNS 服务器的列表，其中查询将从私有云转发到该服务器。

## <a name="next-steps"></a>后续步骤

* [了解有关本地防火墙配置的更多信息](on-premises-firewall-configuration.md)
* [本地 DNS 服务器配置](on-premises-dns-setup.md)
