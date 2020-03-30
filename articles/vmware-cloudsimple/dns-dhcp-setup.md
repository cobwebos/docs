---
title: Azure VMware 解决方案（按云简单 ） - 为私有云设置工作负载 DNS 和 DHCP
description: 描述如何为云简单私有云环境中运行的应用程序和工作负载设置 DNS 和 DHCP
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ef1266b783034cf18dc2b3ea4be5ebc01bc12c70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024681"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>在云简单私有云中设置 DNS 和 DHCP 应用程序和工作负载

在私有云环境中运行的应用程序和工作负载需要名称解析和 DHCP 服务才能进行查找和 IP 地址分配。  需要适当的 DHCP 和 DNS 基础结构来提供这些服务。  您可以配置虚拟机以在私有云环境中提供这些服务。  

## <a name="prerequisites"></a>先决条件

* 配置了 VLAN 的分布式端口组
* 将设置路由到本地或基于 Internet 的 DNS 服务器
* 虚拟机模板或 ISO 以创建虚拟机

## <a name="linux-based-dns-server-setup"></a>基于 Linux 的 DNS 服务器设置

Linux 提供了用于设置 DNS 服务器的各种软件包。  下面是[数字海洋的一个示例设置](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04)，其中包含设置开源 BIND DNS 服务器的说明。

## <a name="windows-based-setup"></a>基于 Windows 的设置

这些 Microsoft 主题描述了如何将 Windows 服务器设置为 DNS 服务器和 DHCP 服务器。

* [视窗服务器作为 DNS 服务器](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [视窗服务器为 DHCP 服务器](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
