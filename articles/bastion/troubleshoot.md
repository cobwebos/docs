---
title: 排查 Azure 堡垒问题 |Microsoft Docs
description: 在本文中，学习如何对 Azure Bastion 进行排除故障。
services: bastion
author: charwen
ms.service: bastion
ms.topic: troubleshooting
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: f3c142491363f30513877ae4368f291430aa3675
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85831924"
---
# <a name="troubleshoot-azure-bastion"></a>Azure Bastion 疑难解答

本文介绍如何对 Azure Bastion 进行排除故障。

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>无法在 AzureBastionSubnet 上创建网络安全组 (NSG)

**问：** 我尝试在 Azure Bastion 子网上创建 NSG 时，遇到以下错误：“网络安全组 <NSG name> 没有 Azure Bastion 子网 AzureBastionSubnet 必需的规则”。

**答:** 如果要创建 NSG 并将其应用到 AzureBastionSubnet，请确保已在 NSG 中添加以下规则。 如果未添加它们，则 NSG 创建/更新操作将失败。

1. 控制平面连接-从 GatewayManager 到443的入站
2. 诊断日志记录和其他内容-在443到 AzureCloud 的出站 (尚不支持此服务标记内的区域标记。 ) 
3. 目标 VM –3389和22到 VirtualNetwork 的出站

[快速启动模板](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg)中提供了 NSG 规则的示例以供参考。
有关详细信息，请参阅 [Azure 服务的 NSG 指南](bastion-nsg.md)。

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>无法将 SSH 密钥用于 Azure Bastion

**问：** 我在尝试浏览我的 SSH 密钥文件时，遇到以下错误：“SSH 私钥必须以 -----BEGIN RSA PRIVATE KEY----- 开头，以 -----END RSA PRIVATE KEY----- 结尾”。

**答:** Azure Bastion 目前仅支持 RSA SSH 密钥。 请确保你浏览的密钥文件是 SSH 的 RSA 私钥，且目标 VM 上预配了公钥。 

例如，你可使用以下命令来创建新的 RSA SSH 密钥：

ssh-keygen -t rsa -b 4096 -C "email@domain.com"

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

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>无法登录已加入域的 Windows 虚拟机

**问：** 我无法连接到已加入域的 Windows 虚拟机。

**答:** Azure Bastion 仅支持采用用户名和密码来登录已加入域的 VM。 在 Azure 门户中指定域凭据时，请使用 UPN (username@domain) 格式进行登录，而不是使用 domain\username 格式。 这可用于已加入域或已加入混合（已加入域且已加入 Azure AD）的虚拟机， 不可用于仅加入了 Azure AD 的虚拟机。

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>文件传输问题

**问：** Azure Bastion 是否支持文件传输？

**答:** 目前不支持文件传输。 我们正在努力添加该项支持。

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Azure 门户中出现黑屏

**问：** 我在尝试使用 Azure Bastion 进行连接时，Azure 门户中出现黑屏现象。

**答:** 如果 Web 浏览器与 Azure Bastion 之间出现网络连接问题（诸如客户端 Internet 防火墙可能正在阻止 WebSockets 流量之类的），或者 Azure Bastion 与目标 VM 之间出现网络连接问题，则会出现此情况。 大多数情况是 AzureBastionSubnet 或目标 VM 子网上应用了一个 NSG 来阻止你的虚拟机中的 RDP/SSH 流量。 请允许客户端 Internet 防火墙上的 WebSockets 流量，并检查目标 VM 子网上的 NSG。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Bastion 常见问题解答](bastion-faq.md)。
