---
title: Azure VMware 解决方案（AVS）-设置适用于 AVS 私有云的工作负荷 DNS 和 DHCP
description: 描述如何为你的 AVS 私有云环境中运行的应用程序和工作负荷设置 DNS 和 DHCP
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2a5cae868f2d8f3689f05dd9d466715ab2008a3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024681"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-avs-private-cloud"></a>在 AVS 私有云中设置 DNS 和 DHCP 应用程序和工作负荷

在 AVS 私有云环境中运行的应用程序和工作负荷需要进行名称解析，并使用 DHCP 服务进行查找和 IP 地址分配。 需要适当的 DHCP 和 DNS 基础结构才能提供这些服务。 你可以配置虚拟机以在你的 AVS 私有云环境中提供这些服务。 

## <a name="prerequisites"></a>必备组件

* 配置了 VLAN 的分布式端口组
* 将设置路由到本地或基于 Internet 的 DNS 服务器
* 用于创建虚拟机的虚拟机模板或 ISO

## <a name="linux-based-dns-server-setup"></a>基于 Linux 的 DNS 服务器设置

Linux 提供了用于设置 DNS 服务器的各种包。 下面是[DigitalOcean 的示例设置](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04)，说明了如何设置开源绑定 DNS 服务器。

## <a name="windows-based-setup"></a>基于 Windows 的安装

这些 Microsoft 主题介绍如何将 Windows server 设置为 DNS 服务器和 DHCP 服务器。

* [Windows Server 作为 DNS 服务器](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Windows Server 作为 DHCP 服务器](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
