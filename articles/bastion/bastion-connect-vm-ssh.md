---
title: 使用 Azure 堡垒连接到 Linux VM
description: 在本文中，了解如何使用 Azure 堡垒连接到 Linux 虚拟机。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 381f45a51002188c72174cff83c26b829912a0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596821"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>使用 SSH 连接到使用 Azure 堡垒的 Linux 虚拟机

本文介绍如何在 Azure 虚拟网络中安全地将 SSH 无缝地连接到 Linux VM。 可以直接从 Azure 门户连接到 VM。 使用 Azure 堡垒时，VM 不需要客户端、代理或其他软件。 有关 Azure 堡垒的详细信息，请参阅[概述](bastion-overview.md)。

您可以使用 Azure 堡垒使用 SSH 连接到 Linux 虚拟机。 您可以使用用户名/密码和 SSH 密钥进行身份验证。 您可以使用以下任一操作使用 SSH 密钥连接到 VM：

* 手动输入的私钥
* 包含私钥信息的文件

SSH 私钥必须采用以`"-----BEGIN RSA PRIVATE KEY-----"`开头和以`"-----END RSA PRIVATE KEY-----"`结尾的格式。

## <a name="before-you-begin"></a>开始之前

请确保为 VM 所在的虚拟网络设置了 Azure 堡垒主机。 有关详细信息，请参阅创建[Azure 堡垒主机](bastion-create-host-portal.md)。 在虚拟网络中预配和部署 Bastion 服务后，可以使用它连接到此虚拟网络中的任何 VM。 

当您使用 Bastion 进行连接时，它假定您正在使用 RDP 连接到 Windows VM，SSH 连接到 Linux VM。 有关连接到 Windows VM 的信息，请参阅[连接到 VM - Windows](bastion-connect-vm-rdp.md)。

### <a name="required-roles"></a>所需角色

需要使用以下角色进行连接：

* 虚拟机上的读者角色
* NIC 上的读者角色（使用虚拟机的专用 IP）
* Azure Bastion 资源上的读者角色

### <a name="ports"></a>端口

要通过 SSH 连接到 Linux VM，必须在 VM 上打开以下端口：

* 入站端口： SSH （22）

## <a name="connect-using-username-and-password"></a><a name="username"></a>连接：使用用户名和密码

1. 打开[Azure 门户](https://portal.azure.com)。 导航到要连接到的虚拟机，然后单击 **"连接**"并从下拉列表中选择 **"堡垒**"。

   ![连接](./media/bastion-connect-vm-ssh/connect.png)
1. 单击"堡垒"后，将显示一个侧栏，该边栏具有三个选项卡 - RDP、SSH 和堡垒。 如果为虚拟网络预配了堡垒，则默认情况下堡垒选项卡处于活动状态。 如果未为虚拟网络预配堡垒，请参阅[配置堡垒](bastion-create-host-portal.md)。

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. 向虚拟机输入 SSH 的用户名和密码。
1. 输入键后单击 **"连接**"按钮。

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>连接：手动输入私钥

1. 打开[Azure 门户](https://portal.azure.com)。 导航到要连接到的虚拟机，然后单击 **"连接**"并从下拉列表中选择 **"堡垒**"。

   ![连接](./media/bastion-connect-vm-ssh/connect.png)
1. 单击"堡垒"后，将显示一个侧栏，该边栏具有三个选项卡 - RDP、SSH 和堡垒。 如果为虚拟网络预配了堡垒，则默认情况下堡垒选项卡处于活动状态。 如果未为虚拟网络预配堡垒，请参阅[配置堡垒](bastion-create-host-portal.md)。

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. 输入用户名并选择**SSH 私钥**。
1. 将私钥输入文本区域**SSH 私钥**（或直接粘贴）。
1. 输入键后单击 **"连接**"按钮。

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>连接：使用私钥文件

1. 打开[Azure 门户](https://portal.azure.com)。 导航到要连接到的虚拟机，然后单击 **"连接**"并从下拉列表中选择 **"堡垒**"。

   ![连接](./media/bastion-connect-vm-ssh/connect.png)
1. 单击"堡垒"后，将显示一个侧栏，该边栏具有三个选项卡 - RDP、SSH 和堡垒。 如果为虚拟网络预配了堡垒，则默认情况下堡垒选项卡处于活动状态。 如果未为虚拟网络预配堡垒，请参阅[配置堡垒](bastion-create-host-portal.md)。

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. 输入用户名并从**本地文件中选择 SSH 私钥**。
1. 单击 **"浏览"** 按钮（本地文件中的文件夹图标）。
1. 浏览该文件，然后单击"**打开**"。
1. 单击"**连接**"以连接到 VM。 单击"连接"后，SSH 将直接在 Azure 门户中打开。 此连接通过 HTML5 使用堡垒服务上的端口 443 覆盖虚拟机的专用 IP。

## <a name="next-steps"></a>后续步骤

阅读[堡垒常见问题解答](bastion-faq.md)
