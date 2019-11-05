---
title: 使用 Azure 堡垒连接到 Linux VM |Microsoft Docs
description: 本文介绍如何使用 Azure 堡垒连接到 Linux 虚拟机。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: b88327ea0b5d2958cc1c86fa317415f2441af894
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494473"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>使用 Azure 堡垒通过 SSH 连接到 Linux 虚拟机

本文介绍如何在 Azure 虚拟网络中安全且无缝地连接到 Linux Vm。 可通过 Azure 门户直接连接到 VM。 在使用 Azure Bastion 时，VM 无需客户端、代理和其他软件。 有关 Azure 堡垒的详细信息，请参阅[概述](bastion-overview.md)。

可以使用 Azure 堡垒连接到使用 SSH 的 Linux 虚拟机。 你可以使用用户名/密码和 SSH 密钥进行身份验证。 你可以使用 SSH 密钥连接到 VM，方法是使用：

* 手动输入的私钥
* 包含私钥信息的文件

SSH 私钥的格式必须以 `"-----BEGIN RSA PRIVATE KEY-----"` 开头，并以 `"-----END RSA PRIVATE KEY-----"`结束。

## <a name="before-you-begin"></a>开始之前

请确保已为 VM 所在的虚拟网络设置了 Azure 堡垒主机。 有关详细信息，请参阅[创建 Azure 堡垒主机](bastion-create-host-portal.md)。 在虚拟网络中设置并部署堡垒服务后，可以使用它连接到此虚拟网络中的任何 VM。 使用堡垒进行连接时，它假设你使用 RDP 连接到 Windows VM，使用 SSH 连接到 Linux Vm。

需要使用以下角色进行连接：

* 虚拟机上的读者角色
* NIC 上的读者角色（使用虚拟机的专用 IP）
* Azure Bastion 资源上的读者角色

## <a name="username"></a>连接：使用用户名和密码

1.   打开 [Azure 门户](https://portal.azure.com)。 导航到要连接到的虚拟机，然后单击 "**连接**"。 使用 SSH 连接时，VM 应为 Linux 虚拟机。
1. 单击 "连接" 后，将显示一个侧栏，其中有三个选项卡-RDP、SSH 和堡垒。 如果已为虚拟网络预配了堡垒，则默认情况下，堡垒选项卡处于活动状态。 如果没有为虚拟网络预配堡垒，请参阅[配置堡垒](bastion-create-host-portal.md)。

   ![VM 连接](./media/bastion-connect-vm-ssh/bastion.png)
1. 输入用于 SSH 连接到虚拟机的用户名和密码。
1. 输入密钥后，单击 "**连接**" 按钮。

## <a name="privatekey"></a>连接：手动输入私钥

1. 打开 [Azure 门户](https://portal.azure.com)。 导航到要连接到的虚拟机，然后单击 "**连接**"。 使用 SSH 连接时，VM 应为 Linux 虚拟机。
1. 单击 "连接" 后，将显示一个侧栏，其中有三个选项卡-RDP、SSH 和堡垒。 如果已为虚拟网络预配了堡垒，则默认情况下，堡垒选项卡处于活动状态。 如果没有为虚拟网络预配堡垒，请参阅[配置堡垒](bastion-create-host-portal.md)。

   ![VM 连接](./media/bastion-connect-vm-ssh/bastion.png)
1. 输入用户名并选择 " **SSH 私钥**"。
1. 将私钥输入到文本区域**SSH 私钥**中（或直接粘贴）。
1. 输入密钥后，单击 "**连接**" 按钮。

## <a name="ssh"></a>连接：使用私钥文件

1. 打开 [Azure 门户](https://portal.azure.com)。 导航到要连接到的虚拟机，然后单击 "**连接**"。 使用 SSH 连接时，VM 应为 Linux 虚拟机。

   ![VM 连接](./media/bastion-connect-vm-ssh/connect.png)
1. 单击 "连接" 后，将显示一个侧栏，其中有三个选项卡-RDP、SSH 和堡垒。 如果已为虚拟网络预配了堡垒，则默认情况下，堡垒选项卡处于活动状态。 如果没有为虚拟网络预配堡垒，请参阅[配置堡垒](bastion-create-host-portal.md)。

   ![VM 连接](./media/bastion-connect-vm-ssh/bastion.png)
1. 输入用户名，并选择 "**本地文件中的 SSH 私钥**"。
1. 单击 "**浏览**" 按钮（本地文件中的文件夹图标）。
1. 浏览文件，然后单击 "**打开**"。
1. 单击 "**连接**" 以连接到 VM。 单击 "连接" 后，SSH 将直接在 Azure 门户中打开此虚拟机。 此连接通过虚拟机的专用 IP 上使用堡垒服务上端口443的 HTML5。

## <a name="next-steps"></a>后续步骤

阅读[堡垒常见问题解答](bastion-faq.md)