---
title: 排查 Azure 堡垒问题 |Microsoft Docs
description: 本文介绍如何排查 Azure 堡垒问题。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: cherylmc
ms.openlocfilehash: de112ff441bb53a0b3bc7f4ffa4456f1c241682c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512946"
---
# <a name="troubleshoot-azure-bastion"></a>排查 Azure 堡垒问题

本文介绍了如何对 Azure 堡垒进行故障排除。

## <a name="nsg"></a>无法在 AzureBastionSubnet 上创建 NSG

**问：** 尝试在 Azure 堡垒子网中创建 NSG 时，出现以下错误： *"网络安全组 <NSG name> 没有 Azure 堡垒子网 AzureBastionSubnet 的必需规则"* 。

**答：** 如果创建 NSG 并将其应用于*AzureBastionSubnet*，请确保已在 NSG 中添加以下规则。 如果未添加这些规则，则 NSG 创建/更新将会失败。

1. 控制平面连接-从 GatewayManager 到443的入站
2. 诊断日志记录和其他-从443到 AzureCloud 的出站（目前不支持此服务标签内的区域标记。）
3. 目标 VM –3389和22到 VirtualNetwork 的出站

可以参考[快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion)中的 NSG 规则的示例。
有关详细信息，请参阅[Azure 堡垒的 NSG 指南](bastion-nsg.md)。

## <a name="sshkey"></a>无法将 SSH 密钥用于 Azure 堡垒

**问：** 当我尝试浏览 SSH 密钥文件时，出现以下错误： *"SSH 私钥必须以-----开始 RSA 私钥-----并以-----END RSA 私钥-----" 结尾*。

**答：** Azure 堡垒此时仅支持 RSA SSH 密钥。 请确保浏览密钥文件，该密钥文件是用于 SSH 的 RSA 私钥，并在目标 VM 上预配了公共密钥。 

例如，你可以使用以下命令创建新的 RSA SSH 密钥：

**ssh-keygen-t rsa-b 4096-C "email@domain.com"**

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

## <a name="domain"></a>无法登录到已加入 Windows 域的虚拟机

**问：** 我无法连接到已加入域的 Windows 虚拟机。

**答：** Azure 堡垒支持已加入域的 VM 登录以进行基于用户名密码的域登录。 在 Azure 门户中指定域凭据时，请使用 UPN （username@domain）格式，而不是使用 "*域 \ 用户名*" 格式登录。 这对于加入域或混合加入（加入域的虚拟机和 Azure AD 联接的虚拟机）都受支持。 不支持只加入 Azure AD 的虚拟机。

## <a name="filetransfer"></a>文件传输问题

**问：** Azure 堡垒是否支持文件传输？

**答：** 此时不支持文件传输。 我们正在努力添加支持。

## <a name="blackscreen"></a>Azure 门户中的黑色屏幕

**问：** 尝试使用 Azure 堡垒进行连接时，在 Azure 门户中会出现黑屏。

**答：** 当 web 浏览器与 Azure 堡垒之间出现网络连接问题（客户端 Internet 防火墙可能会阻止 Websocket 流量或类似），或在 Azure 堡垒与目标 VM 之间出现网络连接问题时，会发生这种情况。 大多数情况下，会将 NSG 应用到 AzureBastionSubnet 或目标 VM 子网上，阻止虚拟网络中的 RDP/SSH 流量。 允许客户端 internet 防火墙上的 Websocket 流量，并检查目标 VM 子网上的 Nsg。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[堡垒常见问题解答](bastion-faq.md)。