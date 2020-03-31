---
title: 排除 Azure 堡垒故障 |微软文档
description: 在本文中，了解如何对 Azure 堡垒进行故障排除。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: cherylmc
ms.openlocfilehash: de112ff441bb53a0b3bc7f4ffa4456f1c241682c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512946"
---
# <a name="troubleshoot-azure-bastion"></a>排除 Azure 堡垒故障

本文介绍如何排除 Azure 堡垒的故障。

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>无法在 Azure Bastion Subnet 上创建 NSG

**问：** 当我尝试在 Azure 堡垒子网上创建 NSG 时，我得到以下错误："*网络安全组<NSG name>没有 Azure 堡垒子网 Azure Bastion Subnet 的必要规则"。*

**答：** 如果创建 NSG 并将其应用于*AzureBastionSubnet，* 请确保在 NSG 中添加了以下规则。 如果不添加这些规则，NSG 创建/更新将失败。

1. 控制平面连接 – 从网关管理器 443 上进入
2. 诊断日志记录和其他 — 443 到 AzureCloud 上的出站（此服务标记中的区域标记尚不支持。
3. 目标 VM = 3389 和 22 到虚拟网络的出站

NSG 规则的示例可在[快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion)中参考。
有关详细信息，请参阅[Azure 堡垒的 NSG 指南](bastion-nsg.md)。

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>无法使用我的 SSH 密钥与 Azure 堡垒

**问：** 当我尝试浏览我的SSH密钥文件时，我得到以下错误 *：'SSH私钥必须从-----BEGIN RSA私钥开始-----以-----END RSA私钥-----"结尾*。

**答：** 此时，Azure 堡垒仅支持 RSA SSH 密钥。 请确保浏览为 SSH 的 RSA 私钥的密钥文件，在目标 VM 上预配公钥。 

例如，可以使用以下命令创建新的 RSA SSH 密钥：

**ssh-keygen -t rsa -b 4096 -C "email@domain.com**

输出：

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>无法登录到我的 Windows 域加入的虚拟机

**问：** 我无法连接到加入域的 Windows 虚拟机。

**答：** Azure Bastion 支持加入域的 VM 登录，仅支持基于用户名密码的域登录。 在 Azure 门户中指定域凭据时，请使用 UPNusername@domain（ ） 格式而不是*域用户名*格式登录。 对于域加入或混合联接（域加入和 Azure AD 联接）虚拟机，支持此功能。 Azure AD 加入虚拟机不支持它。

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>文件传输问题

**问：** Azure 堡垒是否支持文件传输？

**答：** 目前不支持文件传输。 我们正在努力增加支持。

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Azure 门户中的黑屏

**问：** 当我尝试使用 Azure 堡垒进行连接时，我在 Azure 门户中得到一个黑屏。

**答：** 当 Web 浏览器和 Azure 堡垒之间存在网络连接问题时（客户端 Internet 防火墙可能阻止 WebSocket 流量或类似流量）或 Azure 堡垒和目标 VM 之间，就会发生这种情况。 大多数案例包括应用于 AzureBastionSubnet 的 NSG，或在目标 VM 子网上应用的 NSG，该子网阻止虚拟网络中的 RDP/SSH 流量。 允许 WebSocket 在客户端 Internet 防火墙上进行流量，并检查目标 VM 子网上的 NSG。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[堡垒常见问题解答](bastion-faq.md)。